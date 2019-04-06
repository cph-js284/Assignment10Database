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
parksregister.sql         locations of parks  - multipolygon
uo.sql                    exposed areas       - multipolygon
gt.sql                    street trees        - point
cs.sql                    bike racks          - point
tv.sql                    heavy trafic        - multistring
```
<br>
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


