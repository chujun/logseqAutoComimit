- 环境变量
  按照作用域来分，环境变量可以简单的分成:
  用户级别环境变量 : `~/.bashrc` 、 `~/.bash_profile` 。
  系统级别环境变量 : `/etc/bashrc` 、 `/etc/environment` 、 `/etc/profile` 、 `/etc/profile.d` 。
  
  上述配置文件执行先后顺序为： `/etc/enviroment` –> `/etc/profile` –> `/etc/profile.d` –> `~/.bash_profile` –> `/etc/bashrc` –> `~/.bashrc` 
  
  如果要修改系统级别环境变量文件，需要管理员具备对该文件的写入权限。
  
  建议用户级别环境变量在 `~/.bash_profile` 中配置，系统级别环境变量在 `/etc/profile.d` 中配置。
  
  按照生命周期来分，环境变量可以简单的分成:
  永久的：需要用户修改相关的配置文件，变量永久生效。
  临时的：用户利用 `export` 命令，在当前终端下声明环境变量，关闭 shell 终端失效。
	- 读取环境变量
	  通过 `export` 命令可以输出当前系统定义的所有环境变量。
	  ```
	  # 列出当前的环境变量值
	  export -p
	  ```
	  除了 `export` 命令之外， `env` 命令也可以列出所有环境变量。
	  `echo` 命令可以输出指定环境变量的值。
	  ```
	  # 输出当前的PATH环境变量的值
	  echo $PATH
	  # 输出当前的HOME环境变量的值
	  echo $HOME
	  ```
	- 环境变量修改
	  通过 `export` 命令可以修改指定的环境变量。不过，这种方式修改环境变量仅仅对当前 shell 终端生效，关闭 shell 终端就会失效。修改完成之后，立即生效。
	  ```
	  export CLASSPATH=./JAVA_HOME/lib;$JAVA_HOME/jre/lib
	  ```
	  通过 `vim` 命令修改环境变量配置文件。这种方式修改环境变量永久有效。
	  ```
	  vim ~/.bash_profile
	  ```
	  如果修改的是系统级别环境变量则对所有用户生效，如果修改的是用户级别环境变量则仅对当前用户生效。
	  修改完成之后，需要 `source` 命令让其生效或者关闭 shell 终端重新登录。
	  ```
	  source /etc/profile
	  ```
-