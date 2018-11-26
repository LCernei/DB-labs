# Laboratory No.5


### Objectives

#### T-SQL: Procedural Instructions

### Tasks

1. Find maximum value between 3 numbers.

__SQL Querry:__

```sql
DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @MAI_MARE INT;

SET @N1 = 60 * RAND();
SET @N2 = 60 * RAND();
SET @N3 = 60 * RAND();

IF (@N1 > @N2 AND @N1 > @N3)
  SET @MAI_MARE = @N1
ELSE IF (@N2 > @N1 AND @N2 > @N3)
  SET @MAI_MARE = @N2
ELSE
  SET @MAI_MARE = @N3

PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(2)); 
```

  __Output:__

![out](/lab5/1.PNG)


2. Display the first 10 rows of the students' marks on the first evaluation on `Baze de date` course, except for marks 6 and 8.

 __SQL Querry:__

```sql
DECLARE @DISCIPLINA VARCHAR(20); DECLARE @TIP_EVALUARE VARCHAR(20), @EXCLUDE1 INT, @EXCLUDE2 INT;
SET @DISCIPLINA = 'Baze de date'
SET @TIP_EVALUARE = 'Testul 1'
SET @EXCLUDE1 = 6
SET @EXCLUDE2 = 8

SELECT TOP 10 Nume_Student, Prenume_Student, Nota
FROM studenti_reusita
	INNER JOIN discipline ON studenti_reusita.Id_Disciplina = discipline.Id_Disciplina
	INNER JOIN studenti ON studenti_reusita.Id_Student = studenti.Id_Student
WHERE Tip_Evaluare = @TIP_EVALUARE
	AND Disciplina = @DISCIPLINA
	AND Nota = IIF(Nota <> @EXCLUDE1 AND Nota <> @EXCLUDE2, Nota, NULL)
```

 __Output:__
 
 ![out](/lab5/2.PNG)
 

3. Rewrite the 1st task using CASE.

  __SQL Querry:__

  ```sql
DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @MAI_MARE INT;

SET @N1 = 60 * RAND();
SET @N2 = 60 * RAND();
SET @N3 = 60 * RAND();

SET @MAI_MARE = 
CASE
	WHEN (@N1 > @N2 AND @N1 > @N3) THEN @N1
	WHEN (@N2 > @N1 AND @N2 > @N3) THEN @N2
	ELSE @N3
END

PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(2)); 
  ```
 __Output:__
 
 ![out](/lab5/3.PNG)
 
4. Rewrite the 1st task with try/catch blocks and RAISERROR:

  __SQL Querry:__

```sql
BEGIN TRY

DECLARE @N1 INT, @N2 INT, @N3 INT;
DECLARE @MAI_MARE FLOAT;

SET @N1 = 60 * RAND();
SET @N2 = 60 * RAND();
SET @N3 = 60 * RAND();

IF (@N1 > @N2 AND @N1 > @N3)
  SET @MAI_MARE = @N1
ELSE IF (@N2 > @N1 AND @N2 > @N3)
  SET @MAI_MARE = @N2
ELSE
  SET @MAI_MARE = @N3;

IF (@MAI_MARE > 50)
	RAISERROR('The number is too big', 15, 1)
IF (@MAI_MARE < 10)
	RAISERROR('The number is too small', 15, 1)

PRINT @N1;
PRINT @N2;
PRINT @N3;
PRINT 'Mai mare = ' + CAST(@MAI_MARE AS VARCHAR(20));

END TRY
BEGIN CATCH
	PRINT 'ERROR: ' + CAST(ERROR_MESSAGE() AS VARCHAR(255))
END CATCH
```

 __Output:__
 
 ![out](/lab5/4.PNG)
 ![out](/lab5/44.PNG)
