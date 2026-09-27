一、 資料定義語言 (DDL - Data Definition Language)
用於建立、修改與刪除資料庫結構（資料庫、資料表等）。

-- 1. 建立與切換資料庫
CREATE DATABASE HospitalLab;       -- 建立資料庫
GO
USE HospitalLab;                  -- 切換使用指定資料庫
GO

-- 2. 刪除資料庫 (注意：刪除後資料無法復原)
DROP DATABASE HospitalLab;

-- 3. 建立資料表 (含常用約束條件)
CREATE TABLE Patient (
PatientID INT IDENTITY(1,1) PRIMARY KEY, -- IDENTITY(起始值, 增量) 自動遞增主鍵
PatientName NVARCHAR(50) NOT NULL,       -- NVARCHAR支援中文，NOT NULL 代表必填
BirthDate DATE NOT NULL,
Gender CHAR(1) CHECK (Gender IN ('M', 'F')), -- CHECK 限制輸入值範圍
Phone VARCHAR(15) NULL,                  -- NULL 代表允許留空
CreatedAt DATETIME DEFAULT GETDATE()     -- DEFAULT 設定預設值為目前時間
);

-- 4. 修改資料表結構 (ALTER TABLE)
ALTER TABLE Patient ADD Address NVARCHAR(200);        -- 新增欄位
ALTER TABLE Patient ALTER COLUMN Phone VARCHAR(20);   -- 修改欄位型別或長度
ALTER TABLE Patient DROP COLUMN Address;              -- 刪除欄位

-- 5. 刪除資料表
DROP TABLE Patient;                                   -- 完全刪除資料表結構與資料
TRUNCATE TABLE Patient;                               -- 清空資料表中所有資料（比 DELETE 快且重置 IDENTITY）

------------------------------------------------------------------------------------------------------

二、 資料操作語言 (DML - Data Manipulation Language)
用於新增、查詢、更新與刪除資料表內的數據。

1. 新增資料 (INSERT)
-- 新增單筆資料
   INSERT INTO Department (DepartmentName)
   VALUES (N'內科');

-- 一次新增多筆資料 (N 代表 Unicode)
INSERT INTO Department (DepartmentName)
VALUES (N'外科'), (N'小兒科'), (N'心臟內科');
2. 更新資料 (UPDATE)
   -- 修改資料（務必加 WHERE，否則整張表的資料都會被改掉！）
   UPDATE Patient
   SET Phone = '0912345678', PatientName = N'張大華'
   WHERE PatientID = 1;
3. 刪除資料 (DELETE)
   -- 刪除特定條件的資料（務必加 WHERE）
   DELETE FROM Patient
   WHERE PatientID = 1;

------------------------------------------------------------------------------------------------------

三、 資料查詢語言 (DQL - Data Query Language)
核心查詢語法 SELECT，包含常用條件過濾、排序與聚合。

-- 1. 基本查詢與別名 (Alias)
SELECT
PatientID AS 病患編號,
PatientName AS 姓名
FROM Patient;

-- 2. 條件過濾 (WHERE)
SELECT * FROM Patient
WHERE Gender = 'M' AND BirthDate >= '1990-01-01';

-- 3. 模糊查詢 (LIKE) %代表任意多個字元，_代表單一字元
SELECT * FROM Patient WHERE PatientName LIKE N'林%';    -- 姓「林」的人
SELECT * FROM Patient WHERE PatientName LIKE N'%華%';   -- 名字裡有「華」的人

-- 4. 範圍與集合查詢 (IN / BETWEEN)
SELECT * FROM Patient WHERE PatientID IN (1, 3, 5);      -- ID 為 1, 3, 5 者
SELECT * FROM Patient WHERE BirthDate BETWEEN '1980-01-01' AND '2000-12-31';

-- 5. 排序結果 (ORDER BY) ASC: 升冪(預設) / DESC: 降冪
SELECT * FROM Patient
ORDER BY BirthDate DESC;

-- 6. 限制筆數 (TOP)
SELECT TOP 5 * FROM Patient ORDER BY CreatedAt DESC;  -- 取最新建立的前 5 筆

------------------------------------------------------------------------------------------------------

-- 1. 基本查詢與別名 (Alias)
SELECT
PatientID AS 病患編號,
PatientName AS 姓名
FROM Patient;

-- 2. 條件過濾 (WHERE)
SELECT * FROM Patient
WHERE Gender = 'M' AND BirthDate >= '1990-01-01';

-- 3. 模糊查詢 (LIKE) %代表任意多個字元，_代表單一字元
SELECT * FROM Patient WHERE PatientName LIKE N'林%';    -- 姓「林」的人
SELECT * FROM Patient WHERE PatientName LIKE N'%華%';   -- 名字裡有「華」的人

-- 4. 範圍與集合查詢 (IN / BETWEEN)
SELECT * FROM Patient WHERE PatientID IN (1, 3, 5);      -- ID 為 1, 3, 5 者
SELECT * FROM Patient WHERE BirthDate BETWEEN '1980-01-01' AND '2000-12-31';

-- 5. 排序結果 (ORDER BY) ASC: 升冪(預設) / DESC: 降冪
SELECT * FROM Patient
ORDER BY BirthDate DESC;

-- 6. 限制筆數 (TOP)
SELECT TOP 5 * FROM Patient ORDER BY CreatedAt DESC;  -- 取最新建立的前 5 筆

------------------------------------------------------------------------------------------------------

四、 資料聯結與分組 (JOIN & GROUP BY)
1. 多表聯結 (JOIN)
   -- 內聯結 (INNER JOIN)：只抓取兩邊都有對應到的資料
   SELECT
   V.VisitID,
   P.PatientName,
   D.DoctorName,
   V.VisitDate
   FROM Visit V
   INNER JOIN Patient P ON V.PatientID = P.PatientID
   INNER JOIN Doctor D ON V.DoctorID = D.DoctorID;

-- 左外聯結 (LEFT JOIN)：保留左表所有資料，右表沒匹配到的顯示 NULL
SELECT
D.DepartmentName,
Doc.DoctorName
FROM Department D
LEFT JOIN Doctor Doc ON D.DepartmentID = Doc.DepartmentID;
2. 分組與統計 (GROUP BY & HAVING)
   -- 統計各科別的醫師人數
   SELECT
   DepartmentID,
   COUNT(DoctorID) AS 醫師總數
   FROM Doctor
   GROUP BY DepartmentID;

-- 結合 HAVING（對分組後的統計結果做過濾）
SELECT
DepartmentID,
COUNT(DoctorID) AS 醫師總數
FROM Doctor
GROUP BY DepartmentID
HAVING COUNT(DoctorID) >= 2; -- 只顯示醫師人數 2 人（含）以上的科別

------------------------------------------------------------------------------------------------------

五、 SSMS 常用快捷鍵
F5 或 Ctrl + E：執行選取（或整頁）的 SQL 敘述。

Ctrl + K, Ctrl + C：快速將選取文字註釋（加上 --）。

Ctrl + K, Ctrl + U：快速將選取文字取消註釋。

Ctrl + R：顯示 / 隱藏下方查詢結果視窗。

Ctrl + N：開啟新的查詢視窗 (Query Window)。

_______________________________________________________________________________________________________

一、 文字與字串型別 (Text / String)

NVARCHAR(n)：變長度 Unicode 字串（1 個字吃 2 bytes），支援中文、日文、特殊符號。\n
VARCHAR(n)：變長度非 Unicode 字串（1 個英文字吃 1 byte），不支援中文字。\n
CHAR(n)：定長度字串。長度固定，不足補空白。\n
NVARCHAR(MAX)：超長文字內容（最大可存 2 GB）。\n

二、 數值型別 (Numeric)

INT：標準整數（最常用，4 bytes）。
BIGINT：大整數（8 bytes）。
TINYINT：極小整數（1 byte）。
BIT：布林值（1 bit）。

三、 日期與時間型別 (Date / Time)

DATE：純日期（3 bytes）。
TIME：純時間（3~5 bytes）。
DATETIME2：日期與時間（精確度高，優於舊的 DATETIME）。
