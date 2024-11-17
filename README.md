# DataCleaning-with-SQL

## Table of Content

- [📋Project Overview](#project-overview)
  
- [🗂Dataset Description](#dataset-description)
  
- [🛠Tools Used](#tools-used)
  
- [🔧Key Data Cleaning Steps](#key-data-cleaning-steps)
   - [Creating a Backup Table](#creating-a-backup-table)
   - [Identifying Duplicates Using Common Table Expression (CTE)](#identifying-duplicates-using-common-table-expression-(cte))
   - [Standardizing Data Formats](#standardizing-data-formats)
   - [Null Values and Blank Values](#null-values-and-blank-values)
   - [Removing unnecessary columns and Rows](#removing-uneccessary-columns-and-rows)
     
- [📌Limitations](#limitations)

- [📁Project Files](#project-files)




  

## 📋Project Overview

Understanding global layoffs is key to analyzing workforce trends and their impact on industries. This project leverages SQL to clean and prepare layoff data by resolving issues like duplicates, missing values, and inconsistent formatting. The goal is to create a structured dataset that enables meaningful insights and supports business strategies, policy-making, and further analysis.

## 🗂Dataset Description

The dataset provides detailed information about global layoffs, offering insights into workforce reductions across industries and locations. It includes the following columns:
- **Company**: Name of the company that experienced layoffs.
- **Location**: Specific city or region where the layoffs occurred.
- **Industry**: The sector or domain the company operates in (e.g., technology, Real Estate, Healthcare, finance).
- **Total_Laid_Off**: The total number of employees affected by layoffs.
- **Percentage_Laid_Off**: The proportion of the workforce impacted by the layoffs.
- **Date**: The date when the layoffs were announced or occurred.
- **Stage of Company**: The development stage of the company.
- **Country**: The country where the layoffs occurred.
- **Funds_Raised_Millions**: Total funds raised by the company, in millions of dollars.
  
## 🛠Tools Used

- MySQL, for data cleaning [Download here](https://www.mysql.com)
- Excel, for data import [Download here](https://www.microsoft.com)

## 🔧Key Data Cleaning Steps

1. Creating a Backup Table.
2. Identifying Duplicates Using Common Table Expression (CTE).
3. Standardizing Data Formats.
4. Null Values and Blank Values.
5. Removing Unnecessary Columns and Rows.

### 1. Creating a Backup Table

```SQL
		        SELECT *
                        FROM layoffs;

			CREATE TABLE layoffs_staging
			LIKE layoffs;
			
			SELECT *
			FROM layoffs_staging;
			
			INSERT layoffs_staging
			SELECT *
			FROM layoffs;
```


### 2. Identifying Duplicates Using Common Table Expression (CTE)

```SQL
WITH duplicate_cte AS
			(
			SELECT *,
			ROW_NUMBER() OVER(PARTITION BY company, location, industry,
            total_laid_off, pecentage_laid_off, `date`, stage,
            country, funds_raised_millions) AS row_num
			FROM layoffs_staging
			)
SELECT * FROM duplicate_cte
			WHERE row_num >1;
			
			CREATE TABLE `layoffs_staging2` (
	  `company` text,
	  `location` text,
	  `industry` text,
	  `total_laid_off` int DEFAULT NULL,
	  `percentage_laid_off` text,
	  `date` text,
	  `stage` text,
	  `country` text,
	  `funds_raised_millions` int DEFAULT NULL,
	`row_num` INT
	);

	SELECT *
	FROM layoffs_staging2;

	INSERT INTO layoffs_staging2
	SELECT *,
			ROW_NUMBER() OVER(PARTITION BY company, location, industry, total_laid_off,
			pecentage_laid_off, `date`,
			stage, country, funds_raised_millions) AS row_num
			FROM layoffs_staging;
			
			SELECT *
	FROM layoffs_staging2
	WHERE row_num >1;

			DELETE
	FROM layoffs_staging2
	WHERE row_num >1;
```

### 3. Standardizing Data Formats

```SQL
SELECT company, TRIM (company)
	 FROM layoffs_staging2;
	 
	 UPDATE layoffs_staging2 
	 SET company = TRIM (company);
	 
	  SELECT *
	  FROM layoff_staging2
	  WHERE industry LIKE 'crypto';
	  
	  UPDATE layoffs_staging2
	  SET industry = 'crypto'
	  WHERE industry LIKE 'crypto%';
	  
	  SELECT DISTINCT country, TRIM(TRAILING '.' FROM country)
	  FROM layoffs_staging2;
	  
	  UPDATE layoffs_staging2
	  SET country = TRIM(TRAILING '.' FROM country)
	  WHERE country LIKE 'United states%';

		SELECT `date`,
		STR_TO_DATE(`date`, '%m/%d/%Y')
		FROM layoffs_staging2;

		UPDATE layoffs_staging2
		SET `date`= STR_TO_DATE(`date`, '%m/%d/%Y');

		ALTER TABLE layoffs_staging2
		MODIFY COLUMN `date` DATE;
```

### 4. Null Values and Blank Values
```SQL
SELECT *
		FROM layoffs_staging2
		WHERE total_laid_off IS null
		AND percentage_laid_off IS null

		SELECT t1.industry, t2.industry
		FROM layoffs_staging2 t1
		JOIN layoffs_staging2 t2
		ON t1.company = t2.company
		WHERE t1.industry IS NULL
		AND t2.industry IS NOT NULL;
		
		UPDATE layoffs_staging2
		SET industry = NULL
		WHERE industry ='';
		
		UPDATE layoffs_staging2 t1
		JOIN layoffs_staging2 t2
		ON t1.company = t2.company
		SET t1.industry = t2.industry
		  WHERE t1.industry IS NULL
		  AND t2.industry IS NOT NULL;

			  SELECT * FROM layoffs_staging2
			  WHERE total_laid_off IS NULL
			  AND percentage_laid_off IS NULL;
			  
			  DELETE FROM layoffs_staging2
			  WHERE total_laid_off IS NULL
			  AND percentage_laid_off IS NULL;
```
### 5. Removing Unnecessary Columns and Rows

```SQL
ALTER TABLE layoffs_staging2
			  DROP COLUMN row_num;
			  
			  SELECT * FROM layoffs_staging2;
```

## 📌Limitations

  Some numeric fields, such as the "Total Laid Off," and "Percentage Laid Off" contained **null values** that could not be reliably imputed as to maintain data integrity. As a result, these fields were left with **null values** in the cleaned dataset.

## 📁Project Files

[Data Cleaning Final Output.xlsx](https://github.com/user-attachments/files/17792729/Data.Cleaning.Final.Output.xlsx)

😄

💻


   
  
