#### windows下载安装`MySql`

* 下载页面:https://dev.mysql.com/downloads/mysql/

* 解压 ZIP 文件

*  在解压的文件夹下创建 `my.ini `配置文件

  ```ini
  [mysqld]
  # 设置3306端口 
  port=3306   
  
  # 设置mysql的安装目录
  basedir=D:\\Program Files (x86)\\mysql-8.0.32-winx64
  
  # 设置mysql数据库的数据的存放目录 
  datadir=D:\\Program Files (x86)\\mysql-8.0.32-winx64\\data  
  
  # 允许最大连接数  
  max_connections=200
  
  # 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
  max_connect_errors=10  
  ```

* 初始化 `MySQL`

  ```shell
  mysqld --initialize --console
  # 这会初始化默认的 MySQL 实例。
  # 会生成一个随机密码用来登录
  ```

* 安装 `MySQL `服务

  ```shell
  mysqld -install
  # 这会将 MySQL 安装为 Windows 服务。
  ```

* 启动 `MySQL` 服务

  ```shell
  net start mysql # 启动
  net stop mysql # 关闭
  ```

* 登录

  ```shell
  mysql -u root -p
  # 根据提示输入密码。
  ```

* ```shell
  show variables like 'character\_set\_%'
  
* 修改密码

  ```shell
  ALTER USER 'root'@'localhost' IDENTIFIED BY '123456'
  mysql -u root -p123456   # 使用新密码 123456 登录
  ```

* 忘记密码

  1. 停止` MySQL` 服务
  2. 执行 `mysqld --skip-grant-tables --console` 重启 `MySQL`,忽略权限表
  3. 在另一个终端执行 `FLUSH PRIVILEGES; ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';` 重置密码
  4. 在两个终端均执行 `quit` 退出 `MySQL`
  5. 启动` MySQL `服务,使用新密码登录
  6. 重置密码会使` MySQL `权限表恢复默认状态,你需要重新配置用户和权限。
  7. 上述方法只适用于本地 root 用户,对远程用户无效。如果你忘记远程 root 用户的密码,需要手动修改用户表。

#### mac(m1)下载安装`mySQL`

* 下载mysql-8.0.32-macos13-arm64.dmg，按照提示安装

* 设置环境变量

  ```shell
  open ~/.zshrc
  
  ## 末尾添加
  export PATH="/usr/local/mysql/bin:$PATH"
  export PATH=$PATH:/usr/local/mysql/support-files
  ```

* 开启停止重启命令

  ```shell
  sudo mysql.server start
  sudo mysql.server stop
  sudo mysql.server restart
  ```

* 遇到权限问题

  ```shell
  sudo chmod -R 777 /usr/local/mysql/data
  ```


#### 字段

字段名、字段类型、自增、是否位null、默认值


##### 字段类型

- bit：占一位，0或者1，false或者true
- int：占32位，整数
- decimal（M，N）：能精准计算的实数，M是总的数子位数，N是小数位数
- char(n)：固定长度位n的字符
- varchar(n)：长度可变，最大长度位n的字符
- text：大量的字符
- date：仅日期
- datetime：日期时间
- time：仅时间

#### 主键

- 唯一
- 不能更改
- 无业务含义

#### 外键

- 用于产生表关系的列
- 外键列回连接到另一张表

#### 表关系

- 一对一
- 一对多
- 多对多

#### 三大设计范式

1. 要求数据库表的每一项都是不可分割的原子数据项
2. 非主键列必须依赖于主键
3. 非主键列必须直接依赖主键列
#### mySQL常用操作

##### 查询版本

```mysql
SELECT VERSION();
```

##### 创建数据库

```mysql
CREATE DATANASE testdb;
```

##### 显示所有数据库

```mysql
SHOW DATABASES;
```

##### 选择数据库

```mysql
USE testdb;
```

##### 显示所有表

```mysql
SHOW TABLES;
```

##### 插入数据

```mysql
# 插入一条 ``为了区分关键字
INSERT INTO users (username, `password`, realname) VALUES ('lishi', '123456', '李四');
# 插入多条
INSERT INTO users (username, `password`, realname) VALUES ('lishi', '123456', '李四'),('lishi', '123456', '李四');
```

##### 更新数据

```mysql
UPDATE users SET realname = '李斯' WHERE id = '1';
```

##### 删除数据

```mysql
# 真删除
DELETE FROM users WHERE username = 'lisi';

# 软删除：添加一列state默认值等于1
UPDATE users SET state = 0 WHERE	username = 'lisi';
SELECT * FROM	users WHERE	state <> 0;

```

##### 单表查询数据

- select 后面查询字段
- from 后面数据源（表）
- where 后面查询条件
- order by 排序
- limit： n,m 跳过n条数据，取出m条数据

```mysql
SELECT * FROM users; # 查询users表的所有数据
SELECT id, username FROM users; # 查询指定列的数据
SELECT username as 'name', "新列" as "new" FROM users; # 查询指定列设置别名
SELECT distinct location FROM users; # distinct去重


SELECT realname AS "name",
CASE gender 
WHEN 1 THEN '男'
ELSE '女' 
END as sex
FROM users;# case进一步处理查询结果
	
SELECT realname AS "name",
case
when gender = 0 then '男'
when gender = 1 then '女'
else '未知'
end as sex
FROM users;# case进一步处理查询结果



 # 按条查询
SELECT * FROM users WHERE username = 'zhangsan';
SELECT * FROM users WHERE username = 'zhangsan' and `password` = '123456'; # and
SELECT * FROM users WHERE username = 'zhangsan' or `password` = '123456'; # or
select * from users where id in (1, 2); # 范围
select * from users where id between 1 and 3; # 范围
select * from users where location is null; # 查询null
select * from users where location is not null; # 查询 not null
SELECT * FROM users WHERE username like '%s%'; # 模糊查询
SELECT * FROM users WHERE username like '%s%' order by id desc; # 模糊查询，desc：倒序； asc：升序

# password为关键词，加``
```

##### 联表查询

- 笛卡尔积 ...from a,b
- 左连接，左外连接，left join ...from a left join b
- 右连接，右外连接，right join
- 内连接，inner join ...from a inner join b

```shell
# ...from a,b
```

##### 函数

- 数学
  - ABS(x) 返回x的绝对值
  - CEILING 向上取整
  - FLOOR 向下取整
  - MOD（x, y） 返回x/y的模（余数）
  - PI() 返回pi的值（圆周率）
  - RAND() 返回0到1的随机数
  - ROUND（x,y） 返回参数x的四舍五入的有y位的小数的值
  - TRUNCATE（x,y）返回数字x截断短y位的小数的结果
- 聚合
  - AVG(col) 返回指定列的平均子
  - COUNT(col) 返回指定列中非NULL值的个数
  - MIN（col） 返回指定列的最小值
  - MAX(col)
  - SUM(col)
- 字符
  - CONCAT(s1, s2,...,Sn) 连接字符串
  - CONCAT_WS(sep,s1, s2,...,Sn) 连接字符串,并用sep字符间隔
  - TRIM（str）去除字符串首尾的所有空格
  - LTRIM（str）从字符串str中切掉开头的空格
  - RTRIM（str）
- 日期
  - CURDATE()或者CURRENT_DATE（）　返回当前日期
  - CURTIME()或者CURRENT_TIME（）　返回当前时间
  - TIMESTAMPDIFF（part, date1,date2）返回date1到date2之前相隔的part值，part是用于指定的相隔的年 月  日

##### 分组

- 分组后，只能查询分组的列和聚合列
- 运行顺序： 
  - form
  -  join..on.. 
  -  where 
  - group by
  - select
  - having
  - orderby
  - limit

##### 视图

理解：方便查询

#### nodejs操作mySQL

`mysql`连接mySQL8失败,也可以使用`mysql2`模块操作mySQL

```mysql
# 新的mysql模块并未完全支持MySQL8的“caching_sha2_password”加密方式，而“caching_sha2_password”在MySQL 8中是默认的加密方式。

# 下面的方式命令是默认已经使用了“caching_sha2_password”加密方式，该账号、密码无法在mysql模块中使用。
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456'; 


# 解决方法是从新修改用户root的密码，并指定mysql模块能够支持的加密方式：
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
# 刷新权限
FLUSH PRIVILEGES;
```

##### mysql2

https://github.com/sidorares/node-mysql2/tree/master/documentation/zh-cn

>  防止sql注入
>
>  用户通过注入sql语句到最终查询中，导致影响查询结果
>
>  msyql支持变量，变量不作为任何sql语句
>
>  使用execute不使用query

##### ORM

ORM：对象关系映射

通过ORM框架，可以自动的吧程序中的对象和数据库关联

ORM框架会隐藏具体的数据库底层细节，让开发者使用同样的数据操作接口，完成对数据库的操作

node中的ORM：Sequelize TypeORM

##### Sequelize

https://www.sequelize.cn/

>  报错：#initializeTTLTracking() {
>
> 肯能是node mysql2版本问题

```shell
#安装
npm install --save sequelize
# 你还必须手动为所选数据库安装驱动程序：
npm install --save mysql2
```

```js
// model/db.js
const { Sequelize } = require('sequelize')
const sequelize = new Sequelize('test', 'root', '123456', {
  host: 'localhost',
  dialect: 'mysql'
})

module.exports = sequelize

// index.js 测试连接
const sequelize = require('./models/db')
;(async () => {
  try {
    await sequelize.authenticate()
    console.log('Connection has been established successfully.')
  } catch (error) {
    console.error('Unable to connect to the database:', error)
  }
})()
```

###### 模型定义同步

- `User.sync()` - 如果表不存在,则创建该表(如果已经存在,则不执行任何操作)
- `User.sync({ force: true })` - 将创建表,如果表已经存在,则将其首先删除
- `User.sync({ alter: true })` - 这将检查数据库中表的当前状态(它具有哪些列,它们的数据类型等),然后在表中进行必要的更改以使其与模型匹配.

```js
// model/User.js
const sequelize = require('./db.js')
const { DataTypes } = require('sequelize')

const User = sequelize.define(
  'User',
  {
    username: {
      type: DataTypes.STRING(100),
      allowNull: false
    },
    password: {
      type: DataTypes.STRING(100),
      allowNull: false
    }
  },
  {
    // freezeTableName: true 强制表名等于模型名
    // tableName: 'Employees' 定义表名
    // timestamps: false
    paranoid: true // 该表的数据不会真正删除，而是增加一列deletedAt, 记录散出时间
  }
)
;(async function () {
  await User.sync({ alter: true })
  console.log('同步User表')
})()
module.exports = User

/*
一次同步所有模型
await sequelize.sync({ force: true });
console.log("所有模型均已成功同步.");
*/

```



#### 模拟数据（mockjs）

```shell
# 安装 http://mockjs.com/
npm install mockjs
```

#### 数据抓取

```js
const Book = require('../models/Book')
const superagent = require('superagent')
const cheerio = require('cheerio')

class FetchBooks {
  URL = ''
  data = []
  constructor(url) {
    this.URL = url
  }
  /**
   * 获取html
   * @returns string
   */
  async getHtml() {
    const { text } = await superagent.get(this.URL)
    return text
  }
  /**
   * 获取目标信息
   * @returns []
   */
  async getTargetInfo() {
    const html = await this.getHtml()
    const $ = cheerio.load(html)
    const list = $('.chart-dashed-list .media')
    list.map((i, el) => {
      const arr = $(el).find('.media__body .subject-abstract').text().split('/')
      this.data.push({
        name: $(el).find('.media__body h2 a').text(),
        imgurl: $(el).find('.media__img img').attr('src'),
        publishDate: arr[1].trim(),
        author: arr[0].trim()
      })
    })
    this.saveDataToDb(this.data)
    console.log(this.data)
    return this.data
  }
  /**
   * 保存数据到数据库
   */
  async saveDataToDb(data) {
    await Book.bulkCreate(data)
    console.log('保存数据到数据库成功！')
  }
}
const ins = new FetchBooks(
  'https://book.douban.com/latest?subcat=%E5%85%A8%E9%83%A8&p=2'
)
ins.getTargetInfo()

```

#### moment

##### UTC时间和北京时间

- 相差8小时：UTC0点，北京8点

##### 时间戳（timestamp）

- 某个UTC时间到1970-1-1凌晨经过的毫秒数
- 时间戳表示的是UTC时间的差

#### 数据验证

##### 数据验证的位置

- 前端（客户端）：为了用户体验
- 路由层：验证接口格式是否正常
- 业务逻辑层：保证业务完整
- 数据库验证（约束）：保证数据完整性

##### 相关库

- validator：用于验证某个字符串是否满足规则
- validate.js: 用验证某个对象的属性是否满足规则

#### 访问器和虚拟字段

#### 日志记录

- level日志级别：信息日志 错误日志 调试日志
- category日志分类 ：sql日志 请求日志
- appender日志出口： 写到哪里，输出格式

##### log4js

