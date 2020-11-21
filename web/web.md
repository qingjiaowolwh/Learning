
# node.js安装后才能使用npm  

npm(node package manager)node的包管理工具
## 查看版本
$ npm -v
2.3.0

## 升级 npm
cnpm install npm -g


## 升级或安装 cnpm
npm install cnpm -g

## 最新稳定版
$ cnpm install vue


## 全局安装 vue-cli
$ cnpm install --global vue-cli
## 创建一个基于 webpack 模板的新项目
$ vue init webpack my-project
## 这里需要进行一些配置，默认回车即可
This will install Vue 2.x version of the template.

For Vue 1.x use: vue init webpack#1.0 my-project

? Project name my-project  
? Project description A Vue.js project  
? Author runoob <test@runoob.com>  
? Vue build standalone  
? Use ESLint to lint your code? Yes  
? Pick an ESLint preset Standard  
? Setup unit tests with Karma + Mocha? Yes  
? Setup e2e tests with Nightwatch? Yes  

   vue-cli · Generated "my-project".

   To get started:
   
     cd my-project
     npm install
     npm run dev
   
   Documentation can be found at https://vuejs-templates.github.io/webpack


进入项目，安装并运行：   
$ cd my-project  
$ cnpm install  
$ cnpm run dev  
 DONE  Compiled successfully in 4388ms

> Listening at http://localhost:8080



