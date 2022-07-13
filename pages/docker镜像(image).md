- Docker镜像类似于虚拟机镜像，--->类比可以将它理解为一个只读的模板。
  例如，一个镜像可以包含一个基本的操作系统环境，里面仅安装了Apache应用程序（或用户需要的其他软件）。可以把它称为一个Apache镜像。
  镜像是创建Docker容器的基础。
  
  Docker运行容器前需要本地存在对应的镜像，如果镜像不存在，Docker会尝试先从默认镜像仓库下载（默认使用Docker Hub公共注册服务器中的仓库），用户也可以通过配置，使用自定义的镜像仓库
  
  镜像文件由多个层组成
  关于镜像大小的认识
  镜像大小信息只是表示了该镜像的逻辑体积大小，实际上由于相同的镜像层本地只会存储一份，物理上占用的存储空间会小于各镜像逻辑体积之和。
- 获取镜像命令pull
  docker pull NAME[:TAG]
  对于Docker镜像来说，如果不显式指定TAG，则默认会选择latest标签，这会下载仓库中最新版本的镜像。
  一般来说，镜像的latest标签意味着该镜像的内容会跟踪最新版本的变更而变化，内容是不稳定的。因此，从稳定性上考虑，不要在生产环境中忽略镜像的标签信息或使用默认的latest标记的镜像。
  
  严格地讲，镜像的仓库名称中还应该添加仓库地址（即registry，注册服务器）作为前缀，只是默认使用的是官方Docker Hub服务，该前缀可以忽略。
  例如，docker pull ubuntu:18.04命令相当于docker pull registry.hub.docker.com/ubuntu:18.04命令，即从默认的Docker官方注册服务器Docker Hub Registry中的ubuntu仓库来下载标记为18.04的镜像。
  
  例如网易仓库服务器pull
  docker pull hub.c.163.com/public/ubuntu:18.04
  
  另外，有时需要使用镜像代理服务来加速Docker镜像获取过程，可以在Docker服务启动配置中增加--registry-mirror=proxy_URL来指定镜像代理服务地址（如https://registry.docker-cn.com）。
- 查看镜像命令images
  docker images
  ```
  Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]
  
  List images
  
  Options:
    -a, --all             Show all images (default hides intermediate images),默认为false
        --digests         Show digests,默认为false
    -f, --filter filter   Filter output based on conditions provided
        --format string   Pretty-print images using a Go template
        --no-trunc        Don't truncate output,默认为true
    -q, --quiet           Only show image IDs,默认为false
  ```
  docker image 
  ```
   chujun@chujundeMacBook-Pro-2  ~  docker image
  
  Usage:  docker image COMMAND
  
  Manage images
  
  Commands:
    build       Build an image from a Dockerfile
    history     Show the history of an image
    import      Import the contents from a tarball to create a filesystem image
    inspect     Display detailed information on one or more images
    load        Load an image from a tar archive or STDIN
    ls          List images
    prune       Remove unused images
    pull        Pull an image or a repository from a registry
    push        Push an image or a repository to a registry
    rm          Remove one or more images
    save        Save one or more images to a tar archive (streamed to STDOUT by default)
    tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  ```
  docker images
  ```
  REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
  mysql        latest    33037edcac9b   2 hours ago    444MB
  postgres     latest    1133a9cdc367   22 hours ago   376MB
  nginx        latest    41b0e86104ba   27 hours ago   142MB
  redis        latest    3534610348b5   30 hours ago   117MB
  ```
  其中镜像的ID信息十分重要，它唯一标识了镜像。在使用镜像ID的时候，一般可以使用该ID的前若干个字符组成的可区分串来替代完整的ID。
  
  镜像大小信息只是表示了该镜像的逻辑体积大小，实际上由于相同的镜像层本地只会存储一份，物理上占用的存储空间会小于各镜像逻辑体积之和。
- 添加镜像标签命令tag
  docker tag mysql:latest mymysql:latest
  使用tag命令为本地镜像增加新标签
  ```
  chujun@chujundeMacBook-Pro-2  ~  docker tag mysql:latest mymysql:latest
   chujun@chujundeMacBook-Pro-2  ~  docker images
  REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
  mymysql      latest    33037edcac9b   2 hours ago    444MB
  mysql        latest    33037edcac9b   2 hours ago    444MB
  postgres     latest    1133a9cdc367   23 hours ago   376MB
  nginx        latest    41b0e86104ba   28 hours ago   142MB
  redis        latest    3534610348b5   30 hours ago   117MB
  ```
  多出来了一个镜像mymysql:latest,和mysql:latest都指向同一个镜像ID(33037edcac9b)
- 查看镜像详细信息命令inspect
  使用docker[image]inspect命令可以获取该镜像的详细信息，包括制作者、适应架构、各层的数字摘要等：
  ```
  chujun@chujundeMacBook-Pro-2  ~  docker inspect nginx:latest
  [
      {
          "Id": "sha256:41b0e86104ba681811bf60b4d6970ed24dd59e282b36c352b8a55823bbb5e14a",
          "RepoTags": [
              "nginx:latest"
          ],
          "RepoDigests": [
              "nginx@sha256:dbe677093f569cc0afe2a149c529645f255aac959490ef11fb19ac6418b815d3"
          ],
          "Parent": "",
          "Comment": "",
          "Created": "2022-07-12T05:00:50.081423817Z",
          "Container": "6fdddc3714ed390e2273b770258aef03b173b6f1be7221ab94f7ab673b7224c8",
          "ContainerConfig": {
              "Hostname": "6fdddc3714ed",
              "Domainname": "",
              "User": "",
              "AttachStdin": false,
              "AttachStdout": false,
              "AttachStderr": false,
              "ExposedPorts": {
                  "80/tcp": {}
              },
              "Tty": false,
              "OpenStdin": false,
              "StdinOnce": false,
              "Env": [
                  "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                  "NGINX_VERSION=1.23.0",
                  "NJS_VERSION=0.7.5",
                  "PKG_RELEASE=1~bullseye"
              ],
              "Cmd": [
                  "/bin/sh",
                  "-c",
                  "#(nop) ",
                  "CMD [\"nginx\" \"-g\" \"daemon off;\"]"
              ],
              "Image": "sha256:bcdd0667cf621a29e893be9968d0bad14ccaae14a0b3f91d9c07371bf6f64cd2",
              "Volumes": null,
              "WorkingDir": "",
              "Entrypoint": [
                  "/docker-entrypoint.sh"
              ],
              "OnBuild": null,
              "Labels": {
                  "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
              },
              "StopSignal": "SIGQUIT"
          },
          "DockerVersion": "20.10.12",
          "Author": "",
          "Config": {
              "Hostname": "",
              "Domainname": "",
              "User": "",
              "AttachStdin": false,
              "AttachStdout": false,
              "AttachStderr": false,
              "ExposedPorts": {
                  "80/tcp": {}
              },
              "Tty": false,
              "OpenStdin": false,
              "StdinOnce": false,
              "Env": [
                  "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                  "NGINX_VERSION=1.23.0",
                  "NJS_VERSION=0.7.5",
                  "PKG_RELEASE=1~bullseye"
              ],
              "Cmd": [
                  "nginx",
                  "-g",
                  "daemon off;"
              ],
              "Image": "sha256:bcdd0667cf621a29e893be9968d0bad14ccaae14a0b3f91d9c07371bf6f64cd2",
              "Volumes": null,
              "WorkingDir": "",
              "Entrypoint": [
                  "/docker-entrypoint.sh"
              ],
              "OnBuild": null,
              "Labels": {
                  "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
              },
              "StopSignal": "SIGQUIT"
          },
          "Architecture": "amd64",
          "Os": "linux",
          "Size": 141508615,
          "VirtualSize": 141508615,
          "GraphDriver": {
              "Data": {
                  "LowerDir": "/var/lib/docker/overlay2/16c2ea0bd9a83a6342328de9ef443797367c6f8029b0d2aacef0b74c48447cd9/diff:/var/lib/docker/overlay2/6eff80bd9475f7a5d43628134e328e2b0ad71595d76f5e37c26bbf5a70f4179f/diff:/var/lib/docker/overlay2/7a176df54170d72882ed54a99aa0a0233038b634c0083df526dbf6777059ea53/diff:/var/lib/docker/overlay2/d5f658c7fcf1a579bc6f2968cdbfc3556c871fc722d57795e7053892a7635068/diff:/var/lib/docker/overlay2/804c02e13a59e88871ba22856b2798a5e35623846a6854cad6a556506d33f3ed/diff",
                  "MergedDir": "/var/lib/docker/overlay2/5d9e6436273a81d5720c8088ed971546b5acbe157dbf3f7fc242459bd3d5e852/merged",
                  "UpperDir": "/var/lib/docker/overlay2/5d9e6436273a81d5720c8088ed971546b5acbe157dbf3f7fc242459bd3d5e852/diff",
                  "WorkDir": "/var/lib/docker/overlay2/5d9e6436273a81d5720c8088ed971546b5acbe157dbf3f7fc242459bd3d5e852/work"
              },
              "Name": "overlay2"
          },
          "RootFS": {
              "Type": "layers",
              "Layers": [
                  "sha256:43b3c4e3001c662d1c264ffb132f4e52950893452b15508df810214f1d3f124b",
                  "sha256:1c99a7efe9d92fa6e492787de8a3278bc7fbedf371fae96029fda8bb0910f873",
                  "sha256:305b0db3a2102757e5eddaf791cdc5e8f6d21dd9fff931230e51a3dc724bb19d",
                  "sha256:c03189a5ef7018a691b7ef5970737127f07cce7df283ca3d9b32a00838058990",
                  "sha256:1d561d93862806928bb7c99fea4035804ab7e86aa66ae8b547e5cde6856fb5db",
                  "sha256:de100bd247e00374c0614bcd52615b847ffb36eebf07d87a3158a41703385867"
              ]
          },
          "Metadata": {
              "LastTagTime": "0001-01-01T00:00:00Z"
          }
      }
  ]
  ```
  返回的是一个JSON格式的消息，如果我们只要其中一项内容时，可以使用-f来指定
  ```
  docker inspect -f {{".Architecture"}} nginx:latest
  ```
  
  其中选项中的名称是json里面的key
  ```
  docker inspect -f {{".Architecture"}} nginx:latest
  amd64
   chujun@chujundeMacBook-Pro-2  ~  docker inspect -f {{".ContainerConfig"}} nginx:latest
  {6fdddc3714ed   false false false map[80/tcp:{}] false false false [PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin NGINX_VERSION=1.23.0 NJS_VERSION=0.7.5 PKG_RELEASE=1~bullseye] [/bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon off;"]] <nil> false sha256:bcdd0667cf621a29e893be9968d0bad14ccaae14a0b3f91d9c07371bf6f64cd2 map[]  [/docker-entrypoint.sh] false  [] map[maintainer:NGINX Docker Maintainers <docker-maint@nginx.com>] SIGQUIT <nil> []}
   chujun@chujundeMacBook-Pro-2  ~  docker inspect -f {{".ContainerConfigError"}} nginx:latest
  
  Template parsing error: template: :1:2: executing "" at <.ContainerConfigError>: map has no entry for key "ContainerConfigError"
   ✘ chujun@chujundeMacBook-Pro-2  ~ 
  ```
- 查看镜像历史命令history
  既然镜像文件由多个层组成，那么怎么知道各个层的内容具体是什么呢？这时候可以使用history子命令，该命令将列出各层的创建信息。
  docker history nginx:latest
  ```
  IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
  41b0e86104ba   28 hours ago   /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B
  <missing>      28 hours ago   /bin/sh -c #(nop)  STOPSIGNAL SIGQUIT           0B
  <missing>      28 hours ago   /bin/sh -c #(nop)  EXPOSE 80                    0B
  <missing>      28 hours ago   /bin/sh -c #(nop)  ENTRYPOINT ["/docker-entr…   0B
  <missing>      28 hours ago   /bin/sh -c #(nop) COPY file:09a214a3e07c919a…   4.61kB
  <missing>      28 hours ago   /bin/sh -c #(nop) COPY file:0fd5fca330dcd6a7…   1.04kB
  <missing>      28 hours ago   /bin/sh -c #(nop) COPY file:0b866ff3fc1ef5b0…   1.96kB
  <missing>      28 hours ago   /bin/sh -c #(nop) COPY file:65504f71f5855ca0…   1.2kB
  <missing>      28 hours ago   /bin/sh -c set -x     && addgroup --system -…   61.1MB
  <missing>      28 hours ago   /bin/sh -c #(nop)  ENV PKG_RELEASE=1~bullseye   0B
  <missing>      28 hours ago   /bin/sh -c #(nop)  ENV NJS_VERSION=0.7.5        0B
  <missing>      28 hours ago   /bin/sh -c #(nop)  ENV NGINX_VERSION=1.23.0     0B
  <missing>      28 hours ago   /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B
  <missing>      32 hours ago   /bin/sh -c #(nop)  CMD ["bash"]                 0B
  <missing>      32 hours ago   /bin/sh -c #(nop) ADD file:d978f6d3025a06f51…   80.4MB
  ```
  注意，过长的命令被自动截断了，可以使用--no-trunc选项来输出完整命令。
- 搜索镜像命令search
  ```
  docker search --help
  Usage:  docker search [OPTIONS] TERM
  
  Search the Docker Hub for images
  
  Options:
    -f, --filter filter   Filter output based on conditions provided
        --format string   Pretty-print search using a Go template
        --limit int       Max number of search results (default 25)
        --no-trunc        Don't truncate output
  ```
  举例1
   docker search nginx
  ```
   docker search nginx
  NAME                                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
  nginx                                             Official build of Nginx.                        17087     [OK]
  linuxserver/nginx                                 An Nginx container, brought to you by LinuxS…   169
  bitnami/nginx                                     Bitnami nginx Docker Image                      136                  [OK]
  ubuntu/nginx                                      Nginx, a high-performance reverse proxy & we…   52
  bitnami/nginx-ingress-controller                  Bitnami Docker Image for NGINX Ingress Contr…   19                   [OK]
  rancher/nginx-ingress-controller                                                                  10
  clearlinux/nginx                                  Nginx reverse proxy server with the benefits…   4
  ibmcom/nginx-ingress-controller                   Docker Image for IBM Cloud Private-CE (Commu…   4
  bitnami/nginx-ldap-auth-daemon                                                                    3
  bitnami/nginx-exporter                                                                            2
  rapidfort/nginx                                   RapidFort optimized, hardened image for NGINX   2
  rancher/nginx                                                                                     2
  rancher/nginx-ingress-controller-defaultbackend                                                   2
  vmware/nginx                                                                                      2
  circleci/nginx                                    This image is for internal use                  2
  vmware/nginx-photon                                                                               1
  wallarm/nginx-ingress-controller                  Kubernetes Ingress Controller with Wallarm e…   1
  bitnami/nginx-intel                                                                               1
  kasmweb/nginx                                     An Nginx image based off nginx:alpine and in…   1
  rancher/nginx-conf                                                                                0
  ibmcom/nginx-ingress-controller-ppc64le           Docker Image for IBM Cloud Private-CE (Commu…   0
  continuumio/nginx-ingress-ws                                                                      0
  rancher/nginx-ingress-controller-amd64                                                            0
  ibmcom/nginx-ppc64le                              Docker image for nginx-ppc64le                  0
  rancher/nginx-ssl                                                                                 0
  ```
  例子2基于官网镜像的过滤
  docker search -f=is-official=true nginx
  ```
  docker search -f=is-official=true nginx
  AME      DESCRIPTION                STARS     OFFICIAL   AUTOMATED
  nginx     Official build of Nginx.   17087     [OK]
  ```
  例子3star大于4的过滤
  docker search -f=stars=4 nginx
  ```
   ✘ chujun@chujundeMacBook-Pro-2  ~  docker search -f=stars=4 nginx
  NAME                               DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
  nginx                              Official build of Nginx.                        17087     [OK]
  linuxserver/nginx                  An Nginx container, brought to you by LinuxS…   169
  bitnami/nginx                      Bitnami nginx Docker Image                      136                  [OK]
  ubuntu/nginx                       Nginx, a high-performance reverse proxy & we…   52
  bitnami/nginx-ingress-controller   Bitnami Docker Image for NGINX Ingress Contr…   19                   [OK]
  rancher/nginx-ingress-controller                                                   10
  ibmcom/nginx-ingress-controller    Docker Image for IBM Cloud Private-CE (Commu…   4
  clearlinux/nginx                   Nginx reverse proxy server with the benefits…   4
  ```
-