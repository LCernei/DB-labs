# Laboratory No.4


### Objectives

#### Query editor for T-SQL and PL/SQL:

### Tasks

5. Display a list of Students which have their last name ending in 'u'.

__SQL Querry:__

```sql
select * 
from studenti 
where Nume_Student like '%u'
```

__Output:__  
![5](/lab4/1.PNG)

21. How many marks does each student have? Display their names and surnames.

__SQL Querry:__

```sql
select Nume_Student, Prenume_Student, count(studenti_reusita.Id_Student) as nrOfMarks
from studenti left join studenti_reusita 
on studenti.Id_Student = studenti_reusita.Id_Student
group by studenti.Id_Student, Nume_Student, Prenume_Student
```

__Output:__
There are the first 46 rows out of 75. The others have value 0 in the 3rd column, but did not fit on the screen.
![21](/lab4/2.PNG)


25. In which study group **_(Cod_ Grupa)_** there are more than 24 students?

__SQL Querry:__

  ```sql
select grupe.Cod_Grupa
from 
  (select Id_Grupa, count(distinct Id_Student) as nr 
   from studenti_reusita
   group by Id_Grupa) as nrOfStudentsPerGroup
inner join grupe
on nrOfStudentsPerGroup.Id_Grupa = grupe.Id_Grupa
where nr > 24
  ```

__Output:__

![25](/lab4/3.PNG)
