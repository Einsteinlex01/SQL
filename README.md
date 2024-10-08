# MBA ADMISSION ANALYSIS 2025

## AIMS AND OBJECTIVES OF THE PROJECT
### The Admission Officer wants to gain more insight on the data in order to keep track of correct analysis base on the key roles;
#### (a). Assign a funding fee of $50000 to all the sponsored and $0 to notsponsored candidate.
#### (b). sum the funding fee for all sponsored candidates.
#### (c). sum the funding fee of all sponsored candidates for male and female
#### (d). Assign a tuition fee of 75000 for notsponsored candidate, $(75000 - funding_fee) for sponsored candidates and $0 for nonadmitted candidate.
#### (e). Group the funding fee of all the sponsoring candidate by gender and major.

#### Base on the admission process, the purpose of this whole analysis is to ensure the growth and success of the MBA admission program for 2025, it is crucial to adopt a multifaceted approach. By enhancing marketing strategies, diversifying program offerings, streamlining application processes, promoting inclusivity, and providing robust career support, we can attract a vibrant and diverse cohort of students. Implementing these strategies will position the program as a leader in business education and meet the needs of future business leaders.

### Tools
- SQL (MySqlWorkbench) For data cleaning and analysis
- Excel for Data analysis and visualization [Download Here](https://www.microsoft.com)
- Analytical and problem solving skill.

### The data provided was so dirty, so some query was written to do "DATA CLEANING" and "DATA ANALYSIS" as the concluding part.

##The SQL code is written below and the comments makes the Database Administrators understand the code

```sql
-- <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<MBA ADMISSION DATA ANALYSIS>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

-- The project is base only on data cleaning and data analysis(conclusion)

-- The aim of the project 
-- Data wrangling
-- Standardizing data in the database 
-- Feature engineering 
-- Conclusion this involves providing answers to the questions ask by the admission officer

-- <<<<<<<Data cleaning>>>>>>>>>>

-- create a database named MBA_ADMISSION_DB

CREATE DATABASE MBA_ADMISSION_DB;

-- import table mba and select all the data from the table in the database 

SELECT 
	*
FROM 
	mba;
/* Notice that the datatype assign to each column in the table is not allign to the data in the table 
so a well structured modification will be done to keep the alignment*/

-- 1/2.
/*data wrangling: adjust by modifying the column datatype and the standardization of data seem
to be okay so no standardization is needed to be done*/

alter table mba
MODIFY COLUMN gender varchar(7);

alter table mba
modify column international varchar(5);

alter table mba
MODIFY COLUMN gpa float(3,2);

alter table mba
MODIFY COLUMN Major varchar(16);

alter table mba
MODIFY COLUMN race varchar(9);

alter table mba
MODIFY COLUMN gmat Float(4,1);

alter table mba
MODIFY COLUMN work_exp Float(3,1);

alter table mba
MODIFY COLUMN work_industry varchar(30);

alter table mba
MODIFY COLUMN admission varchar(12);

-- 3. Feature engineering

-- Removing duplicate by adding a column(row_num) to a table 

select 
	*,
    row_number() over(partition by application_id,gender,international,gpa,major,race,gmat,work_exp) as row_num
from 
	mba;

-- this drops the existing table from the database
drop table if exists mba2;

-- create a table mba2 to serves as a backup for the existing table mba
CREATE TABLE `mba2` (
  `application_id` int DEFAULT NULL,
  `gender` varchar(7) DEFAULT NULL,
  `international` varchar(5) DEFAULT NULL,
  `gpa` float(3,2) DEFAULT NULL,
  `Major` varchar(16) DEFAULT NULL,
  `race` varchar(9) DEFAULT NULL,
  `gmat` float(4,1) DEFAULT NULL,
  `work_exp` float(3,1) DEFAULT NULL,
  `work_industry` varchar(30) DEFAULT NULL,
  `admission` varchar(12) DEFAULT NULL,
  `row_num` int not null,
   `admission_finals` varchar(14) default null,
  `scholarship_status` Varchar(15) default null
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

-- select all from mba2 table 
select 
	* 
from mba2;

-- insert all into mba2 table from mba table

insert into mba2
select 
	*,
	 row_number() over(partition by application_id,gender,international,gpa,major,race,gmat,work_exp) as row_num
from 
	mba;
    
    
-- Remove duplicate by adding an extra column to the table (row_num) and delete where row_num is greater than 1
select 
	count(*)
from 
	mba2
where row_num >1;

delete 
from mba3
where row_num > 1;

-- checking for blank space in the admission column where gender is male and female

select
	sum(case when admission = "" then 1 else 0 end) as sum_of_unadmmited 
from
	mba3
where 
	gender = "Male";
    
select
	sum(case when admission = "" then 1 else 0 end) as sum_unadmitted 
from
	mba2
where 
	gender = "Female";

select 
	case 
		when admission = "" then "notadmitted" 
        when admission = "admit" then "admit" 
        else "waitlist"
	end as admission
from 
	mba2;
    
-- selection of application_id with respect to sorted admission but not final admission
select 
	A.*
FROM
		(select 
			application_id,
			case 
				when admission = "" then "notadmitted" 
				when admission = "admit" then "admit" 
				else "waitlist"
			end as admission
		from 
			mba2) as A;
            

select 
	B.*
from
	(select 
		case
			when gpa > "2.5" and gmat > "600" then "admitted"
		else "notadmitted" end as admission_final
        
		from
			mba2) as B;
            
select 
	C.*
from
	(select 
		case 
			when gpa >= "3.5" and gmat >= "750" then "sponsored" else "notsponsored"
            end as scholarship_status
	from
				mba2) as C;
                
 
select 
	*,
	case
			when gpa >= "2.5" and gmat > "600" then "admitted"
		else "notadmitted" end as admission_final,
	case 
			when gpa >= "3.5" and gmat >= "750" then "sponsored" else "notsponsored"
            end as scholarship_status,
	row_number() over(partition by application_id,gender,international,gpa,major,race,gmat,work_exp) as row_num
from 
	mba;
    

    
insert into mba2

select 
	*,
    row_number() over(partition by application_id,gender,international,gpa,major,race,gmat,work_exp) as row_num,
	case
			when gpa >= 2.5 and gmat > 600 then "admitted"
		else "notadmitted" end as admission_final,
	case 
			when gpa >= 3.5 and gmat >= 750 then "sponsored" else "notsponsored"
            end as scholarship_status
from 
	mba;

-- trim column in the table (gender,international,major,race,work_industry and admission) to remove excess space
select  
	*
from 
	mba2;
    
select 
	gender,
    trim(gender)
from 
	mba3;
    
update mba2
set gender = trim(gender);

select 
	international,
    trim(international)
from 
	mba2;
    
update mba2
set international = trim(international);

select 
	Major,
    trim(Major)
from 
	mba2;
    
update mba3
set Major = trim(Major);

select 
	Race,
    trim(Race)
from 
	mba2;
    
update mba2
set Race = trim(Race);

select 
	work_industry,
    trim(work_industry)
from 
	mba2;
    
update mba2
set work_industry = trim(work_industry);
    
select 
	Admission,
    trim(Admission)
from 
	mba2;
    
update mba2
set Admission = trim(Admission);

-- checking for null values, blank space and sorting it out

select
	COUNT(gender)
from
	mba2
where gender is null;


select
	COUNT(international)
from
	mba2
where international is null;
select 
	count(Major)
from 
	mba2
where Major is null;

select 
	count(admission)
from
	mba 
where admission = "";

UPDATE mba2
set race = null 
where race = "";

select  
	*
from 
	mba3;
-- drop all the unnecessary column and added column 

Alter table mba2
drop column admission;

ALter table mba2
drop column row_num;

-- trim all the added column
select 
	Admission_finals,
    trim(Admission_finals)
from 
	mba2;
    
update mba2
set Admission_finals = trim(Admission_finals);

select 
	scholarship_status,
    trim(scholarship_status)
from 
	mba2;
    
update mba2
set scholarship_status = trim(scholarship_status);

-- 4. conclusion (this involves data analysis and answering question as asked by the admission officers)

-- (a). Assign a funding fee of $50000 to all the sponsored and $0 to notsponsored candidate

ALter table mba2
add funding_fee int;

SELECT 
	*
FROM 
	MBA2;
    
-- updating the table for $50000
update mba2
set funding_fee = 50000
where scholarship_status = "sponsored";

-- select all from table where funding fee is 50000
SELECT 
	*
FROM 
	MBA2
where funding_fee = 50000;
    
-- updating the table for $0    
update mba2
set funding_fee = 0
where funding_fee is null;

-- creating a temporary table to select records of all sponsored candidate
with mba_cte as(
	select 
		*
	from 
		mba2
	where scholarship_status = "sponsored")

select * from mba_cte;

-- (b). sum the funding fee for all sponsored candidates
select 
	sum(funding_fee)
from
	mba2
where funding_fee = 50000;

-- (c). sum the funding fee of all sponsored candidates for male and female
select 
	gender,
    sum(funding_fee)
from 
	mba2 
where gender = "female" and funding_fee = 50000;

select 
	gender,
    sum(funding_fee)
from 
	mba2 
where gender = "male" and funding_fee = 50000;

-- (d). Assign a tuition fee of $75000 for notsponsored candidate, $(75000 - funding_fee) for sponsored candidates and $0 for nonadmitted candidate
ALTER TABLE MBA2
add tuition_fee int;

select 
		(case
			when admission_finals = "admitted" and scholarship_status = "notsponsored" then 75000
			when admission_finals = "admitted" and scholarship_status = "sponsored" then (75000 - funding_fee)
            else 0 end) as tuition_fee
	from
		mba2;
        
select 
		(case
			when admission_finals = "admitted" and scholarship_status = "notsponsored" then 75000
			when admission_finals = "admitted" and scholarship_status = "sponsored" then 75000 - funding_fee
            else 0 end) as tuition_fee,
        (case
			when admission_finals = "admitted" and scholarship_status = "sponsored" then 75000 - funding_fee
            else 0 end) as tuition_fee2    
		
	from
		mba2;
        
update mba2
	set tuition_fee = 75000
	where admission_finals = "admitted" and scholarship_status = "notsponsored";
    
update mba2
	set tuition_fee = 75000 - funding_fee
    where admission_finals = "admitted" and scholarship_status = "sponsored";
    
update mba2
	set tuition_fee = 0
	where admission_finals = "notadmitted" and scholarship_status = "notsponsored";
        
SELECT 
	*
FROM 
	MBA2;
    
-- (e). Group the funding fee of all the sponsoring candidate by gender snd major
-- drop any procedure if exists 

drop PROCEDURE IF EXISTS p_grouping_funding_fee;

DELIMITER $$
CREATE PROCEDURE p_grouping_funding_fee(in p_gender varchar(7), in p_major varchar(15))
begin
select 
	gender,
    major,
    sum(funding_fee)
from
	mba2
where 
	gender = p_gender and major = p_major
    group by gender, major, funding_fee;
end$$
DELIMITER ;

-- uncomment this below statement to insert the p_gender and p_major of choice
-- call MBA_ADMISSION_DB.p_grouping_funding_fee('p_gender', 'p_major');

##### These are the steps follows for data cleaning and visualization;
```

### Data cleaning/preparation

1. Data wrangling
2. Standardizing data in the database 
3. Feature engineering

Due to followed process a clean and analysable table is obtained as shown below (SAMPLE DATA)

![SAMPLE DATA](https://github.com/user-attachments/assets/f3aed97a-44f0-4a76-8fdb-21891a1fbccf)

Base on the above observation, Null student shows international students which should'nt be neglected base on the instruction given by admission officer

4. conclusion (DATA ANALYSIS IN SQL)
   NOTE: This gives the information about the questions asked by the admission officer

- sum the funding fee for all sponsored candidates
  
 ![Total funding](https://github.com/user-attachments/assets/86ae38b5-469d-4fbe-ab10-f64121b972ac)


- sum the funding fee of all sponsored candidates for male and female

For Male
Code:
```sql
select 
	gender,
    sum(funding_fee)
from 
	mba2 
where gender = "male" and funding_fee = 50000;
```

Result:
 ![Total F MALE](https://github.com/user-attachments/assets/509c2be2-0257-449f-a83f-43d574adc6da)

For Female
Code:
```sql
select 
	gender,
    sum(funding_fee)
from 
	mba2 
where gender = "female" and funding_fee = 50000;
```

Result:
 ![Total F female](https://github.com/user-attachments/assets/7da510a9-0ba1-467e-bbaf-7fd85b207ea9)

- Group the funding fee of all the sponsoring candidate by gender snd major
  drop any procedure if exists
Code:
```sql
drop PROCEDURE IF EXISTS p_grouping_funding_fee;

DELIMITER $$
CREATE PROCEDURE p_grouping_funding_fee(in p_gender varchar(7), in p_major varchar(15))
begin
select 
	gender,
    major,
    sum(funding_fee)
from
	mba2
where 
	gender = p_gender and major = p_major
    group by gender, major, funding_fee;
end$$
DELIMITER ;

-- uncomment this below statement to insert the p_gender and p_major of choice
-- call MBA_ADMISSION_DB.p_grouping_funding_fee('p_gender', 'p_major');
```
The technique used in this perticular section is stored procedure and this is what it brings after execution

Result:
 ![Final](https://github.com/user-attachments/assets/1d1af63a-a1a3-4142-a032-32f9c3aec136)

it sums the funding fee base on the major of each gender.

  
  
