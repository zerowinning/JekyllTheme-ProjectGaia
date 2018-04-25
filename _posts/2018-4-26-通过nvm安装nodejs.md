

## 一、安装Nvm 
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.10/install.sh | bash
```
## 二、使用淘宝nvm源安装nodejs
```
NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/dist
nvm install 8.11.1 
```
## 三、安装使用CNpm 
```
npm install -g cnpm –registry=https://registry.npm.taobao.org 
```

## 四：查看版本信息 

nvm -v 

node -v 

npm -v