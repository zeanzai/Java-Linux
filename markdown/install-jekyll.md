- 检查ruby是否安装
	ruby -v
- 更新安装源
	yum -y update
- 安装RVM
	gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
	curl -sSL https://get.rvm.io | bash -s stable // 此步骤时间有点长
	source /etc/profile.d/rvm.sh
	rvm -v
		输出：rvm 1.29.4 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]
- 安装最新的稳定版本
	rvm install ruby // 此步骤时间有点长
- 测试是否安装
	ruby -v
		输出：ruby 2.5.1p57 (2018-03-29 revision 63029) [x86_64-linux]
	gem -v
		输出：2.7.7

- 安装jekyll和bundle
	gem install jekyll bundle jekyll-paginate
- 测试jekyll是否安装
	bundle -v
		输出： Bundler version 1.16.2
	jekyll -v
		输出： jekyll 3.8.3
