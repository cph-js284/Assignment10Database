# Assignment10Database
This is the 10th. assignment for PBA database soft2019spring

# What it is
This is a project containing sourcable sql files and MySqldumps for excercise 1 and 2.<br>
The sourcable sql files are built using Kaspers *makeSQLfile* method from the lecture notes<br>
*I have however changed the order of the return parameters in the helper-method*

```
def transformer(x,y):
    # point is on the form "722942.66 6173097.7"
    ll = transform(inProj, outProj, x,y)
    return str(ll[1])+' '+str(ll[0])
```

<br>
<b>Tables</b>

```
Filename                  Contains
parks.sql         locations of parks  - multipolygon
uo.sql                    exposed areas       - multipolygon
gt.sql                    street trees        - point
cs.sql                    bike racks          - point
tv.sql                    heavy trafic        - multistring
```
# Setup
1) clone the repo
2) start a docker container running MySql:
```
sudo docker run --rm --name mysql01 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=pass1234 -d mysql
```
3) copy the *\*.sql* files into the container
```
sudo docker cp ./parks.sql mysql01:/parks.sql
sudo docker cp ./uo.sql mysql01:/uo.sql
sudo docker cp ./gt.sql mysql01:/gt.sql
sudo docker cp ./cs.sql mysql01:/cs.sql
sudo docker cp ./tv.sql mysql01:/tv.sql
```
4) enter the container
```
sudo docker exec -it mysql01 bash 
```
5) enter MySql
```
mysql -u root -ppass1234
```
6) create new database (I called this one copenhagen), and switch to newly create database
```
create database copenhagen;
use copenhagen;
```
7) source the *\*.sql-files*
```
source ./parks.sql;
source ./uo.sql;
source ./gt.sql;
source ./cs.sql;
source ./tv.sql;
```
*This takes care of the setup, you can now run the queries directly in the shell, or copy them into the workbench*

# Exercise 1

<b>How many parks are located in exposed areas?</b> <br>

*Testing for overlaps* 
```sql
select count(parkregister.areal_id) from  parkregister, uo
where mbroverlaps(uo.wkb_geometry, parkregister.wkb_geometry);
```
Answer : 0<br>
where uo = exposed areas<br>

*Testing for contained*
```sql
select count(parkregister.areal_id) from  parkregister, uo
where st_contains(uo.wkb_geometry, parkregister.wkb_geometry);
```
Answer : 0<br>
where uo = exposed areas<br>

*Testing for within*
```sql
select count(parkregister.areal_id) from  parkregister, uo
where mbrwithin(uo.wkb_geometry, parkregister.wkb_geometry);
```
Answer : 0<br>
where uo = exposed areas<br>
<br>
*Conclusion there are no parks in exposed areas*<br>
<br>
<b>How many trees are located in exposed areas?</b>
```sql
select count(gt.FID) from  gt, uo
where st_contains(uo.wkb_geometry, gt.wkb_geometry);
```
Answer : 4866<br>
where gt = street trees and uo = exposed areas<br>
<br>
<b>How many bike racks are places along routes for heavy traffic?</b>
```sql
```
Answer : <br>
where <br>

# Exercise 2
