# Redis数据类型

## 基本数据类型

String Hash List Set SortedSet

## 特殊类型

GEO BitMap HyperLog

# Redis通用命令

keys 查看符合模版的所有key，不建议在生产环境设备上使用

del 删除一个指定的key

exists  key是否存在

==expire== 给一个key设置一个有效期，有效期到期时该key会被自动删除

==ttl==查看一个key的有效期 当key值是-1永久有效，-2失效消逝

# String类型

是Redis中最简单的存储类型

String：普通字符串

Int：整数类型，可以做自增，自减操作

float 浮点类型，可以做自增，自减操作

不管是那种格式，底层都是字节数组形式存储，只不过编码方式不同。字符串类型的最大空间不能超过512m

## 命令

set 添加或者修改String类型

get获得获取value值

mset 批量新增加

mget 批量获取value

incr 让一个整型的key自增1

incrby 让一个整型的key自增并指定步长

incrbyfloat 让一个浮点类型的数字自增并指定步长

setnt 添加一个String类型的字符串，key不存在插入成功

sentex 添加一个String类型的字符串，并且添加有效期 

# key的层级格式

在java中可以将对象序列化为json字符串后存储

# Hash类型

wei:user:1 {name:"jack",age:21}

hash结构可以将对象中的每个字段独立存储，单独针对一个字段进行修改

## Hash类型命令

==hset key field value==

heima:user:3 name jack;	heima:user:3 age 18;

==hget key field==

==hmset key field value field value==

==hmget key field field==

==hgetall==

==hkeys==

==hvals==

==hincrby==	hincrby heima:user:4 field 2

==hsetnx==       添加一个hash类型的key的field值，前提是这个field不存在，否则不执行。

# List类型

不同类型就是在值的方向上有不同，底层可以看成双向链表

- 有序
- 元素可以重复
- 插入和删除快
- 查询速度一般

## 命令

lpush key element ...	rpush key element ...

lpop key	rpop 

lrange key star end 返回一段角标范围内的所有元素

blpop	brpop	brpop 只不过在没有元素时等待指定时间，而不是直接返回nil	阻塞

blpop key element

# Set类型

类似于HashSet 可以看做是一个value为null的HashMap。因为也是一个hash表，因此与HashSet类似的特征：

- 无序
- 元素不可重复
- 查找快
- 支持交集、并集、差集等功能

## 命令

sadd key member ... 	向set中添加一个或者多个元素

srem key member ...	移除set中的指定元素

scard key 返回set中元素的个数

sismember key member	判断一个元素是否存在于set中

smembers 获取set中的所有元素

sinter key1 key2	求key1于key2的交集

sdiff key1 key2	求key1与key2的差集

sunion key1 key2	求key1和key2的并集

# SortedSet可排序集合

TreeSet类型，但底层TreeSet是红黑树

SortedSet的每个元素都带有一个score属性，可以基于score属性对元素排序，底层的实现是一个跳表加上hash表

- 可排序
- 元素不重复
- 查询速度快

zadd key score member	添加一个或者多个元素，如果存在就更新score值

zrem key member	删除一个指定元素

zscore key member	获取指定元素的score

zrank key member  获取元素排名 从低到高	

zrevrank key member	获取元素排名 从高到低

zcard key 	获取元素的个数

zcount key  min max 	统计score值在给定范围内的所有元素个数

zincrby key increment member 让集合中的指定元素自增，步长为指定的值

zrange key min max	按照score排序后，获取指定的排名范围的元素

zrangebyscore key min max	按照score排序后，获取指定score范围内的元素

zdiff	zinter	zunion	差集、交集、并集

# jedis



# SpringDataRedis

自带的是lettuce ，并不是jedis。jedis线程不安全。

```java
package com.hmdp.service.impl;

import cn.hutool.core.bean.BeanUtil;
import cn.hutool.core.bean.copier.CopyOptions;
import cn.hutool.core.lang.UUID;
import cn.hutool.core.util.RandomUtil;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.hmdp.dto.LoginFormDTO;
import com.hmdp.dto.Result;
import com.hmdp.dto.UserDTO;
import com.hmdp.entity.User;
import com.hmdp.mapper.UserMapper;
import com.hmdp.service.IUserService;
import com.hmdp.utils.RegexUtils;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import javax.servlet.http.HttpSession;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;

import static com.hmdp.utils.RedisConstants.*;
import static com.hmdp.utils.SystemConstants.USER_NICK_NAME_PREFIX;

/**
 * <p>
 * 服务实现类
 * </p>
 *
 * @author 虎哥
 * @since 2021-12-22
 */
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements IUserService {
    @Resource
    private StringRedisTemplate stringRedisTemplate;
    @Override
    public Result sendCode(String phone, HttpSession session) {
        //1.校验手机号，
        if (RegexUtils.isPhoneInvalid(phone)) {
            //2.如果不符合打回，返回一个错误信息
            return Result.fail("手机号格式错误");
        }
        //3.符合就生成，并保存验证码(redis保存)
        String code = RandomUtil.randomNumbers(6);
        stringRedisTemplate.opsForValue().set(LOGIN_CODE_KEY+phone,code,LOGIN_CODE_TTL, TimeUnit.MINUTES);
        log.debug("发送短信验证码成功，验证码：{}"+code);
        //4.发送验证码

        return Result.ok();
    }

    @Override
    public Result login(LoginFormDTO loginForm, HttpSession session) {
        //1.校验手机号
        //2.校验验证码,从redis中拿到
        //3.不一致，报错
        //4.一致，根据手机号查询用户
        //5.判断是否存在，不存在创建将用户存入redis中；随机生成token，作为登录令牌；
        // 将user作为hash结构存储； 设置有效期
        // 返回token到客户端；
        String phone = loginForm.getPhone();
        if (RegexUtils.isPhoneInvalid(phone)) {
            return Result.fail("手机号格式有误！");
        }
        String cachecode = stringRedisTemplate.opsForValue().get(LOGIN_CODE_KEY+phone);
        String code = loginForm.getCode();
        if (cachecode == null || !cachecode.equals(code)) {
            return Result.fail("验证码错误");
        }
        User user = query().eq("phone", phone).one();
        if (user ==null) {
            user = createUserWithPhone(phone);
        }
        String token = UUID.randomUUID().toString(true);
        UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
        Map<String, Object> userMap =
                BeanUtil.beanToMap(userDTO,new HashMap<>(),
                        CopyOptions.create().setIgnoreNullValue(true)
                        .setFieldValueEditor
                                ((fieldName,fieldValue) -> fieldValue.toString()));
        String tokenKey = LOGIN_USER_KEY + token;
        System.out.println(tokenKey);
        stringRedisTemplate.opsForHash().putAll(tokenKey,userMap);
        stringRedisTemplate.expire(tokenKey,LOGIN_USER_TTL,TimeUnit.MINUTES);

        return Result.ok(token);
    }

    private User createUserWithPhone(String phone) {
        User user = new User();
        user.setPhone(phone);
        user.setNickName(USER_NICK_NAME_PREFIX + RandomUtil.randomString(10));
        save(user);
        return user;
    }


}

```

