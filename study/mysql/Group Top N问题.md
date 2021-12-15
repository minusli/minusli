# Group Top N 问题
- 添加全局排序变量 group by top 3：
    ```mysql
    select * 
    from (
        select (@i:=@i+1) i, inner_app.* 
        from application as inner_app, (select @i:=0) as i 
        where inner_app.talent_id in (3, 10) 
        order by active desc, id desc
    ) as t1 
    where 2 > (
        select count(1) 
        from (
            select (@i:=@i+1) i, inner_app.* 
            from application as inner_app, (select @i:=0) as i 
            where inner_app.talent_id in (3, 10) 
            order by active desc, id desc
        ) as t2 
        where t1.talent_id = t2.talent_id and t2.i < t1.i)
    ```

- 添加分组内的排序变量 group by top 3：
    ```mysql
    select var_no, var_talent_id 
    from (
        select 
            t1.*, 
            @var_no:=case when @var_talent_id=talent_id then @var_no+1 else 1 end as var_no,
            @var_talent_id:=talent_id as var_talent_id
        from (
            select application.* from application
            where talent_id in (3, 10)
            order by talent_id, stage, id desc
        ) as t1, (select @var_no:=null) as t2, (select @var_talent_id:=null) as t3
    ) as t2 where var_no < 3;
    ``` 