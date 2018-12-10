# Laboratory Work No.9


### Objectives

#### Procedures and Functions

### Tasks

1. Create 2 procedures based on previously created querries (in lab4):

  __SQL Querry:__
  
  ```sql
DROP PROCEDURE IF EXISTS proc11
GO
CREATE PROCEDURE proc1
	@pattern VARCHAR(10) = '%u'
AS
	select * 
	from studenti.studenti 
	where Nume_Student like @pattern
GO
EXECUTE proc11

DROP PROCEDURE IF EXISTS proc12;
GO
CREATE PROCEDURE proc12
AS
	select Nume_Student, Prenume_Student, count(studenti.studenti_reusita.Id_Student) as nrOfMarks
	from studenti.studenti left join studenti.studenti_reusita 
	on studenti.Id_Student = studenti_reusita.Id_Student
	group by studenti.Id_Student, Nume_Student, Prenume_Student
GO
EXECUTE proc12
  ```

2. Create a procedure that doesn't have an input parameter, but has an output parameter. The ouput parameter is the number of students that didn't pass at least one exam:

    __SQL Querry:__
  
  ```sql
DROP PROCEDURE IF EXISTS proc2
GO
CREATE PROCEDURE proc2
	@nr INT OUTPUT
AS
	SET @nr = (SELECT COUNT(DISTINCT Id_Student)
				FROM studenti.studenti_reusita
				WHERE Nota < 5 OR Nota is NULL)
GO

DECLARE @outNr INT
EXECUTE proc2 @outNr OUTPUT

PRINT CAST(@outNr AS VARCHAR(5))
  ```
  
  __Output:__   
  56


3. Create a procedure that would insert a new student into the database:

  __SQL Querry:__

  ```sql
DROP PROCEDURE IF EXISTS newStudent
GO

CREATE PROCEDURE newStudent
	@id INT,
	@name VARCHAR(50),
	@surname VARCHAR(50),
	@birthday DATE,
	@address VARCHAR(500),
	@GroupCode VARCHAR(6)
AS
	INSERT INTO studenti.studenti
	VALUES (@id, @name, @surname, @birthday, @address)
	INSERT INTO studenti.studenti_reusita
	VALUES (@id, 106, 104, @GroupCode, 'New', NULL, NULL)
GO

EXECUTE newStudent
		@id = 99,
		@name = 'Nume',
		@surname = 'Prenume',
		@birthday = '01/01/1997',
		@address = 'Aici',
		@GroupCode = 1
  ```
  
4. Create a procedure that would replace a professor in the _studenti_reusita_ table with another one:

  __SQL Querry:__
  
  ```sql
DROP PROCEDURE IF EXISTS transferReusita
GO

CREATE PROCEDURE transferReusita
	@oldName VARCHAR(60),
	@oldSurname VARCHAR(60),
	@newName VARCHAR(60),
	@newSurname VARCHAR(60),
	@discipline VARCHAR(255)
AS
	DECLARE @old_id INT, @new_id INT, @disc_id INT
	
	SET @old_id = (SELECT Id_Profesor
		FROM cadre_didactice.profesori
		WHERE Nume_Profesor = @oldName
		AND Prenume_Profesor = @oldSurname)
	
	SET @new_id = (SELECT Id_Profesor
		FROM cadre_didactice.profesori
		WHERE Nume_Profesor = @newName
		AND Prenume_Profesor = @newSurname)
	
	SET @disc_id = (SELECT Id_Disciplina
		FROM plan_studii.discipline
		WHERE Disciplina = @discipline)
	
	IF (@new_id = NULL OR @old_id = NULL OR @disc_id = NULL)
	BEGIN
		RAISERROR('Data not found.', 15, 1)
	END

	UPDATE studenti.studenti_reusita
	SET Id_Profesor = @new_id
	WHERE Id_Profesor = @old_id AND Id_Disciplina = @disc_id
GO

EXECUTE transferReusita
		@oldName = 'Frent',
		@oldSurname = 'Tudor',
		@newName = 'Jugaru',
		@newSurname = 'George',
		@discipline = 'Cercetari operationale'
  ```
  
  
5. Create a procedure that would find the top 3 best students from a group (input parameter) and add 1p to their marks:

  ```sql
DROP PROCEDURE IF EXISTS raiseTop3for
GO
CREATE PROCEDURE raiseTop3for
	@disciplina VARCHAR(255)
AS
RETURN SELECT DISTINCT TOP(3) Cod_Grupa,
			CONCAT(Nume_Student,' ',Prenume_Student) AS Nume_Prenume,
			Disciplina,
			Nota AS Nota_Veche,
			CASE WHEN Nota=10 THEN Nota ELSE Nota+1 END AS Nota_Noua
		FROM studenti.studenti_reusita r
		JOIN grupe g ON r.Id_Grupa = g.Id_Grupa
		JOIN studenti.studenti s ON r.Id_Student = s.Id_Student
		JOIN plan_studii.discipline d ON r.Id_Disciplina = d.Id_Disciplina
		WHERE Disciplina = @disciplina AND Tip_Evaluare = 'Examen'
		ORDER BY Nota DESC
GO
EXECUTE raiseTop3for @disciplina = 'Cercetari operationale'
  ```

6. Create functions based on the 4th lab:

  __SQL Querry:__
```sql
DROP FUNCTION IF EXISTS func61 
GO 
CREATE FUNCTION func61 (@pattern varchar(10)) 
returns table
as
return
	select * 
	from studenti.studenti 
	where Nume_Student like @pattern
go

DROP FUNCTION IF EXISTS func62
GO 
CREATE FUNCTION func62 () 
returns table
as
return
	select Nume_Student, Prenume_Student, count(studenti.studenti_reusita.Id_Student) as nrOfMarks
	from studenti.studenti left join studenti.studenti_reusita 
	on studenti.Id_Student = studenti_reusita.Id_Student
	group by studenti.Id_Student, Nume_Student, Prenume_Student
go
``` 
_
    ![a](/lab9/6.PNG)
  
7. Create a function that would compute the age of a student:

  __SQL Querry:__

  ```sql
DROP FUNCTION IF EXISTS getAge
GO
CREATE FUNCTION getAge(@bd DATE)
RETURNS INT
BEGIN
	RETURN DATEDIFF(YEAR, @bd, GETDATE())
END
GO
SELECT dbo.getAge('11.11.1997')
  ```
  ![a](/lab9/7.PNG)
8. Create a function that would return a student's data regarding his marks:


  __SQL Querry:__

  ```sql
DROP FUNCTION IF EXISTS studentData
GO

CREATE FUNCTION studentData(@np VARCHAR(50))
RETURNS TABLE
AS
	RETURN (SELECT @name Nume_Prenume_Student, Disciplina, Nota, Data_Evaluare
			FROM studenti.studenti S
			JOIN studenti.studenti_reusita SR ON S.Id_Student = SR.Id_Student
			JOIN plan_studii.discipline D ON SR.Id_Disciplina = D.Id_Disciplina
			WHERE @np = Nume_Student + ' ' + Prenume_Student)
GO

SELECT * FROM studentData('Dan David')
  ```
  ![a](/lab9/8.PNG)

9. Create a function that would find the best or the worst student in a group, depending on the value of the input variable __is_good__ which can be either 'sarguincios' or 'slab'. The function should return a table:


  
  __SQL Querry:__

  ```sql
DROP FUNCTION IF EXISTS getWorstBestSt
GO
CREATE FUNCTION getWorstBestSt(@Cod_Grupa VARCHAR(6), @is_good VARCHAR(20))
RETURNS TABLE
AS
	RETURN (
	SELECT TOP 1 
		@Cod_Grupa as 'Cod_Grupa',
		Nume_Student + ' ' + Prenume_Student as 'Nume_Prenume_Student',
		CAST(AVG(CAST(Nota AS DECIMAL(4, 2))) AS DECIMAL(4, 2)) as 'Nota_Medie',
		@is_good as 'is_good'
	FROM studenti.studenti S
	JOIN studenti.studenti_reusita SR ON S.Id_Student = SR.Id_Student
	JOIN grupe G ON SR.Id_Grupa = G.Id_Grupa
	WHERE Cod_Grupa = @Cod_Grupa AND Nota IS NOT NULL
	GROUP BY Nume_Student, Prenume_Student
	ORDER BY AVG(CAST(Nota AS DECIMAL(4, 2))) * CASE @is_good WHEN 'sarguincios' THEN -1 WHEN 'slab' THEN 1 END)
GO

SELECT * FROM getWorstBestSt('CIB171', 'sarguincios')
SELECT * FROM getWorstBestSt('CIB171', 'slab')

  ```
  
    ![a](/lab9/9.PNG)
