## npm 更换成淘宝镜像源以及cnpm 
### 设置镜像站点
```shell
npm config set registry https://registry.npm.taobao.org
```
检测是否成功
npm config get registry

还原npm仓库地址
```shell
npm config set registry https://registry.npmjs.org/
```
### 使用 cnpm 命令
```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
``` 
是否成功
```shell
cnpm -v
```