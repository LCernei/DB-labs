# Laboratory No.6


### Objectives

#### Creation of tables and indexes

### Tasks

1. Write a T-SQL instruction to fill the __Adresa_Postala_profesor__ from the table __profesori__ with the value _mun.Chisinau_ if the address is null:

__SQL Querry:__

```sql
update profesori
set Adresa_Postala_Profesor = 'mun.Chisinau'
where Adresa_Postala_Profesor is null
```

  __Output:__
 ![out](/lab6/1.PNG)

2. Modify the scheme of the table __grupe__, so that the field _Cod_Grupa_ has unique not null values:


  __SQL Querry:__

```sql
ALTER TABLE grupe
ADD CONSTRAINT unique_group UNIQUE(Cod_Grupa)
```

3. Add to the table __grupe__ 2 new columns - _Sef_Grupa_ and _Prof_Indrumator_ with the specified conditions:

  __SQL Querry:__

  ```sql
ALTER TABLE grupe
ADD Sef_grupa INT,
	Prof_Indrumator INT;

ALTER TABLE grupe
ADD FOREIGN KEY (Sef_grupa) REFERENCES studenti(Id_Student),
	FOREIGN KEY (Prof_Indrumator) REFERENCES profesori(Id_Profesor);

UPDATE grupe
SET Sef_grupa =	(
	SELECT TOP 1 Id_Student
	FROM studenti_reusita
	WHERE grupe.Id_Grupa = studenti_reusita.Id_Grupa
	GROUP BY Id_Student
	ORDER BY AVG(CONVERT(FLOAT, Nota)) DESC
	)

UPDATE grupe
SET Prof_Indrumator = (
	SELECT TOP 1 Id_Profesor
	FROM studenti_reusita
	WHERE grupe.Id_Grupa = studenti_reusita.Id_Grupa
	GROUP BY Id_Profesor
	ORDER BY COUNT(Id_Disciplina) DESC, Id_Profesor ASC
	)

ALTER TABLE grupe ADD UNIQUE (Sef_grupa)
ALTER TABLE grupe ADD UNIQUE (Prof_Indrumator);
  ```

  __Output:__
   ![out](/lab6/3.PNG)
   

4. Write a T-SQL querry that would add an additional point to the evaluation marks of the group chiefs:


  __SQL Querry:__

```sql
UPDATE studenti_reusita 
SET Nota += 1 WHERE Nota != 10 AND Id_Student IN (SELECT Sef_grupa FROM grupe)
```

__Output:__
   ![out](/lab6/4.PNG)

5. Create a new table _profesori_new_ (as described in the book):

  __SQL Querry:__

```sql
CREATE TABLE profesori_new (
	Id_Profesor INT PRIMARY KEY CLUSTERED,
	Nume_Profesor VARCHAR(60),
	Prenume_Profesor VARCHAR(60),
	Localitate VARCHAR(255) DEFAULT('mun. Chisinau'),
	Adresa_1 VARCHAR(255),
	Adresa_2 VARCHAR(255));

INSERT INTO profesori_new(Id_Profesor, Nume_Profesor, Prenume_Profesor, Localitate, Adresa_1, Adresa_2)
SELECT	Id_Profesor,
		Nume_Profesor,
		Prenume_Profesor,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, 1, CHARINDEX(', str.', Adresa_Postala_Profesor) - 1)
			WHEN CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, 1, CHARINDEX(', bd.', Adresa_Postala_Profesor) - 1)
			ELSE SUBSTRING(Adresa_Postala_Profesor, 1, LEN(Adresa_Postala_Profesor))
		END Localitate,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(	Adresa_Postala_Profesor,
								CHARINDEX(', str.', Adresa_Postala_Profesor) + 2,
								CHARINDEX(',', SUBSTRING(	Adresa_Postala_Profesor,
															CHARINDEX(', str.', Adresa_Postala_Profesor) + 2, 99)) - 1)
			WHEN CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(	Adresa_Postala_Profesor,
								CHARINDEX(', bd.', Adresa_Postala_Profesor) + 2,
								CHARINDEX(',', SUBSTRING(	Adresa_Postala_Profesor,
															CHARINDEX(', bd.', Adresa_Postala_Profesor) + 2, 99)) - 1)
		END Adresa_1,
		CASE
			WHEN CHARINDEX(', str.', Adresa_Postala_Profesor) > 0 OR CHARINDEX(', bd.', Adresa_Postala_Profesor) > 0
				THEN SUBSTRING(Adresa_Postala_Profesor, PATINDEX('%[0-9]%', Adresa_Postala_Profesor), 10)
		END Adresa_2
FROM profesori
```

__Output:__
   ![out](/lab6/5.PNG)



6. Insert the following data in the table __orarul__:

__SQL Querry:__

```sql
CREATE TABLE orarul (
Id_Disciplina INT,
Id_Profesor INT,
Id_Grupa SMALLINT,
Bloc CHAR(10),
Zi CHAR(10),
Ora TIME(0),
Auditoriu INT,
PRIMARY KEY(Id_Grupa, Zi, Ora),
FOREIGN KEY (Id_Disciplina) REFERENCES discipline(Id_Disciplina),
FOREIGN KEY (Id_Profesor) REFERENCES profesori(Id_Profesor),
FOREIGN KEY (Id_Grupa) REFERENCES grupe(Id_Grupa),
);

INSERT INTO orarul (Id_Disciplina, Id_Profesor, Bloc, Id_Grupa, Zi, Ora, Auditoriu)
VALUES(107, 101, 'B', 1, 'Luni', '08:00', 202),
	  (108, 101, 'B', 1, 'Luni', '11:30', 501),
      (119, 117, 'B', 1, 'Luni', '13:00', 501)
```
__Output:__
   ![out](/lab6/6.PNG)


7. Also insert (using select) this data in the same table __orarul__:

__SQL Querry:__

```sql
INSERT INTO  orarul(Id_Grupa, Id_Disciplina, Id_Profesor, Ora, Auditoriu, Bloc, Zi)
VALUES (	
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Structuri de date si algoritmi'),
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Bivol' AND Prenume_Profesor = 'Ion'),
			'08:00', 101, 'B', 'Luni'
		),
		(
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Programe aplicative'),
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Mircea' AND Prenume_Profesor = 'Sorin'),
			'11:30', 101, 'B', 'Luni'
		),
		(
			(SELECT Id_Grupa FROM grupe WHERE Cod_Grupa = 'INF171'),
			(SELECT Id_Disciplina FROM discipline WHERE Disciplina = 'Baze de date'),
			(SELECT Id_Profesor FROM profesori WHERE Nume_Profesor = 'Micu' AND Prenume_Profesor = 'Elena'),
			'13:00', 101, 'B', 'Luni'
		)
```

__Output:__
![out](/lab6/7.PNG)


8. Create indexes and show statistics before and after optimization for a querry from Lab4:

In which study group **_(Cod_ Grupa)_** there are more than 24 students?
__SQL Querry:__

  ```sql
ALTER DATABASE universitatea 
ADD FILEGROUP userdatafgroup4  
GO  
ALTER DATABASE universitatea  
ADD FILE   
(  
    NAME = Index4,  
    FILENAME = 'E:\userdatafgroup4.ndf',  
    SIZE = 10MB,  
    MAXSIZE = 100MB,  
    FILEGROWTH = 10%  
)   
TO FILEGROUP userdatafgroup4;  
GO
create nonclustered index create_index4 on studenti_reusita(Id_Grupa) on [userdatafgroup4]

set statistics time on
select grupe.Cod_Grupa
from 
  (select Id_Grupa, count(distinct Id_Student) as nr 
   from studenti_reusita
   group by Id_Grupa) as nrOfStudentsPerGroup
inner join grupe
on nrOfStudentsPerGroup.Id_Grupa = grupe.Id_Grupa
set statistics time off
  ```

__Output:__
![out](/lab6/81.PNG)
![out](/lab6/82.PNG)

