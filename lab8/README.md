# Laboratory Work No.8


### Objectives

#### Views and table-expressions

### Tasks

1. Create 2 views based on previously created querries (in lab4). The first view should be created in the querry editor, the second in View Designer mode:

  __SQL Querry:__
  
  ```sql
CREATE VIEW view1 AS
  SELECT Id_Student, Nume_Student, Prenume_Student
  FROM studenti.studenti
  WHERE Nume_Student LIKE '%u'
GO
SELECT * FROM view1 
  ```
  __View Designer:__
  ![task](/lab8/1.PNG)


2. Write an example of Insert, Update, Delete statements on the created view:

    __SQL Querry:__
  
  ```sql
INSERT INTO view1
VALUES (99, 'Bostanu', 'Asd')
GO

UPDATE view1
SET Nume_Student = 'Nicolescu' WHERE Nume_Student = 'Brasovianu' AND Prenume_Student = 'Teodora'
GO

DELETE FROM view1
WHERE Nume_Student = 'Bostanu'
GO
  ```

3. Write the necessary SQL instructions which would modify the views so they can't be modified or deleted if the _WHERE_ cause isn't satisfied:

  __SQL Querry:__

  ```sql
ALTER VIEW view1
WITH SCHEMABINDING
AS
  SELECT Id_Student, Nume_Student, Prenume_Student
  FROM studenti.studenti
  WHERE Nume_Student LIKE '%u'
WITH CHECK OPTION
  ```
  
4. Test the previous instructions:

  __SQL Querry:__
  
  ```sql
ALTER TABLE studenti.studenti DROP COLUMN Prenume_Student
  ```
  ![task](/lab8/4.PNG)
  
5. Write 2 querries from the 4th lab in __CTE__:

  ```sql
WITH cte1 AS (
	SELECT Id_Student, Nume_Student, Prenume_Student
	FROM studenti.studenti
  	WHERE Nume_Student LIKE '%u'
  )
SELECT * FROM cte1;

WITH cte2 AS (
	SELECT Nume_Student, Prenume_Student, count(studenti_reusita.Id_Student) AS nrOfMarks
	FROM studenti.studenti LEFT JOIN studenti.studenti_reusita 
	ON studenti.Id_Student = studenti_reusita.Id_Student
	GROUP BY studenti.Id_Student, Nume_Student, Prenume_Student
)
SELECT * FROM cte2
  ```

6. Write a recursive table-expression to traverse in depth a graph (the path 3->0 is highlighted):

  __SQL Querry:__

  ```sql
DECLARE @G TABLE
(
ID int,
ParentID int
)

INSERT @G
SELECT 3, NULL UNION ALL
SELECT 4, NULL UNION ALL
SELECT 5, NULL UNION ALL
SELECT 2, 3 UNION ALL
SELECT 2, 4 UNION ALL
SELECT 1, 2 UNION ALL
SELECT 0, 1 UNION ALL
SELECT 0, 5;

WITH graph AS
(
SELECT *, 0 AS Generation
FROM @G
WHERE ParentID IS NULL
UNION ALL
SELECT subGrapg.*, Generation + 1
FROM @G AS subGrapg
INNER JOIN graph
ON subGrapg.ParentID = graph.ID
)
SELECT DISTINCT * FROM graph ORDER BY Generation
  ```
  
   __Output:__
  ![task](/lab8/6.PNG)
  ![task](/lab8/61.png)
