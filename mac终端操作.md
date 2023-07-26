#### 查看当前使用的shell

```shell
echo $SHELL

# 对应配置文件是 ~/.zshrc
```

#### 切换shell

* 切换到 zsh:输入 `zsh` 命令
* 切换到 bash:输入 `bash` 命令
* zsh 的配置文件是 `~/.zshrc`
* bash 的配置文件是 `~/.bash_profile`

#### 编辑shell的配置文件

*  编辑 `~/.zshrc`

  ```shell
  echo 'export PATH="/usr/local/mysql/bin:$PATH"' >> ~/.zshrc
  ```

* 编辑 `~/.bash_profile`

  ```shell
  echo 'export PATH="/usr/local/mysql/bin:$PATH"' >> ~/.bash_profile
  ```

* 重载配置

  ```shell
  source ~/.zshrc
  source ~/.bash_profile
  ```

* 验证环境变量是否生效

  ```shell
  echo $PATH   # 在 zsh 和 bash 下路径应包含 MySQL bin 目录
  ```

* 手动修改配置文件

  ```shell
  open ~/.zshrc
  ```

  

#### mac终端常用命令

0. 光标操作

   * Ctrl + h 删除上一个字符
   * Ctrl + w 删除上一个单词
   * Ctrl + u 删除当前行
   * Ctrl + a 回到行首
   * Ctrl + e 回到行尾
   * Ctrl + b 向前移动光标
   * Ctrl + f 向后移动光标
   
1. cd:切换目录

   ```shell
   cd /Users/      # 切换到用户主目录
   cd ~            # 切换到默认目录
   cd Desktop      # 切换到桌面
   cd ..           # 切换到上一级目录
   ```

2. ls:列出目录内容

   ```shell
   ls               # 列出当前目录内容
   ls /Users        # 列出 Users 目录内容
   ls -a            # 列出所有内容,包括隐藏文件
   ls -l            # 以列表形式显示内容详情
   ```

3. pwd:显示当前目录

4. mkdir:创建目录

   ```bash
   mkdir mydir      # 创建 mydir 目录
   mkdir -p a/b/c   # 创建多级目录
   ```

5.  rm:删除文件/目录

   ```shell
   rm file.txt      # 删除 file.txt 文件 
   rm -r mydir      # 删除 mydir 目录及其内容
   rm -f file.txt   # 强制删除文件,无需确认
   ```

6. mv:移动/重命名文件

   ```shell
   mv file.txt mydir  # 将 file.txt 移动到 mydir 目录
   mv file.txt file2.txt # 将 file.txt 重命名为 file2.txt
   ```

7. cp:复制文件/目录

   ```shell
   cp file.txt mydir        # 将 file.txt 复制到 mydir 目录
   cp -r mydir another_dir  # 递归复制 mydir 目录到 another_dir
   ```

8. cat:连接文件/查看文件内容

   ```shell
   cat file1.txt file2.txt  # 将两个文件的内容连接打印
   ```

9. echo:输出字符串

   ```shell
   echo "Hello"          # 打印 Hello
   echo $PATH            # 打印 PATH 环境变量的值 
   ```

10. open:用默认应用程序打开文件

    ```shell
    open file.txt         # 用默认编辑器打开 file.txt
    open mydir            # 打开 mydir 目录
    ```

11. man:查看命令手册

    ```shell
    man ls        # 查看 ls 命令的手册
    man man       # 查看 man 命令的手册
    ```

12. touch:创建空文件

    ```shell
    touch file.txt   # 创建空的 file.txt 文件
    ```

13. code:使用 Visual Studio Code 打开文件

    ```shell
    code file.txt      # 使用 VS Code 打开 file.txt
    code .              # 使用 VS Code 打开当前目录
    ```

14. sudo:以管理员权限运行命令

    ```shell
    sudo mkdir /usr/local/mydir    # 需要密码,创建 /usr/local/mydir 目录
    ```

15. top:查看进程信息

16. ps:查看进程快照

    ```bash
    ps aux   # 显示所有进程信息
    ```

17.  kill:终止进程

    ```shell
    kill 1234   # 终止进程 ID 为 1234 的进程
    kill -9 1234 # 强制终止进程 
    ```

18. df:查看磁盘使用情况

    ```shell
    df -h      # 以人性化格式显示磁盘使用信息
    ```

19. du:查看目录占用空间

    ```shell
    du -sh *   # 显示当前目录下各文件和目录占用空间信息
    ```

20. zip/unzip:压缩和解压文件

    ```shell
    zip -r file.zip *   # 将当前目录所有内容压缩到 file.zip
    unzip file.zip      # 解压 file.zip 文件
    ```

    

