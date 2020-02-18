---
title: hexo搭建个人博客
date: 2020-02-17 18:14:50
tags:
---

## 一、环境搭建

- 安装node

- 安装git

- 通过npm安装hexo-cli

  ```
  npm install -g hexo-cli
  
  ```
  
- 通过npm安装hexo-de

  ```
  npm install hexo-deployer-git --save
  
  ```

## 二、建站

- 创建hexo项目blog

  ```
  hexo init blog
  
  ```

  blog项目的目录结构如下

  ```
  ├── _config.yml 	// 网站配置文件
  ├── package.json 	// 项目配置文件
  ├── scaffolds		// 模板文件夹
  ├── source  		// 资源文件
  |   ├── _drafts  	// 草稿文章
  |   └── _posts 		// 文章
  └── themes 			// 主题
  
  ```

  

- 新建一个文章 hello wolrd，标题中有空格需用引号，否则可以省略引号

  ```
  hexo new "hello world" // 可以简写成 hexo n "hello world"
  
  ```

- _posts文件夹下会生成一个markdown文件

- ```
  hexo generate 或者 hexo g // 文章编辑完成，用此命令将markdown文件生成相对于的html静态文件和其他配置
  
  ```

- ```
  hexo publish // 将文章保存到草稿
  
  ```

-  启动服务器

  ```
  hexo server 或者 hexo s // 默认访问网址http://localhost:4000/
  
  ```

- 部署到GitHub

  - 在GitHub中创建仓库`xxx.github.io`，xxx必须为GitHub账号名称

  - 在_config.yml中最后配置deploy信息

    ```
    deploy:
      type: git
      repo: https://github.com/<username>/<project>
      # example, https://github.com/hexojs/hexojs.github.io
      branch: master
      
    ```

  - `hexo clean && hexo deploy`清楚缓存并按配置部署