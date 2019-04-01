### 配置
keyProperty="userId"  表示将自增长后的Id赋值给实体类中的userId字段。
parameterType="com.chenzhou.mybatis.User" 这个属性指向传递的参数实体类
- 关于select标签一些属性功能如下：   
1、id：命名空间内唯一标识符,用来被引用这条语句。</br>
2、parameterType：用来指定传入参数的完全限定名或别名。 
3、resultType：指定返回的期望的完全限定名或别名,如果是集合,应该为集合包含的类型 ,不能和resultMap共存。 </br>
4、resultMap：命名引用外部的resultMap。 </br>
5、flushCache：默认false,设置为true,则不论语句什么时候被调用,都会清空缓存。 </br>
6、useCache：默认值true,会缓存本次语句结果。 </br>
7、timeout：这个设置驱动程序等待数据库返回请求结果,并抛出异常时间的最大等待值。默认不设置（驱动自行处理）。 </br>
8、fetchSize：暗示程序每次返回的结果行数。 </br>
9、statementType：STATEMENT、PREPARED、CALLABLE中的一种,让MyBatis选择使用Statement、PreparedStatement、CallableStatement。默认是PREPARED。 </br>
10、resultSetType：FORWARD_ONLY|SCROOL_SENSITIVE|SCROLL_INSENSITIVE中的一种,默认是不设置（驱动自行处理）。</br>
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
 - [链接:返回值问题](https://www.cnblogs.com/xingyunblog/p/6243179.html)

	<insert id="insert" parameterType="HttpRequestEntity" useGeneratedKeys="true" keyProperty="id">
> useGeneratedKeys="true" keyProperty="id" 组合，把产生的主键[useGeneratedKeys="true"]放入到实体类属性名的id[keyProperty="id"]中