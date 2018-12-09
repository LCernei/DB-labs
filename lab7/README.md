# Laboratory Work No.7


### Objectives

#### Diagrams, Schemes and Synonyms

### Tasks

1. Create a standart diagram of the __universitatea__ database:

  __Result:__

  ![out](/lab7/1.PNG)


2. Add constraints for _Sef_grupa_ and _Prof_Indrumar_:

  __Result:__

  ![out](/lab7/2.PNG)


3. Add the table _orarul_ to the diagram:

  __Result:__

  ![out](/lab7/3.PNG)
  
  
4. Add secondary keys to the table _orarul_:

  __Result:__
  
  ![out](/lab7/41.PNG)
  ![out](/lab7/42.PNG)
  
  
5. Also define FK-PK constraints to the table _orarul_:

  __Result:__

  ![out](/lab7/3.PNG)


6. Create 3 new schemas in the __universitatea__ database: _cadre_didactice_, _plan_studii_ and _studenti_. Transfer the table _profesori_ to the schema _cadre_didactice_, tables _orarul_, _discipline_ to the schema _plan_studii_, and the tables _studenti_, _studenti_reusita_ to the schema _studenti_:

  __SQL Querry:__

  ```sql
  ALTER SCHEMA cadre_didactice TRANSFER dbo.profesori
  ALTER SCHEMA plan_studii TRANSFER dbo.orarul
  ALTER SCHEMA plan_studii TRANSFER dbo.discipline
  ALTER SCHEMA studenti TRANSFER dbo.studenti
  ALTER SCHEMA studenti TRANSFER dbo.studenti_reusita
  ```
  
   __Result:__

  ![out](/lab7/6.PNG)
  

7. Modify 3 querries from the 4th lab with renamed table names:

  __SQL Querry:__

  ```sql
  select * 
  from studenti.studenti 
  where Nume_Student like '%u'


  select Nume_Student, Prenume_Student, count(studenti.studenti_reusita.Id_Student) as nrOfMarks
  from studenti.studenti left join studenti.studenti_reusita 
  on studenti.studenti.Id_Student = studenti.studenti_reusita.Id_Student
  group by studenti.studenti.Id_Student, Nume_Student, Prenume_Student


  select grupe.Cod_Grupa
  from 
  (select Id_Grupa, count(distinct Id_Student) as nr 
   from studenti.studenti_reusita
   group by Id_Grupa) as nrOfStudentsPerGroup
  inner join grupe
  on nrOfStudentsPerGroup.Id_Grupa = grupe.Id_Grupa
  where nr > 24
  ```

8. Create synonyms to simplify the previous querries:

__SQL Querry:__

  ```sql
CREATE SYNONYM dicipline2 FOR plan_studii.discipline
CREATE SYNONYM studenti2 FOR studenti.studenti
CREATE SYNONYM studenti_reusita2 FOR studenti.studenti_reusita

select * 
from studenti2
where Nume_Student like '%u'


select Nume_Student, Prenume_Student, count(studenti_reusita2.Id_Student) as nrOfMarks
from studenti2 left join studenti_reusita2
on studenti2.Id_Student = studenti_reusita2.Id_Student
group by studenti2.Id_Student, Nume_Student, Prenume_Student


select grupe.Cod_Grupa
from 
(select Id_Grupa, count(distinct Id_Student) as nr 
 from studenti_reusita2
 group by Id_Grupa) as nrOfStudentsPerGroup
inner join grupe
on nrOfStudentsPerGroup.Id_Grupa = grupe.Id_Grupa
where nr > 24
  ```
 __Result:__
![out](/lab7/6.PNG)
