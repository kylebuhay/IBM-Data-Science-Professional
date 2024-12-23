Exercise 1: Using Joins

1. Write and execute a SQL query to list the school names, community names and average attendance for communities with a hardship index of 98.

- SCRIPT:
SELECT CPS.NAME_OF_SCHOOL, CD.COMMUNITY_AREA_NAME, CPS.AVERAGE_STUDENT_ATTENDANCE
FROM chicago_public_schools CPS
	JOIN chicago_socioeconomic_data CD
ON CPS.COMMUNITY_AREA_NUMBER = CD.COMMUNITY_AREA_NUMBER
WHERE CD.HARDSHIP_INDEX = 98;

- OUTPUT: 
e1q1-hardship index.png

2. Write and execute a SQL query to list all crimes that took place at a school. Include case number, crime type and community name.

- SCRIPT:
SELECT cc.CASE_NUMBER, cc.PRIMARY_TYPE, csd.COMMUNITY_AREA_NAME, cc.LOCATION_DESCRIPTION
FROM chicago_crime cc
	LEFT JOIN chicago_socioeconomic_data csd ON cc.COMMUNITY_AREA_NUMBER = csd.COMMUNITY_AREA_NUMBER
WHERE cc.LOCATION_DESCRIPTION LIKE '%SCHOOL%';

- OUTPUT:
e1q2-school crimes.png

+-----------+

Exercise 2: Creating a View

1. Write and execute a SQL statement to create a view showing the columns listed in the following table, with new column names as shown in the second column.

- SCRIPT:
CREATE VIEW PRIVATE_VIEW AS
SELECT 
    NAME_OF_SCHOOL AS School_Name,
    Safety_Icon AS Safety_Rating,
    Family_Involvement_Icon AS Family_Rating,
    Environment_Icon AS Environment_Rating,
    Instruction_Icon AS Instruction_Rating,
    Leaders_Icon AS Leaders_Rating,
    Teachers_Icon AS Teachers_Rating
FROM chicago_public_schools;

SELECT * FROM PRIVATE_VIEW;

- OUTPUT:
e2q1-chicago_public_schools view

+-----------+

Exercise 3: Creating a Stored Procedure

1. Write the structure of a query to create or replace a stored procedure called UPDATE_LEADERS_SCORE that takes a in_School_ID parameter as an integer and a in_Leader_Score parameter as an integer.

- SCRIPT:
CREATE OR REPLACE PROCEDURE
UPDATE_LEADERS_SCORE (IN in School_ID INTEGER, IN in Leader_Score INTEGER)
LANGUAGE SOL
BEGIN
END

- OUTPUT:
e3q1-sql query

2.Inside your stored procedure, write a SQL statement to update the Leaders_Score field in the CHICAGO_PUBLIC_SCHOOLS table for the school identified by in_School_ID to the value in the in_Leader_Score parameter.

- SCRIPT:
CREATE OR REPLACE PROCEDURE
UPDATE_LEADERS_SCORE (IN in_School_ID INTEGER, IN in_Leader_Score INTEGER)
LANGUAGE SOL
BEGIN
UPDATE CHICAGO PUBLIC SCHOOLS
SET "Leaders Score" = in Leader Score
WHERE "School_ID" = in_School_ID;
END

- OUTPUT:
e3q2-sql query

3. Inside your stored procedure, write a SQL IF statement to update the Leaders_Icon field in the CHICAGO_PUBLIC_SCHOOLS table for the school identified by in_School_ID using the following information.

- SCRIPT:


- OUTPUT:
