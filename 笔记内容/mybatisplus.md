# mybatisplus

DML编程控制是数据库领域中的数据操作语言（Data Manipulation Language）。在数据库中，DML是用于查询和操作数据的语言，主要包括 SELECT、INSERT、UPDATE 和 DELETE 等命令。

在建立项目时不用勾选mybatis

```xml
<dependency>
	<groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
```

这个依赖的导入完成了mybatis整合spring，mybatis包都导完了不要在加，小心版本不同问题。

旨在开发效率，为快速开发为生，国人开发！！！

```java
@Mapper
public interface UserDao extends BaseMapper<User>{
    
}
```

## MybatisPlus特性

无侵入，只做增强不做改变。

强大的CRUD功能，内置通用Mapper

支持lambda：编写查询条件无需胆心字段写错

支持主键自动生成，内置分页插件。

分页拦截器

```java
public MybatisPlusInterceptor mpInterceptor(){
    1定义Mp拦截器;
    MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();
    在Mp的拦截器添加具体的拦截器
    mpInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
    return mpInterceptor;
} 
```

分页接口IPage<T>    selectPage(IPage<T> page)

## MybatisPlus日志

```yaml
mybatis-plus: 
 configuration: 
  log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
 global-config:
  banner: false //这个是那个运行的logo
spring:
 main: 
  banner-mode: off
  
```

查询条件学习

```java
@Test
void testGetAll(){
    //方式1：按条件查询
	QueryWrapper qw = new QueryWrapper();
    qw.lt("age",18);
    List<User> userList = userDao.selectList(qw);
    //方式2：lambda格式按条件查询
    //指定泛型
    QueryWrapper<User> qw = new QueryWrapper<user>();
    qw.lambda().lt(User::getAge,18); 
    //方式3：lambda格式
    LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
    lqw.lt(User::getAge,10).gt这里加也行 //&lt小于
    条件还能继续加；
    //这上面是与关系
    lqw.lt(User::getAge,10).or().gt
        
}

```

查询的东西不能为空

```
//模拟传过来的查询数据
UserQuery uq = new UserQuery();

查询条件不为空，因为一般的查询条件都是变量是人为传来的
lqw.lt(null ！= uq.getage(),User::getAge,10)
   .gt...
```

查询投影

```java
//lambda格式
lqw.select(User::getId,,); //显示哪几个就写啥
qw.select("id",,);//这种String
QueryWrapper<User> qw = new QueryWrapper<User>();
//数据总数
qw.select("count(*) as count");
//分组
qw.groupBy("id")
List<Map<String,Object>> userList = userDao.selectMaps(qw);

```

查询条件详细再来

```java
lqw.eq(User::getName,"jerry")
   .eq(User::getPassword,"jerry");
User loginUser = userDao.selectOne(lqw);
//范围查询 <= lt le gt ge eq between
//between
lqw.between(User::getAge,10,30);
//模糊查询
lqw.like(User::getName,"J");
	likeRight(User::getName,"J");J%这种匹配

```

## 字段映射与表名映射

@TableName("表名")

@TableField(value="列名",select = false)关联两边的关系,并且不让参与查询。

编码中添加了数据库中没有定义的属性

@TableField(exist = false)

## id生成策略

@TableId(type = IdType.AUTO)使用数据库id自增策略控制id生成

NONE 	不设置

INPUT	手工输入

ASSIGN_ID	雪花算法生成id 	雪花算法64位2进制 	占位符  41位时间戳  十位计算机的机器码  																	     	12位序列号

ASSING_UUID	以UUID生成算法作为id生成策略

全局配置id

```yaml
mybatis-plus:
 global-config:
  db-config:
   id-type: assign_id   主键自增策略
   table-prefix: tbl_	表名前缀
```

