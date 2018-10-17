### 插入
- prefix suffix suffixOverrides prefixOverrides
	```java
	<insert id="insertSelective" parameterType="InterfaceExtraInfoEntity" useGeneratedKeys="true" keyProperty="id">
	 insert into <include refid="table_name" />
	 <trim prefix="(" suffix=")" suffixOverrides=",">
	 //suffixOverrides prefixOverrides 删除sql 前缀 后缀的对应字符
	 //prefix suffix sql语句添加对应的字符
					 <if test="id != null"> id,</if>
	 </trim>
	 <trim prefix="values (" suffix=")" suffixOverrides=",">
					 <if test="id != null"> #{id},</if>
	 </trim>
 	</insert>
	```
