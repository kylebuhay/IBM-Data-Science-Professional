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
CREATE OR REPLACE PROCEDURE UPDATE_LEADERS_SCORE (
    in_School_ID INT,
    in_Leader_Score INT
)
BEGIN
    -- Body of Procedure Here
END;


- OUTPUT:
e3q1-script snippet

2.Inside your stored procedure, write a SQL statement to update the Leaders_Score field in the CHICAGO_PUBLIC_SCHOOLS table for the school identified by in_School_ID to the value in the in_Leader_Score parameter.

- SCRIPT:
UPDATE chicago_public_schools
SET Leaders_Score = in_Leader_Score
WHERE School_ID = in_School_ID;

- OUTPUT:
e3q2-script snippet

3. Inside your stored procedure, write a SQL IF statement to update the Leaders_Icon field in the CHICAGO_PUBLIC_SCHOOLS table for the school identified by in_School_ID using the following information.

- SCRIPT:
IF in_Leader_Score BETWEEN 80 AND 99 THEN
    UPDATE chicago_public_schools
    SET Leaders_Icon = 'Very strong'
    WHERE School_ID = in_School_ID;
ELSEIF in_Leader_Score BETWEEN 60 AND 79 THEN
    UPDATE chicago_public_schools
    SET Leaders_Icon = 'Strong'
    WHERE School_ID = in_School_ID;
ELSEIF in_Leader_Score BETWEEN 40 AND 59 THEN
    UPDATE chicago_public_schools
    SET Leaders_Icon = 'Average'
    WHERE School_ID = in_School_ID;
ELSEIF in_Leader_Score BETWEEN 20 AND 39 THEN
    UPDATE chicago_public_schools
    SET Leaders_Icon = 'Weak'
    WHERE School_ID = in_School_ID;
ELSE
    UPDATE chicago_public_schools
    SET Leaders_Icon = 'Very weak'
    WHERE School_ID = in_School_ID;
END IF;

- OUTPUT:
e3q3-if statement

4. Run your code to create the stored procedure. Write a query to call the stored procedure, passing a valid school ID and a leader score of 50, to check that the procedure works as expected.

- SCRIPT:
DELIMITER $$

DROP PROCEDURE IF EXISTS UPDATE_LEADERS_SCORE$$

CREATE PROCEDURE UPDATE_LEADERS_SCORE (
    in_School_ID INT,
    in_Leader_Score INT
)
BEGIN
    -- Update Leaders_Score
    UPDATE chicago_public_schools
    SET Leaders_Score = in_Leader_Score
    WHERE School_ID = in_School_ID;

    -- Update Leaders_Icon based on the score
    IF in_Leader_Score BETWEEN 80 AND 99 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Very strong'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 60 AND 79 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Strong'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 40 AND 59 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Average'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 20 AND 39 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Weak'
        WHERE School_ID = in_School_ID;
    ELSE
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Very weak'
        WHERE School_ID = in_School_ID;
    END IF;
END$$

DELIMITER ;

CALL UPDATE_LEADERS_SCORE(1, 50);

- OUTPUT:
e3q4-procedure

+-----------+

Exercise 4: Using Transactions

1. Update your stored procedure definition. Add a generic ELSE clause to the IF statement that rolls back the current work if the score did not fit any of the preceding categories.

- SCRIPT:
DELIMITER $$

CREATE OR REPLACE PROCEDURE UPDATE_LEADERS_SCORE (
    in_School_ID INT,
    in_Leader_Score INT
)
BEGIN
    DECLARE exit HANDLER FOR SQLEXCEPTION
        ROLLBACK;

    START TRANSACTION;

    -- Update Leaders_Score
    UPDATE chicago_public_schools
    SET Leaders_Score = in_Leader_Score
    WHERE School_ID = in_School_ID;

    -- Update Leaders_Icon based on the score
    IF in_Leader_Score BETWEEN 80 AND 99 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Very strong'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 60 AND 79 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Strong'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 40 AND 59 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Average'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 20 AND 39 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Weak'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 0 AND 19 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Very weak'
        WHERE School_ID = in_School_ID;
    ELSE
        ROLLBACK;
        LEAVE;
    END IF;

    COMMIT;
END$$

DELIMITER ;

- OUTPUT:
e4q1-else clause

2. Update your stored procedure definition again. Add a statement to commit the current unit of work at the end of the procedure.

- SCRIPT:
DELIMITER $$

CREATE OR REPLACE PROCEDURE UPDATE_LEADERS_SCORE (
    in_School_ID INT,
    in_Leader_Score INT
)
BEGIN
    DECLARE exit HANDLER FOR SQLEXCEPTION
        ROLLBACK;

    START TRANSACTION;

    -- Update Leaders_Score
    UPDATE chicago_public_schools
    SET Leaders_Score = in_Leader_Score
    WHERE School_ID = in_School_ID;

    -- Update Leaders_Icon based on the score
    IF in_Leader_Score BETWEEN 80 AND 99 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Very strong'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 60 AND 79 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Strong'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 40 AND 59 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Average'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 20 AND 39 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Weak'
        WHERE School_ID = in_School_ID;
    ELSEIF in_Leader_Score BETWEEN 0 AND 19 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Very weak'
        WHERE School_ID = in_School_ID;
    ELSE
        ROLLBACK;
        LEAVE;
    END IF;

    COMMIT;
END$$

DELIMITER ;

- OUTPUT:
e4q2-commit
