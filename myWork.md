###查询广东省的市和县做思路整理
<li>首先我先查出整个s_provinces表，在进行扩展，然后与自己parentId和id进行关联，查询查出所有广东省的市。
<li>其次我再次与自己的parentId和id关联，查询出县（区）和所有市（一市有多个县和区，所有查询有重复的市）。
<li>然后我只取省、市、县，就能取出全部广东省所有的市和县（不包括冗余的）
<li>最后再次与自己的parentId和id关联，并只取广东省的市和重复的null值的县（市），最终我使用union all的方法来合并两个结果集，重复的（县）市也取，然后取出了所有广东省的市和县，包括重复的数据。


