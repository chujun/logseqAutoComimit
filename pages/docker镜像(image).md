- Docker镜像类似于虚拟机镜像，--->类比可以将它理解为一个只读的模板。
  例如，一个镜像可以包含一个基本的操作系统环境，里面仅安装了Apache应用程序（或用户需要的其他软件）。可以把它称为一个Apache镜像。
  镜像是创建Docker容器的基础。
  
  Docker运行容器前需要本地存在对应的镜像，如果镜像不存在，Docker会尝试先从默认镜像仓库下载（默认使用Docker Hub公共注册服务器中的仓库），用户也可以通过配置，使用自定义的镜像仓库
- 获取镜像
  docker pull NAME[:TAG]
  对于Docker镜像来说，如果不显式指定TAG，则默认会选择latest标签，这会下载仓库中最新版本的镜像。
  一般来说，镜像的latest标签意味着该镜像的内容会跟踪最新版本的变更而变化，内容是不稳定的。因此，从稳定性上考虑，不要在生产环境中忽略镜像的标签信息或使用默认的latest标记的镜像。
  
  严格地讲，镜像的仓库名称中还应该添加仓库地址（即registry，注册服务器）作为前缀，只是默认使用的是官方Docker Hub服务，该前缀可以忽略。
  例如，docker pull ubuntu:18.04命令相当于docker pull registry.hub.docker.com/ubuntu:18.04命令，即从默认的Docker官方注册服务器Docker Hub Registry中的ubuntu仓库来下载标记为18.04的镜像。
  
  例如网易仓库服务器pull
  docker pull hub.c.163.com/public/ubuntu:18.04
  
  另外，有时需要使用镜像代理服务来加速Docker镜像获取过程，可以在Docker服务启动配置中增加--registry-mirror=proxy_URL来指定镜像代理服务地址（如https://registry.docker-cn.com）。
-