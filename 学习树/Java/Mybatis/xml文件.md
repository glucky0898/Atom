### 插入
- prefix suffix suffixOverrides prefixOverrides
	```java
	insert into <include refid="table_name" />
	<trim prefix="(" suffix=")" suffixOverrides=",">
	```
	> suffixOverrides prefixOverrides 删除sql 前缀 后缀的对应字符
	> prefix suffix sql语句添加对应的字符
