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
parks.sql                 locations of parks  - multipolygon
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
7) source the *\*.sql-files* (sourcing the gt.sql file takes several seconds - depending on hardware)
```
source ./parks.sql;
source ./uo.sql;
source ./gt.sql;
source ./cs.sql;
source ./tv.sql;
```
8) Adding indicies
```sql
ALTER TABLE parks ADD SPATIAL INDEX(wkb_geometry);
ALTER TABLE uo ADD SPATIAL INDEX(wkb_geometry);
ALTER TABLE gt ADD SPATIAL INDEX(wkb_geometry);
ALTER TABLE cs ADD SPATIAL INDEX(wkb_geometry);
ALTER TABLE tv ADD SPATIAL INDEX(wkb_geometry);
```
*This takes care of the setup, you can now run the queries directly in the shell, or copy them into the workbench*

# Exercise 1

<b>How many parks are located in exposed areas?</b> <br>

*Testing for MBRoverlaps* 
```sql
select count(parks.park_id) from  parks, uo
where mbroverlaps(uo.wkb_geometry, parks.wkb_geometry);
```
Answer : 163<br>
where uo = exposed areas<br>

*Testing for ST_overlaps* 
```sql
select count(parks.park_id) from  parks, uo
where mbroverlaps(uo.wkb_geometry, parks.wkb_geometry);
```
Answer : 50<br>
where uo = exposed areas<br>

*Testing for contained*
```sql
select count(parks.park_id) from  parks, uo
where st_contains(uo.wkb_geometry, parks.wkb_geometry);
```
Answer : 44<br>
where uo = exposed areas<br>

<br>
<b>How many trees are located in exposed areas?</b> <br>

```sql
select count(gt.FID) from  gt, uo
where st_contains(uo.wkb_geometry, gt.wkb_geometry);
```
Answer : 4866<br>
where gt = street trees and uo = exposed areas<br>
<br>

# Exercise 2

<b>How many bike racks are places along routes for heavy traffic?</b> <br>

<b>The shotgun method aka. the fast and almost-certainly-not-precise-method</b> <br>

```sql
select count(cs.FID), sum(cs.antal_pladser)  from cs,tv
where mbrcontains(tv.wkb_geometry,cs.wkb_geometry)
```
Answer : 9442 racks, 114919 spots<br>
where cs = bike racks and tv = heavy-traffic<br>
<br>
<b>the more precise but slower(80+ secs) method (even with indicies)</b><br>
*notice I change the SRID here. I then create a circle 100m radius around each bike rack, I then calculate if the heavy-traffic-road intersects with the circle*

```sql
select count(csCircle.FID), sum(csCircle.antal_pladser) from
(select *, ST_Buffer(ST_GeomFromText(ST_AsText(cs.wkb_geometry), 0), 0.001) as radius from cs) 
as csCircle, 
(select *, ST_GeomFromText(ST_AsText(tv.wkb_geometry), 0) as road from tv) 
as Troads
where st_intersects(Troads.road, csCircle.radius);
```

Answer : 2110 racks, 31726 spots<br>
(*if the radius is lowered to 25m*)<br>
Answer : 637 racks, 9101 spots<br>
where cs = bike racks and tv = heavy-traffic<br>

# Database dump
Included in the repo is a file called *copenhagenDbDump.sql* this is the databasedump containing the copenhagen database that all of the queries have been run against.<br>
*To use this file, copy it into the container and source it from the shell*

