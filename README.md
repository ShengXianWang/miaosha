# 系统介绍
本系统是使用SpringBoot开发的高并发限时抢购秒杀系统，除了实现基本的登录、查看商品列表、秒杀、下单等功能，项目中还针对高并发情况实现了系统缓存、降级和限流。

## 核心知识点：

1.分布式：nginx反向代理，分布式会话管理；
2.查询多级缓存：redis缓存，本地缓存，静态请求cdn，动态请求cdn，页面静态化；
3.交易泄压：缓存库存，交易异步化，异步化事务；
4.流量错峰：秒杀令牌，秒杀大闸，队列泄洪；
5.防刷限流：验证码，限流器，防黄牛；
6.性能测试：jmeter压测，压测优化；


## 框架

### 项目框架：

三种分层技术：项目结构分层，业务逻辑分层，领域模型分层；

### 性能压测框架：

1.云端部署，以体验企业级的开发流程；

2.tomcat内嵌的容器优化方案来解决线程瓶颈问题；通过管道优化方案来简化了非keepalive下的网络建联开销；

### 分布式扩展：

1.负载均衡设计；

2.水平扩展/垂直扩展；

### 查询优化之多级缓存：

1.多级缓存屏障系统，优化了系统查询能力；

2.对于读不到的问题，我们尽可能使用本地缓存，使用缓存预热机制；对于脏读，在业务层面需要容忍商品详情页和数据库的不一致；

3.越近越好的缓存的概念，把缓存推到离用户最近的地方；

### 查询优化之页面静态化

1.cdn的静态分发网络

2.一切皆页面，一切皆静态的系统部署方式；

### 交易技术值缓存库存

1.交易验证：性能和正确性的权衡，要保证性能和用户的体验感，读的缓存不一定那么严格，反之要保证正确性，性能就需要相应的让步；

2.库存模型：在性能和可用性之间的权衡；

### 交易优化值事务型消息：

1.ACID vs CAP vs BASE

2.使用事务型消息完成了最终一致性的建设；

### 流量错峰技术：

1.防洪峰，防击穿

2.防浪费，使用库存的映射，防止请求流量浪费过度泄洪到对应的下游订单系统

3.排队，使用户的请求均匀的进入集群当中；

### 防刷限流：

1.错峰；

2.限流；

3.防黄牛；


## 项目特点

在阿里云上进行部署，以及利用jmeter对交易性能进行压测
利用分布式扩展提高交易性能；
利用多级缓存策略、页面静态化，对查询性能进行优化；
利用缓存库存、事务型消息，优化交易性能；
利用秒杀令牌、秒杀大闸、队列泄洪策略，实现流量的错峰；
引入验证码，限流器，防黄牛策略，实现秒杀活动的防刷限流；

## 目录介绍

- html：有前端的页面以及相应的样式；
- miaosha.sql: 创建mysql数据库；
- miaosha： 是一款基于SpringBoot+maven+redis的前后端分离秒杀系统;
	- pom.xml：添加maven依赖;
	- src\main\resources：秒杀系统的资源文件；
		- application.properties: 配置属性，包括端口，jdbc连接，druid数据源，redis依赖和jedis连接池；
		- mybatis-generator.xml: 连接mysql数据库，并对数据库中的表创建映射关系，生成DOMapper文件；
		- mapping: 利用mybatis-generator.xml生成的映射文件，利用DOMapper.xml文件可以生成由数据库直接映射的java的DOMapper文件。
	- src\main\java\com\imooc\miaoshaproject： 秒杀系统的主文件，包含启动类，三层数据模型，错误和异常的处理，其包含以下文件；
		- controller：资源控制类，用于与前端之间的交互，包含商品，订单和用户的交互，其viewobject是直接用于在前端展示的数据模型。
		- dao：包含相应的Mapper的java文件，有相应的增删改查函数；
		- dataobject：由数据库直接映射而来的数据模型；
		- error：用于对错误和异常的处理；
		- mq：创建订单，同步库存扣减等；
		- response：三层数据模型之间的转换；
		- service：服务类函数的封装，包含用户注册，商品创建，订单创建，秒杀活动开启等相关服务；
			- Impl：用于服务类函数的实现；
			- Model：用于后端处理的数据模型；
		- App: 秒杀系统启动类；


## 技术选型

### 系统架构图

![image text](./Frame.jpg)

### 后端技术

|      技术      |          说明           |                             官网                             |
| :------------: | :---------------------: | :----------------------------------------------------------: |
|   SpringBoot   |         MVC框架         | [ https://spring.io/projects/spring-boot](https://spring.io/projects/spring-boot) |
||  MyBatis-Plus  |         ORM框架         |                   https://mp.baomidou.com/                   |
|    RabbitMQ    |        消息队列         |   [ https://www.rabbitmq.com/](https://www.rabbitmq.com/)    |
|     Redis      |       分布式缓存        |                      https://redis.io/                       |
|     Docker     |       容器化部署        |      [ https://www.docker.com](https://www.docker.com/)      |
|     Druid      |      数据库连接池       | [ https://github.com/alibaba/druid](https://github.com/alibaba/druid) |
|     Nginx      | HTTP和反向代理web服务器 |                      http://nginx.org/                       |


### 开发环境

|     工具      | 版本号 |                             下载                             |
| :-----------: | :----: | :----------------------------------------------------------: |
|      JDK      |  1.8   | https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html |
|     Maven     | 3.3.9  |                   http://maven.apache.org/                   |
|     MySQL     |  8.1   |                    https://www.mysql.com/                    |
|   RabbitMQ    | 3.8.4  |            http://www.rabbitmq.com/download.html             |
|     Redis     | 6.6.0  |                  https://redis.io/download                   |
|   openresty   |1.17.8.2|            https://openresty.org/cn/download.html            |

## 秒杀优化方向

1. 将请求尽量拦截在系统上游：传统秒杀系统之所以挂，请求都压倒了后端数据层，数据读写锁冲突严重，几乎所有请求都超时，流量虽大，下单成功的有效流量甚小，我们可以通过限流、降级等措施来最大化减少对数据库的访问，从而保护系统。

2. 充分利用缓存：秒杀商品是一个典型的读多写少的应用场景，充分利用缓存将大大提高并发量

## 实现技术点

### 1. session共享
验证用户账号密码都正确情况下，通过UUID生成唯一id作为token，再将token作为key、用户信息作为value模拟session存储到redis，同时将token存储到cookie，保存登录状态

好处： 在分布式集群情况下，服务器间需要同步，定时同步各个服务器的session信息，会因为延迟到导致session不一致，使用redis把session数据集中存储起来，解决session不一致问题。

### 2. 全局异常统一处理
通过拦截所有异常，对各种异常进行相应的处理，当遇到异常就逐层上抛，一直抛到最终由一个统一的、专门负责异常处理的地方处理，这有利于对异常的维护。

### 3. 页面缓存 + 对象缓存
1. 页面缓存：通过在手动渲染得到的html页面缓存到redis
2. 对象缓存：包括对用户信息、商品信息、订单信息和token等数据进行缓存，利用缓存来减少对数据库的访问，大大加快查询速度。

### 4. 页面静态化
对商品详情和订单详情进行页面静态化处理，页面是存在html，动态数据是通过接口从服务端获取，实现前后端分离，静态页面无需连接数据库打开速度较动态页面会有明显提高

### 5. 本地标记 + redis预处理 + RabbitMQ异步下单 + 客户端轮询
描述：通过三级缓冲保护，1、本地标记  2、redis预处理  3、RabbitMQ异步下单，最后才会访问数据库，这样做是为了最大力度减少对数据库的访问。

实现：

1. 在秒杀阶段使用本地标记对用户秒杀过的商品做标记，若被标记过直接返回重复秒杀，未被标记才查询redis，通过本地标记来减少对redis的访问
2. 抢购开始前，将商品和库存数据同步到redis中，所有的抢购操作都在redis中进行处理，通过Redis预减少库存减少数据库访问
3. 为了保护系统不受高流量的冲击而导致系统崩溃的问题，使用RabbitMQ用异步队列处理下单，实际做了一层缓冲保护，做了一个窗口模型，窗口模型会实时的刷新用户秒杀的状态。
4. client端用js轮询一个接口，用来获取处理状态

### 6. 解决超卖
描述：比如某商品的库存为1，此时用户1和用户2并发购买该商品，用户1提交订单后该商品的库存被修改为0，而此时用户2并不知道的情况下提交订单，该商品的库存再次被修改为-1，这就是超卖现象

实现：

1. 对库存更新时，先对库存判断，只有当库存大于0才能更新库存
2. 对用户id和商品id建立一个唯一索引，通过这种约束避免同一用户发同时两个请求秒杀到两件相同商品
3. 实现乐观锁，给商品信息表增加一个version字段，为每一条数据加上版本。每次更新的时候version+1，并且更新时候带上版本号，当提交前版本号等于更新前版本号，说明此时没有被其他线程影响到，正常更新，如果冲突了则不会进行提交更新。当库存是足够的情况下发生乐观锁冲突就进行一定次数的重试。

### 7. 使用数学公式验证码
描述：点击秒杀前，先让用户输入数学公式验证码，验证正确才能进行秒杀。

好处：
1. 防止恶意的机器人和爬虫 
2. 分散用户的请求

实现：
1. 前端通过把商品id作为参数调用服务端创建验证码接口
2. 服务端根据前端传过来的商品id和用户id生成验证码，并将商品id+用户id作为key，生成的验证码作为value存入redis，同时将生成的验证码输入图片写入imageIO让前端展示
3. 将用户输入的验证码与根据商品id+用户id从redis查询到的验证码对比，相同就返回验证成功，进入秒杀；不同或从redis查询的验证码为空都返回验证失败，刷新验证码重试


## 遇到的bug问题

1.错误：(MYSQL错误解决)本地计算机上的MYSQL服务启动停止后,某些服务在未由其他服务或程序使用时将自动停止

https://www.cnblogs.com/xujuntao/p/12530735.html

2.install/Remove of the Service Denied

解决方法：打开cmd.exe,然后以管理员身份运行；

3.在MySQL登录时出现Access denied for user 'root'@'localhost' (using password: YES) 拒绝访问，并可修改MySQL密码

https://blog.csdn.net/qq_36675754/article/details/81381341

4.You have an error in your SQL syntax;  "set password for ‘root’@‘localhost’=password(‘123456’);"

解决方法：set password for root@localhost = '123456'

5.unable to load authentication plugin 'caching_sha2_password'

解决方法一：在my.ini文件中，将default_authentication_plugin=caching_sha2_password,更新为default_authentication_plugin=mysql_native_password
解决方法二：在依赖中，将‘mysql-connector-java’的'<version>5.1.41</version>'更新为'<version>8.0.11</version>'

6.The server time zone value 'XXXXX' is unreconginzed or represents

解决方法：在url路径上添加"?serverTimezone=UTC";如"url=jdbc:mysql://127.0.0.1:3306/bbs?serverTimezone=UTC"

7.java常见问题：incompatible types

解决方法：出现该种错误的原因是因为类型不兼容；而检查代码后，又认为代码没有问题；发生这种问题，一般都是引入包错误导致，因为引入了错误的包，导致使用的类出错。检查所引入的包。

8.method ="POST"

错误描述：incompatible types:required:org.springframework.web.bind.annotion.RequestMethod[]

解决方法:method = {RequestMethod.POST}

9.jquery-1.11.0.min.js:4 POST http://localhost:8090/user/getotp net::ERR_CONNECTION_REFUSED

解决方法：前端和后端，端口不一致

10.http错误："status":404,"error":"Not Found", "message":"No message available", "path":""

解决方法：这是因为后端程序控制器中，未添加ResponseBody，未对前端做出反应

11.Add braces to 'if' statement

解决方法：缺少if语句的大括号

12.throw new BusinessException错误

解决方法：需要抛出异常

13.以下情况异常，版本异常
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.7</version>      
        </dependency>
解决方法：①没有联网；②应该将依赖项放在主依赖项中，而非其他子结构的依赖项中

14.Loading XML bean definitions from class path resource [org/springframework/jdbc/support/sql-error-codes.xml]

解决方法：sql语句错误，需要修正；可能是属性为not null，但却没有添加元素；此外在xml文件中，应该添加，自增属性：如：keyProperty="id" useGeneratedKeys="true"

15.validate (javax.xml.transform.Source) in Validator be applied to (java.lang.Object)；以及Incompatible types:Required:javax.xml.validation.Validator,Foundjavax.validation.Validator:

解决原因：坑能是因为，Validator的包不对，利用javax.validation.Validator;来替换javax.xml.validation.Validator;

16.Description:Field itemService in com.miaoshaproject.controller.ItemController required a bean of type 'com.miaoshaproject.service.ItemService' that could not be found.Action:Consider defining a bean of type 'com.miaoshaproject.service.ItemService' in your configuration.

解决方法：这是因为在定义ItemService接口后，定义ItemServiceImpl类实现接口的过程中，并没有添加@Service注解，因此导致了在控制器ItemController中，利用ItemService定义实例时，该类型出错；

17.前端页面出问题了；error:function(data){alert("Fail creation，the reason is"+data.responseText);}

解决方法：这是前端后端跨域访问出错误了；具体需要注意如下问题；
	  ①在前端页面中，需要在$ajax({})中添加"xhrFields:{withCredentials:true},"表示前端同意跨域访问；
	  ②在后端程序中，需要在控制器前，添加@CrossOrigin(origins = {"*"}, allowCredentials = "true")
	  ③查看是否出错，查看控制器注解，和请求映射的注解是否出错；@Controller("/***")；@RequestMapping("/***")

19.Whitelabel Error Page:This application has no explicit mapping for /error, so you are seeing this as a fallback.There was an unexpected error (type=Unsupported Media Type, status=415).Content type 'null' not supported

解决方法;在@RequestMapping()注解中，去除掉consumes={CONTENT_TYPE_FORMED}这个选项即可

20.else{alert("登陆失败，原因为"+data.data.errMsg);}

解决方法：这是前端的数据已经成功传输给后端，但后端因为其他原因导致操作不成功；有可能xml文件中缺少相应的sql语句；

21.telnet 121.199.29.161。。。Trying 121.199.29.161...telnet: connect to address 121.199.29.161: Connection refused

解决方法：这是因为服务器并没有给应用服务器权限去访问连接
> mysql -uroot -p123455
> use mysql;
> select host,user,password from user;
> grant all privileges on *.* to root@'%' identified by '123456'
> flush privileges

22.在云端部署完成，并成功运行jar文件后，在浏览器输入相应url却无法成功输出；

解决方法：这是因为阿里云服务器默认没有开启8090端口，需开启端口。①寻找到安全组》配置规则》安全组规则》手动添加》授权策略：允许，优先级：1，协议类型：TCP类型，端口范围：2000/9999，授权对象：0.0.0.0/0

23.在nginx反向代理设置中。。应用服务器连接服务器需要注意的问题。。。

解决方法：①购买后服务器后，修改密码，重启服务器。
	  ②设置端口范围。
  	  ③在连接服务器后，务必需要注意，在application.properties的文件中，添加url路径；//我这里url路径为：spring.datasource.url=jdbc:mysql://（主机名）:3306/misosha?serverTimezone=UTC
	  ④配置好url路径后，务必记住重启文件application.properties。而因为application.properties在deploy.sh中描述了，所以。。。
	  ⑤重启路径：./deploy.sh &；；//记住，这个步骤是为了启动服务器。。。
 	  ⑥可以通过:tail -f nohup.out查看启动日志，启动日志里描述了mysql的启动过程。。。。

24. Unable to load authentication plugin 'caching_sha2_password

解决方法：将老版本改成新版本。将“5.0.11”改成“8.0.11”
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.11</version>
</dependency>

25.openresty-1.13.6.2.tar【更多IT教程: No such file or directory微信a_xue1995】.gz: No such file or directory

解决方法：在linux系统中，最好不要使用中文的压缩包名。。。

26.the HTTP rewrite module requires the PCRE library

解决方法：yum install pcre-devel openssl-devel gcc curl

28. 当云服务器中修改项目maven中的application.properties,但是没有效果。。。

解决方法：需要重新运行该项目;java -jar miaosha.jar

29. 在nginx中修改默认配置。。。。但是并没有效果。。。且一直报nginx: [emerg] bind() to 0.0.0.0:8090 failed (98: Address already in use) 错误解决
    //server之前添加
    upstream backend_server{
        server 172.16.86.64 weight=1;
        server 172.16.55.3 weight=1;
    }

	//server中添加
        location / {
            proxy_pass http://backend_server;
            proxy_set_header Host $http_host:$proxy_port;   //务必注意nginx的默认端口为80，而不是8090
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

解决方法：需要统一使用nginx的默认端口80，而不是其他的端口。。。。因此。。。如果使用nginx时，尽量使用其默认端口80。如果使用其他端口，注意可能需要调试bug。。

30. Error creating bean with name 'enableRedisKeyspaceNotificationsInitializer' defined in class path resource

解决方法：①经查找，在Java代码中，我们使用了注解：@EnableRedisHttpSession，这个注解是用来开启Redis来集式式管理Session。
	  而在使用这种方式的时候，是需要Redis开启Keyspace Notifications功能的，默认是关闭的。
	  这个功能有一个参数来控制它，notify-keyspace-events，值为Egx。
  	  可以通过在Redis.Config中配置。
 	  也可以通过命令行来配置，如下所示：  

31.2020-07-17 10:44:29.126 ERROR 28480 --- [eate-1499136125] com.alibaba.druid.pool.DruidDataSource   : create connection error, url: jdbc:mysql://127.0.0.1:3306/miaosha?useUnicode=true&characterEncoding=UTF-8, errorCode 1045, state 28000
java.sql.SQLException: Access denied for user 'root'@'localhost' (using password: YES)

原因：①有可能是账户密码错误
		在mysql系统外，使用mysqladmin
		>mysqladmin -u root -p password "test123"
		通过登录mysql系统，
		
		Enter password: 【输入原来的密码】
		mysql>use mysql;
		mysql> update user set password=passworD("test") where user='root';
		mysql> flush privileges;
		mysql> exit;      
      ②有可能是用户没有查看的权限。。需要在mysql数据库中添加sql语句：
		>grant all privileges on *.* to root@'%' identified by 'root'
      ③其他原因。。。。。。。。。。。。

32.org.springframework.data.redis.RedisConnectionFailureException:Cannot connect 127.0.0.1:3306

原因：有可能是redis.conf文件中，bind:127.0.0.1被注释。。或者被其他的地址代替，如被bind:172.16.186.195代替
 
解决方法：①可以重新使用bind:127.0.0.1  ②在application.properties中添加：spring.redis.host=172.16.186.195

## 致谢

本项目是学习了imooc网视频之后的个人理解和知识汇总，学习链接：https://coding.imooc.com/class/168.html

- 感谢**陌溪/蘑菇博客**的gitee模板：[https://gitee.com/moxi159753/mogu_blog_v2]
- 感谢**qiurunze123**的github模板：(http://github.com/qiurunze123/miaosha)
- 感谢**zaiyunduan123**的github：[http://github.com/qiurunze123/springboot-seckill)


