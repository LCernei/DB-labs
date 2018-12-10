# Laboratory Work No.10


### Objectives

#### Creating and using Triggers

### Tasks

1. Modify the _inregistrare_noua_ trigger so that on update a message would pop:

  __SQL Querry:__
  
  ```sql
DROP TRIGGER IF EXISTS plan_studii.inregistrare_noua 
GO
CREATE TRIGGER plan_studii.inregistrare_noua ON plan_studii.orarul
AFTER UPDATE
AS
  IF (UPDATE(Auditoriu))
  BEGIN
  	SELECT 'Lectia la disciplina ' + UPPER(D.Disciplina)+ ', a grupei ' + G.Cod_Grupa +
		', ziua de ' + CAST(I.Zi as VARCHAR(5)) + ', de la ora ' + CAST(I.Ora as VARCHAR(5))
		+ ', a fost transferata in aula ' + CAST(I.Auditoriu as VARCHAR(5)) + ', Blocul '+
		CAST(I.Bloc as VARCHAR(5)) as Mesaj
  	FROM inserted I
  	JOIN grupe G ON G.Id_Grupa = I.Id_Grupa
  	JOIN discipline D ON D.Id_Disciplina = I.Id_Disciplina
  END
GO

SELECT * FROM plan_studii.orarul
UPDATE plan_studii.orarul SET Auditoriu = 999 WHERE Auditoriu = 100
SELECT * FROM plan_studii.orarul

  ```
  
  __Result:__

  ![task1](/lab10/1.PNG)

2. Create a trigger that would assure a consecutive insert into the tables _studenti_ and _studenti_reusita_:

    __SQL Querry:__
  
  ```sql
DROP TRIGGER IF EXISTS studenti.insert_students
GO
CREATE TRIGGER studenti.insert_students ON studenti.studenti
INSTEAD OF INSERT
AS
BEGIN
  DECLARE @ID_STUDENT INT = (SELECT Id_Student FROM inserted)
  INSERT INTO studenti.studenti SELECT * FROM inserted
  INSERT INTO studenti.studenti_reusita VALUES (@ID_STUDENT, 100, 100, 1, 'init', NULL, GETDATE())
END
GO 

INSERT INTO studenti.studenti VALUES (191, 'Sergiu', 'Calancea', '01.01.1997', 'mun. Chisinau')

SELECT * FROM studenti.studenti
SELECT * FROM studenti.studenti_reusita ORDER BY Id_Student
  ```
  
  __Result:__

  ![task1](/lab10/2.PNG)

3. Create a trigger that would prohibit inserting smaller marks and modifying _Data_Evaluare_ data if it's not null in table _studenti_reusita_. The trigger should only be triggered when the affected data belongs to students from _CIB171_ group:

  __SQL Querry:__

  ```sql
DROP TRIGGER IF EXISTS studenti.lowMarks
GO
CREATE OR ALTER TRIGGER studenti.lowMarks ON studenti.studenti_reusita
FOR UPDATE
AS
BEGIN
	IF EXISTS (SELECT TOP 1 I.Nota FROM inserted I INNER JOIN grupe G
		ON I.Id_Grupa = G.Id_Grupa INNER JOIN deleted D
		ON I.Id_Student = D.Id_Student
		WHERE Cod_Grupa = 'CIB171'
		AND (I.Nota < D.Nota OR D.Data_Evaluare IS NOT NULL))
  BEGIN
    PRINT('The marks can''t be lowered!')
    ROLLBACK
  END
END
GO

UPDATE studenti.studenti_reusita
SET Nota = Nota - 1
WHERE Id_Grupa = (SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'CIB171')
  ```
  
  __Result:__
  
  ![task1](/lab10/3.PNG)
  
4. Create a DDL trigger that prohibits modifying the column _Id_Disciplina_:

  __SQL Querry:__
  
  ```sql
DROP TRIGGER IF EXISTS blockId_Disc ON DATABASE
GO
CREATE TRIGGER blockId_Disc ON DATABASE
FOR ALTER_TABLE
AS
BEGIN
	DECLARE @modColumn VARCHAR(50) = EVENTDATA().value('(/EVENT_INSTANCE/AlterTableEVENTList/Alter/Columns/Name)[1]', 'VARCHAR(50)');

	IF @modColumn = 'Id_Disciplina'
	BEGIN
		RAISERROR ('You can''t alter the Id_Disciplina column!', 16, 1)
		ROLLBACK
	END
END
  ```
  __Result:__
  
  ![task1](/lab10/4.PNG)
  
5. Create a trigger that prohibits modifying the database outside work hours:

  ```sql
DROP TRIGGER IF EXISTS overWorkHours ON DATABASE
GO
CREATE TRIGGER overWorkHours ON DATABASE
FOR ALTER_TABLE
AS
BEGIN
	IF (DATEPART(HOUR, GETDATE()) NOT BETWEEN 9 AND 18) OR
		(DATEPART(DAY, GETDATE()) NOT BETWEEN 1 AND 5)
	BEGIN
		PRINT('Can''t modify anything outside Work Hours!')
		ROLLBACK
	END
END
GO
ALTER TABLE studenti.studenti ALTER COLUMN Data_Nastere_Student VARCHAR(50)
  ```
 ![task1](/lab10/5.PNG)
6. Create a DDL trigger that would modify all tables if the column _Id_Profesor_ is changed:

  __SQL Querry:__

  ```sql
DROP TRIGGER IF EXISTS globalTeacherID ON DATABASE
GO
CREATE TRIGGER globalTeacherID ON DATABASE
FOR alter_table
AS
	DECLARE @column_modified VARCHAR(20)
	DECLARE @command VARCHAR(500)
	DECLARE @command2 VARCHAR(500)
	DECLARE @object VARCHAR(50)
	DECLARE @schema VARCHAR(50)

	SET @command = EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)')
	SET @object = EVENTDATA().value('(/EVENT_INSTANCE/ObjectName)[1]', 'nvarchar(max)')			
	SET @column_modified = EVENTDATA().value('(/EVENT_INSTANCE/AlterTableActionList/Alter/Columns/Name)[1]', 'nvarchar(max)')
	SET @schema = EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(max)')											
	IF @column_modified = 'Id_Profesor'
	BEGIN
		SELECT @command2 = REPLACE(@command, @schema + '.' + @object, 'dbo.profesori_new')
		EXECUTE(@command2)
		SELECT @command2 = REPLACE(@command, @schema + '.' + @object, 'studenti.studenti_reusita')
		EXECUTE(@command2)
		SELECT @command2 = REPLACE(@command, @schema + '.' + @object, 'plan_studii.orarul')
		EXECUTE(@command2)
	END
  ```
