- 上传至/opt/packages
- 加压
	tar zxf go*
- 移动解压后的文件到/usr/setup/
	mv -f go* /usr/setup
- 创建编译目录
	mkdir /usr/setup/go/gopath
	mkdir /usr/setup/go/gopath/bin
	mkdir /usr/setup/go/gopath/src
	mkdir /usr/setup/go/gopath/pkg
- 修改环境变量
	vi /etc/profile

	export GOROOT=/usr/setup/go
	export GOPATH=/usr/setup/go/gopath
	export PATH=$PATH:$JAVA_HOME/bin:$GIT_HOME/bin:$GOROOT/bin:$GOPATH/bin
- 使配置生效
	source /etc/profile
- 测试
	go version
