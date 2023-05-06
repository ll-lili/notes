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
  n et stop mysql # 关闭
  ```

* 登录

  ```shell
  mysql -u root -p
  # 根据提示输入密码。
  ```

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

#### mySQL常用操作

* 查询版本

  ```mysql
  SELECT VERSION()
  ```

* 创建数据库

  ```mysql
  CREATE DATANASE testdb;
  ```

* 显示所有数据库

  ```mysql
  SHOW DATABASES;
  ```

* 选择数据库

  ```mysql
  USE testdb;
  ```

* 显示所有表

  ```mysql
  SHOW TABLES;
  ```

* 插入数据

  ```mysql
  INSERT INTO users (username, `password`, realname) VALUES ('lishi', '123456', '李四')
  ```

* 查询数据

  ```mysql
  SELECT * FROM users; # 查询users表的所有数据
  SELECT id, username FROM users; # 查询指定列的数据
  SELECT * FROM users WHERE username = 'zhangsan' # 按条查询
  SELECT * FROM users WHERE username = 'zhangsan' and `password` = '123456' #
  SELECT * FROM users WHERE username = 'zhangsan' or `password` = '123456' #
  SELECT * FROM users WHERE username like '%s%' # 模糊查询
  SELECT * FROM users WHERE username like '%s%' order by id desc # 模糊查询，倒序
  
  # password为关键词，加``
  ```

* 更新数据

  ```mysql
  UPDATE users SET realname = '李斯' WHERE username = 'lisi'
  ```

* 删除数据

  ```mysql
  # 真删除
  DELETE FROM users WHERE username = 'lisi'
  
  # 软删除：添加一列state默认值等于1
  UPDATE users SET state = 0 WHERE	username = 'lisi';
  SELECT * FROM	users WHERE	state <> 0;
  
  ```

  

#### nodejs操作mySQL

* `mysql`连接mySQL8失败,也可以使用`mysql2`模块操作mySQL

  ```mysql
  # 新的mysql模块并未完全支持MySQL8的“caching_sha2_password”加密方式，而“caching_sha2_password”在MySQL 8中是默认的加密方式。
  
  # 下面的方式命令是默认已经使用了“caching_sha2_password”加密方式，该账号、密码无法在mysql模块中使用。
  ALTER USER 'root'@'localhost' IDENTIFIED BY '123456'; 
  
  
  # 解决方法是从新修改用户root的密码，并指定mysql模块能够支持的加密方式：
  ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
  # 刷新权限
  FLUSH PRIVILEGES;
  ```

  

```javascript
/**
	npm install mysql
*/
const mysql = require('mysql')

const connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '123456',
  port: '3306',
  database: 'myblog'
})
connection.connect()
const sql = 'select * from users;'
connection.query(sql, (error, result) => {
  if (error) {
    console.log(error)
    return
  }
  console.log(result)
})
connection.end()
```

