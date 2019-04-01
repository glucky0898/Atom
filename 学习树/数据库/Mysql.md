#### 主表，从表级联问题  
[举例](https://www.cnblogs.com/fengyingwang/p/3391106.html)  
    Mysql中的 的 Cascade ，NO ACTION ，Restrict ，SET NULL  
    外键约束对子表的含义: 
       如果在父表中找不到候选键,则不允许在子表上进行insert/update 

    外键约束对父表的含义:   
       在父表上进行update/delete以更新或删除在子表中有一条或多条对应匹配行的候选键时,父表的行为取决于：在定义子表的外键时指定的on update/on delete子句, InnoDB支持５种方式, 分列如下
    - cascade方式 
    在父表上update/delete记录时，同步update/delete掉子表的匹配记录 
    On delete cascade从mysql3.23.50开始可用; on update cascade从mysql4.0.8开始可用 
    - set null方式 
    在父表上update/delete记录时，将子表上匹配记录的列设为null 
    要注意子表的外键列不能为not null 
    On delete set null从mysql3.23.50开始可用; on update set null从mysql4.0.8开始可用
    - No action方式 
    如果子表中有匹配的记录,则不允许对父表对应候选键进行update/delete操作 
    这个是ANSI SQL-92标准,从mysql4.0.8开始支持 
    - Restrict方式 
    同no action, 都是立即检查外键约束 
    - Set default方式 
    解析器认识这个action,但Innodb不能识别，不知道是什么意思
    注意：trigger不会受外键cascade行为的影响,即不会解发trigger