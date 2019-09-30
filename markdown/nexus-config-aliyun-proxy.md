
## 说明

在前些章节中，我们提到要使用nexus作为公司的主要服务器，所有开发人员都要用这个库中获取依赖关系。但是在我们实际的生产活动中，我们发现，nexus需要连接国外服务器来更新index。所以需要对nexus配置国内的index源。所以就有了这篇文章。

## 配置阿里云的代理源

1. 登陆nexus私服（默认账号密码：admin/admin123）
2. 点击右侧Repositories
3. 点击add，选择Proxy Repository
4. 配置阿里云地址 http://maven.aliyun.com/nexus/content/groups/public/ , id,name随意填
5. 将阿里云仓库加入仓库组中，并将其排在Central仓库之前

## 参考

https://blog.csdn.net/qq_30633989/article/details/80399596
