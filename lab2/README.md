# Laboratory No.2


### Objectives

#### Database Creation and Maintenance Tools

### Tasks

1. Create a database in `MyDocuments/Data` with 16MB growth of the primary file and 128MB limit, 64MB growth and 1024MB limit of the log file. For other files, create a new `Filegroup`, and limit the growth from 64MB to 1024MB.

![ex1](/lab2/1.PNG)
![ex1](/lab2/11.PNG)

2. Create a database with the log file physically placed in `MyDocuments/Log` and with different names physically and logically. It also should be compatible with the newest MS SQL Server 2017 and restricted to a single user at a time.

![ex2](/lab2/2.PNG)
![ex2](/lab2/22.PNG)

3. Create a maintenance plan for the 1st database, with a shrink task if the db overweights 2000MB, the schedule for the plan should be every Friday on 00:00. The report should be saved in `MyDocuments\SQL_event_logs` folder.

![ex3](/lab2/3.PNG)

4. Create a maintenance plan for the 2nd database, with `Rebuild index` (10% free space per page, indexes sorted in tempdb) and `Cleanup History` (clean Backup-Restore operations older than 6 weeks) tasks, every first sunday every month.

![ex4](/lab2/4.PNG)
