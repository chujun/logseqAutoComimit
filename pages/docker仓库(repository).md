- Docker仓库类似于代码仓库，是Docker集中存放镜像文件的场所。
  根据所存储的镜像公开分享与否，Docker仓库可以分为公开仓库（Public）和私有仓库（Private）两种形式。
  目前，最大的公开仓库是官方提供的Docker Hub，其中存放着数量庞大的镜像供用户下载。国内不少云服务提供商（如腾讯云、阿里云等）也提供了仓库的本地源，可以提供稳定的国内访问。
  当然，用户如果不希望公开分享自己的镜像文件，Docker也支持用户在本地网络内创建一个只能自己访问的私有仓库。
  
  >可以看出，Docker利用仓库管理镜像的设计理念与Git代码仓库的概念非常相似，实际上Docker设计上借鉴了Git的很多优秀思想。
-