- 打包和解压文件命令tar
  **1）打包并压缩文件：**
  Linux 中的打包文件一般是以.tar 结尾的，压缩的命令一般是以.gz 结尾的。而一般情况下打包和压缩是一起进行的，打包并压缩后的文件的后缀名一般.tar.gz。 命令： `tar -zcvf 打包压缩后的文件名 要打包压缩的文件` ，其中：
  z：调用 gzip 压缩命令进行压缩
  c：打包文件
  v：显示运行过程
  f：指定文件名
  比如：假如 test 目录下有三个文件分别是：aaa.txt bbb.txt ccc.txt，如果我们要打包 test 目录并指定压缩后的压缩包名称为 test.tar.gz 可以使用命令：** `tar -zcvf test.tar.gz aaa.txt bbb.txt ccc.txt` 或 `tar -zcvf test.tar.gz /test/` **
  
  **2）解压压缩包：**
  命令： `tar [-xvf] 压缩文件`
  其中：x：代表解压
  示例：
  a。将 /test 下的 test.tar.gz 解压到当前目录下可以使用命令：** `tar -xvf test.tar.gz` **
  b。将 /test 下的 test.tar.gz 解压到根目录/usr 下:** `tar -xvf test.tar.gz -C /usr` **（- C 代表指定解压的位置）
- 权限命令
  操作系统中每个文件都拥有特定的权限、所属用户和所属组。权限是操作系统用来限制资源访问的机制，在 Linux 中权限一般分为读(readable)、写(writable)和执行(excutable)，分为三组。分别对应文件的属主(owner)，属组(group)和其他用户(other)，通过这样的机制来限制哪些用户、哪些组可以对特定的文件进行什么样的操作。
  
  通过** `ls -l` **命令我们可以 查看某个目录下的文件或目录的权限
  
  ![image.png](../assets/image_1656750273440_0.png)
  第一列的内容的信息解释如下：
  ![image.png](../assets/image_1656750325968_0.png)