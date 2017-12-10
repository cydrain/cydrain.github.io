---
title: github.io 更新与备份
date: 2017-12-09 14:06:13
tags:
categories: Github
---

# 完整操作如下：（摘自知乎回答）

1. 创建仓库 cydrain.github.io;
2. 创建两个分支：master 与 hexo;
   git branch hexo
3. 设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）;
4. 使用 git clone git@github.com:cydrain/cydrain.github.io.git 拷贝仓库;
5. 在本地 cydrain.github.io 文件夹下通过Git bash依次执行
   npm install hexo
   hexo init
   npm install
   npm install hexo-deployer-git（此时当前分支应显示为hexo）;
6. 修改 _config.yml 中的 deploy 参数，分支应为master；
7. 依次执行
   git add .
   git commit -m “…”
   git push origin hexo 提交网站相关的文件；
8. 执行 hexo generate -d 生成网站并部署到 GitHub 上。



# 本地资料丢失

当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：
使用 git clone git@github.com:cydrain/cydrain.github.io.git 拷贝仓库（默认分支为hexo）;
在本地新拷贝的 cydrain.github.io 文件夹下通过 Git bash 依次执行下列指令：
   npm install hexo
   npm install
   npm install hexo-deployer-git（记得，不需要hexo init这条指令）。
