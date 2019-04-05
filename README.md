# Assignment10Database
This is the 10th. assignment for PBA database soft2019spring

<b>How many parks are located in exposed areas?</b><br>
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
