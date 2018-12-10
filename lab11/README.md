# Laboratory Work No.11


### Objectives

#### Restoring a Database

### Tasks

1. Create a full backup file of the _universitatea_ database:

  __SQL Querry:__
  
  ```sql
USE universitatea
GO
IF EXISTS (SELECT * FROM master.dbo.sysdevices WHERE name='device01')
EXEC sp_dropdevice 'device01' , 'delfile';
GO
EXEC sp_addumpdevice 'DISK', 'device01', 'E:\Backup_lab11\task1.bak'
GO
BACKUP DATABASE universitatea
TO device01
  ```
__Result__:

![a](/lab11/1.PNG)

2. Create a differential backup file of the _universitatea_ database:

    __SQL Querry:__
  
  ```sql
USE universitatea
GO
IF EXISTS(SELECT * FROM master.dbo.sysdevices WHERE name='device02')
EXEC sp_dropdevice 'device02', 'delfile'
GO
EXEC sp_addumpdevice 'DISK', 'device02', 'E:\Backup_lab11\exercitiul2.bak'
GO
BACKUP DATABASE universitatea
TO device02
  ```
__Result__:

![a](/lab11/2.PNG)

3. Create a log backup file for the _universitatea_ database:

  __SQL Querry:__

  ```sql
USE universitatea
GO
IF EXISTS(SELECT * FROM master.dbo.sysdevices WHERE name='device03')
EXEC sp_dropdevice 'device03', 'delfile'
GO
EXEC sp_addumpdevice 'DISK', 'device03', 'E:\Backup_lab11\exercitiul3.bak'
GO
BACKUP LOG universitatea
TO device03
  ```
__Result__:

![a](/lab11/3.PNG) 

4. Restore all the backup files created. The restore should be completed in a new database called _universitatea_lab11_:

  __SQL Querry:__
  
  ```sql
USE universitatea
GO
IF EXISTS(SELECT * FROM master.sys.databases WHERE name='universitatea_lab11')
DROP DATABASE universitatea_lab11
GO
RESTORE DATABASE universitatea_lab11
FROM device01
WITH MOVE 'universitatea' TO 'E:\Backup_lab11\universitatea_lab11.mdf',
		MOVE 'universitatea_log' TO 'E:\Backup_lab11\universitatea_lab11_log.ldf',
		NORECOVERY
GO
RESTORE DATABASE universitatea_lab11
FROM device02
WITH NORECOVERY
GO
RESTORE DATABASE universitatea_lab11
FROM device03
WITH NORECOVERY
  ```
__Result__:

![a](/lab11/4.PNG)
