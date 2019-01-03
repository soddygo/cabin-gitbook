#基于Cabin,自己封装或开发的插件以及模板说明
基于Cabin,以及cabin支持的Vue 2.2版本基础上,进行封装使用.目的是致力于模板化,组件化,减少重复劳动,提高代码的统一维护性,方便后期统一维护.以及快速复用,减少重复劳动.

##介绍
本文档使用gitbook编写,下载此项目,项目根目录,需要用node安装gitbook命令,然后运行命令:gitbook server,然后根据提示打开浏览器查看文档.

## gitbook文档访问
互联网访问文档地址:

    https://soddy.gitbook.io/cabin-plugins/
    
## 内网参考前端使用项目

    git@gitlab.dmall.com:dmall-3pl-platform/privilege_3pl.git

## 本项目本地gitBook 使用

本文档在github上的项目地址:

    https://github.com/soddygo/cabin-gitbook

安装gitbook:

    npm install -g gitbook-cli
        
进入本项目根目录,启动服务:

    gitbook serve
    
然后会有查看文档地址的提示,具体以命令行提示的地址为准,我本地提示给我的地址是:

    http://localhost:4000/

##插件项目地址
自己开发的各个插件,最新封装项目代码,github地址:

    https://github.com/soddygo/date-pick
    

## 目录结构介绍

   ### 时间范围选择器 src/components/DatePick.vue
   打包命令:
    
    npm run build2
   
   ### 拟态框 src/components/VueModal.vue
   打包命令:
   
        npm run build3
    
   ### searchForm搜索条件表单 src/components/VueSearchForm.vue
   打包命令:
      
           npm run build5
        
   ### 树 Tree 使用的三方插件,引入重新打包过的三方的插件和样式即可
   官方github地址,里面也有使用文档:
   
            https://github.com/halower/vue-tree/blob/master/README.md
            
   fork下来的代码,也是目前使用的代码版本:
    
        https://github.com/soddygo/vue-tree