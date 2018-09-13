####分割方法思路
首先根据源数据china_city和lagou_position_bk两个表。然后根据这两个表创建出了lagou_citi表。其中创建lahou_city表的时候，我先用连接查询以及联合查询，查出了根据1,2，3折三个等级区分出了中国的省市区。

create table lagou_city as
select d.id, p.cityName as province, c.cityName as city, d.cityName as district from
  (select * from china_city where depth=3) d
    join china_city c on d.parentId = c.id and c.depth=2
    join china_city p on c.parentId = p.id and p.depth=1
union
select c.id, p.cityName as province, c.cityName as city, null as district from (select * from china_city where depth=2) c
  join china_city p on c.parentId = p.id and p.depth = 1;
  
  
  
  创建lagou_company表，就是根据源数据lagou_position_bk表中取公司相关的信息的信息：
  
  drop table if exists lagou_company;
create table lagou_company as
  select distinct t.company_id         as cid,
                  t.company_short_name as short_name,
                  t.company_full_name  as full_name,
                  t.company_size       as size,
                  t.financestage
  from lagou_position_bk t;
  
  
  
  然后再创建lagou_position 表，这是创建一个公司表，创建过程就是根据源数据lagou_position_bk表创建出相关公司信息的表：
  我先查询position_bk 表中 district 为空的数据，然后再查询position_bk 表中 district 不为空的数据，在使用union all联合起来查询，再用创建表的语句套上
  
  
  create table lagou_position
as
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at from
(
  -- position 表中 district 为空的数据
  select p.*, c.cid from (select * from lagou_position_bk where district is null) p
     join lagou_city c on c.city like concat(p.city, '%') and c.district is null

  union all

  -- position 表中 district 不为空的数据
  select p.*, c.cid from (select * from lagou_position_bk where district is not null) p
    join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%')

) as ppp;