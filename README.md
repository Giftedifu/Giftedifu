--  1. Removing Duplicates
--  2. Standardize data
--  3. Nul values or blank Values
--  4. Remove columnS

--  Removing Duplicates. Create a replicate table so as not to alter the raw data.
CREATE TABLE layoff_replicate
like layoffs;

INSERT INTO layoff_replicate
SELECT *
From layoffs;

Select *,
ROW_NUMBER() over (PARTITION BY company, location,  industry,  total_laid_off, percentage_laid_off, 
'date', Stage, country, funds_raised_millions) AS row_num
From layoff_replicate;

WITH duplicate_cte AS
(Select *,
ROW_NUMBER() over (PARTITION BY company, location,  industry,  total_laid_off, percentage_laid_off, 
'date', Stage, country, funds_raised_millions) AS row_num
From layoff_replicate )
SELECT *
From Duplicate_cte
Where row_num > 1;

--  create another table then delete the Duplicate
CREATE TABLE `layoff_replicate2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
   `row_num` int
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci; 

SELECT *
From layoff_replicate2;

INSERT INTO layoff_replicate2
(Select *,
ROW_NUMBER() over (PARTITION BY company, location,  industry,  total_laid_off, percentage_laid_off, 
'date', Stage, country, funds_raised_millions) AS row_num
From layoff_replicate );

DELETE
FROM layoff_replicate2
WHERE Row_num > 1;



-- Standardizing data
SELECT Company, TRIM(company)
FROM layoff_replicate2;
    
UPDATE layoff_replicate2
SET  Company = TRIM(company);

SELECT Industry
FROM layoff_replicate2
order by 1;

UPDATE  layoff_replicate2
SET industry = 'Cryptocurrency'
WHERE industry  like 'cry%';

SELECT DISTINCT Industry
FROM layoff_replicate2
order by 1;

SELECT *
FROM layoff_replicate2
Where industry  like 'aero%';

SELECT DISTINCT country, trim(country)
FROM layoff_replicate2
order by 1;


SELECT DISTINCT country, trim(trailing '.' from country)
FROM layoff_replicate2
order by 1;

UPDATE layoff_replicate2
SET Country = trim(trailing '.' from country)
WHERE Country like '%united states';



