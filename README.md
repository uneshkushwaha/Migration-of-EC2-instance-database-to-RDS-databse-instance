# Migration-of-EC2-instance-database-to-RDS-databse-instance
## We are Migrating  MariaDB database created on  EC2 instance database to RDS database instance in different multiple zone

      VideoLinK:https:[//drive.google.com/file/d/16skpaT53i4xs8yYMaOjsSNERE8M8j2kM/view?usp=sharing](https://www.linkedin.com/posts/unesh-kushwaha-4aa195224_project2-github-aws-activity-7184794834056962048-r1GG?utm_source=share&utm_medium=member_desktop)
 
 #Step1: Created a VPC name 'first-VPC' 
 
 #Step2: three subnets named 'first-subnet', '1b-subnet2', '1c-subnet3' are created in three different availbility zone i.e us-east-1a, us-east1b, us-east1c respectively for the purpose of RDS backup.
 
 #Step3: launched a EC2 instance name 'Database-on-EC2'and  connected to the EC2 instane private ip address.

 ##Creation of MariaDB database on the EC2 instance
                        
 ```bash
       ##Begin Configuration:
  sudo su-
  yum -y install mariadb-server wget  
  systemctl enable mariab
  systemctl start mariadb
  yum -y update
 

       1. It takes full administrative privileges from root user to modify,create and install software after identifying password prompt.It changes symbol to #
       2. Yum package manager is used to install two packages i.e mariadb-server and wget which is a command line tool for downloading files from the internet. -y automatically answer yes
       3. Configures MariaDB to start automatically at system boot.
       4. Start the MariaDB service immediately.
       5.Updates all installed packages on the system to their latest versions
```
           
##SET Environmental variables
 ```bash
              DBName=ec2RDS
              DBPassword=unesh12345
              DBRootPaasword=admin12345
              DBUser=ec2dbuser
 ```

            
##Database Setup on EC2 Instance:
```bash      
            echo "CREATE DATABASE ${DBName};" >> /tmp/db.setup
            echo "CREATE USER '${DBUser}' IDENTIFIED BY '${DBPassword}';" >> /tmp/db.setup
            echo "GRANT ALL PRIVILEGES ON *.* TO '${DBUser}'@'%';" >> /tmp/db.setup
            echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
            mysqladmin -u root password "${DBRootPassword}"
            mysql -u root --password="${DBRootPassword}" < /tmp/db.setup
            rm /tmp/db.setup
 ```


##Adding some dummy data to the Database inside EC2 instance:

```bash
mysql -u <username> -p <databasename>
USE ec2RDS;
CREATE TABLE table1 (id INT, name VARCHAR(45));
INSERT INTO table1 VALUES(1, 'Allice'), (2, 'Ross Linn'), (1, 'Broda V'), (2, 'Annie Marrie');
SELECT * FROM table1;
```


 ##Go to Amazon RDS and setup the RDS database instance

         -create a subnet group where three subnet with their respective AZ shows for multi-AZ backup purpose
         - click on Database
             -choose: Standard
             -Engine Option: MariaDB
             -Template:free tier
           *Setting:
              -type name:
              -credential settings i.e master username and password
              -Instance configuration: Burstable Classes
              - storage: gp2
              - Availbilty: Multi-AZ deployment as it is 
              - connectivity: don't connect ec2 compute rsource 
              - vpc and subnet groups, public access= no, vpc security group, A.Z= no preference, advance configuration i.e port number, Database authentication:password
              - Monitoring:enable
              - additional configuration:
                              inital name: first_rds_db
                              backup and maintenance : disable, no preference for window
                              Deletion Protection: disable
             - LAUNCH RDS INSTANCE



##Migration of Database in EC2 Instance to RDS Database:

```bash
     mysqldump -u <username> -p <databasename> ec2db.sql
     mysql -h <replace-rds-end-point-here> -P 3306 -u <master username> -p <databsename on RDS>  < ec2db.sql
     mysql -h <replace-rds-end-point-here> -P 3306 -u <master username> -p
     SHOW DATABASES;
     USE <databasename>
     SELECT * FROM table1;


        1. creating backup database named 'ecRDS' using the MYSQL utility mysqldump. database is saved on ec2db.sql

        2. connecting RDS database instance with endpoint i.e 'first-rdb-database-1.cls4s2ge8p8q.us-east-1.rds.amazonaws.com' and port number is essential, backup database i.e ec2db.sql is imported on RDS database instance, it prompots for mater passowrd from RDS ,master username= admin, master password=unesh12345 ,databasename= first_rds_db

        3. connected to the RDS databse for futher interacton

        4. it shows all different  databases names in the RDS instance
        5. use only the created database on ec2
        6. congrats your database from ec2 instance is migrated RDS databse instance
```




        
 
      
