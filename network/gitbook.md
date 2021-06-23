# gitbook
### 安装node
### 安装gitbook
```
npm install gitbook -g
npm install gitbook-cli -g

```
gitbook -v 报错 command not found
```
// 查看全局安装路径
npm root -g
//进入目录
/node-v10.22.0-linux-arm64/lib/node_modules/gitbook-cli/bin/
./gitbook.js -V

 # 增加一个软连接
 ln -s /runtimes/node-v10.22.0-linux-arm64/lib/node_modules/gitbook-cli/bin/gitbook.js /usr/local/bin/gitbook
 gitbook -V
 收工
```

 # 初始化gitbook 仓库
 gitbook init 
  # 部署服务
 gitbook serve
 ​
 # 修改后重新构建服务
 gitbook build
 ​
 # 重新部署服务
 gitbook serve 