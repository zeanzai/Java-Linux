1. 创建文件夹，用来存放图片地址
2. 配置nginx

3. 测试地址

4. 开放端口
```
firewall-cmd --zone=public --permanent --add-port=8103/tcp
firewall-cmd --reload
```
