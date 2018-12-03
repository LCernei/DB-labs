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
