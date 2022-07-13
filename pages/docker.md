- 虚拟化--->docker的容器化(轻量级容器化)
  
  >IBM DeveloperWorks网站关于容器技术的描述十分准确：“容器有效地将由单个操作系统管理的资源划分到孤立的组中，以更好地在孤立的组之间平衡有冲突的资源使用需求。与虚拟化相比，这样既不需要指令级模拟，也不需要即时编译。容器可以在核心CPU本地运行指令，而不需要任何专门的解释机制。此外，也避免了准虚拟化（para-virtualization）和系统调用替换中的复杂性。”
- 什么是docker
  基于go语法开发的开源容器项目。
  
  Docker的构想是要实现“Build, Ship and Run Any App, Anywhere”，即通过对应用的封装（Packaging）、分发（Distribution）、部署（Deployment）、运行（Runtime）生命周期进行管理，达到应用组件级别的“一次封装，到处运行”。
  这里的应用组件，既可以是一个Web应用、一个编译环境，也可以是一套数据库平台服务，甚至是一个操作系统或集群。
  
  Linux容器技术——巨人的肩膀
  最重要的就是Linux容器（Linux Containers, LXC）技术。
  
  从Linux容器到Docker
- 资料
  [docker官网](https://www.docker.com/)
  [github源码地址](https://github.com/docker)