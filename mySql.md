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
  et start mysql # 启动
  et stop mysql # 关闭
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

