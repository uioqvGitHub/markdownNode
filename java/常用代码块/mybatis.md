

[TOC]

### mybatis core 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<settings>
		<setting name="lazyLoadingEnabled" value="true" />
		<setting name="aggressiveLazyLoading" value="false" />
	</settings>
	<plugins>
		<plugin
			interceptor="com.hjcrm.publics.util.BaseOperInterceptor">
			<property name="databaseType" value="mysql" />
		</plugin>
	</plugins>


	<mappers>
		<mapper resource="commonsqlmappings/CommonMapper.xml" />
		<mapper resource="mybatis/UserMapper.xml" />
		<mapper resource="mybatis/MenuMapper.xml" />
		<mapper resource="mybatis/RoleMapper.xml" />
		<mapper resource="mybatis/DeptMapper.xml" />
		<mapper resource="mybatis/ForgetMapper.xml" />
		<mapper resource="mybatis/UserLogsMapper.xml" />
		<mapper resource="mybatis/TodaynoteMapper.xml" />
		<mapper resource="mybatis/PatterMapper.xml" />
		<mapper resource="mybatis/CourseMapper.xml" />
		<mapper resource="mybatis/ResourceMapper.xml" />
		<mapper resource="mybatis/StudentMapper.xml" />
		<mapper resource="mybatis/MatchinfoMapper.xml" />
		<mapper resource="mybatis/CustomMapper.xml" />
		<mapper resource="mybatis/ReportMapper.xml" />
		<mapper resource="mybatis/SystemMessageMapper.xml" />
		<mapper resource="mybatis/CellinfoMapper.xml" />
		<mapper resource="mybatis/TaskMapper.xml" />
		<mapper resource="mybatis/UserDemoMapper.xml" />
		<mapper resource="mybatis/UserDemo10Mapper.xml" />
	</mappers>
</configuration>
```

### mybatis mapper配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.hjcrm.entity">

	<!-- 查询所有部门 -->
	<select id="queryDept" parameterType="java.util.Map" resultType="com.hjcrm.system.entity.Dept">
		select 
			d.deptid,d.deptparaid,d.deptname,d.deptcode,d.create_id,d.create_time,d.update_id,d.update_time,
			d.dr,
			case when d.deptparaid is not null then
				(select hd.deptname from hj_dept hd where 1=1 and hd.dr = 0 and hd.deptid = d.deptparaid)
			end deptParaname
		from hj_dept d 
		where  1=1 and d.dr = 0
	</select>
	
</mapper> 

```

