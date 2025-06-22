

## @注解 （参数，方法，类注解）

EnableWebMvc    配置类//json数据转换成对象功能 根据类型匹配对应转换器

RequestParam  //参数绑定    

RequestBody // 请求体

ResponseBody  //响应体 方法注解设置当前控制器返回值为响应体

DateTimeFormat(pattern="yyyy-mm-dd HH:mm:ss")

ComponentScan(    {"    ","   "} ) 多个扫描

## Rest风格 restful

请求动作get post put delete等八种，路径变量 占位请求路径，

```java
@RequestMapping(value="/users/{id}",method=RequestMethid.DELETE)
@ResponseBody
public String delete(@PathVariable Integer id)
```

post新增，put修改，get得到全部对象，get得到某一个对象   删除  

### 简化开发Rest

将Controller和ResponseBody 合为一个注解RestController

RequestMapping 变为 PostMapping

## 有关数据库

sql语句

insert into  表名 （列名，...   ）values(#{type}, ...)

update 表名 set 类名=#{}，... ,  ...   ,where id = #{id}

```
jdbc:mysql://localhost:3306/taohuo?useUnicode=true&characterEncoding=UTF-8&useSSL=false
com.mysql.cj.jdbc.Driver
```

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 指定mybatis全局配置文件的位置 -->
		<property name="configLocation" value="classpath:mybatis-config.xml"></property>
		<property name="dataSource" ref="pooledDataSource"></property>
		<!-- 指定mybatis，mapper文件的位置 -->
		<property name="mapperLocations" value="classpath:mapper/*.xml"></property>
	</bean>

	<!-- 配置扫描器，将mybatis接口的实现加入到ioc容器中 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<!--扫描所有dao接口的实现，加入到ioc容器中 -->
		<property name="basePackage" value="com.atguigu.crud.dao"></property>
	</bean>
	
	<!-- 配置一个可以执行批量的sqlSession -->
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"></constructor-arg>
		<constructor-arg name="executorType" value="BATCH"></constructor-arg>
	</bean>
```

```java
 @Value("${mybatis-plus.mapper-locations}")
    private String mapperLocations;
    //配置FactoryBean
    @Bean(name = "sqlSessionFactoryBean")
    public SqlSessionFactoryBean sqlSessionFactoryBean() {
        SqlSessionFactoryBean sqlSessionFactoryBean = null;
        try {
            // 加载JNDI配置
            Context context = new InitialContext();
            // 实例SessionFactory
            sqlSessionFactoryBean = new SqlSessionFactoryBean();
            // 配置数据源
            sqlSessionFactoryBean.setDataSource(dataSource());
            // 加载MyBatis配置文件
            PathMatchingResourcePatternResolver resourcePatternResolver = new PathMatchingResourcePatternResolver();
            // 能加载多个，所以可以配置通配符(如：classpath*:mapper/**/*.xml)
            sqlSessionFactoryBean.setMapperLocations(resourcePatternResolver.getResources(mapperLocations));
            // 配置mybatis的config文件(我目前用不上)
            // sqlSessionFactoryBean.setConfigLocation("mybatis-config.xml");
        } catch (Exception e) {
            System.out.println("创建SqlSession连接工厂错误：{}");
        }
        return sqlSessionFactoryBean;
    }
    @Bean
    public SqlSessionTemplate sqlSessionTemplate() throws Exception {
        SqlSessionTemplate sqlSessionTemplate=new SqlSessionTemplate(sqlSessionFactoryBean().getObject(),ExecutorType.BATCH);
        return sqlSessionTemplate;
    }

```

## 异常处理器

```java
@RestControllerAdvice
pubic class ProjectExceptionAdvice{
    @ExceptionHandler(Exception.class)
    public 数据类型 方法名 (Exception ex){
        
    }
}
```

## 拦截器(Interceptor)

在指定的方法调用前后预定设定的代码

阻止原始方法的执行

## 过滤器（Servlet技术）filter

