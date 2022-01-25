---
layout: post
title:  "Synology(群晖)的Git安装和配置"
categories: Git 
---

### 安装
- **安装Git Server**
	
  进入 `套件中心` 点击安装 `Git Server`
	
- **启动SSH**
	
  进入 `控制面板` -> `终端机和SNMP` -> `终端机` 中，`启动SSH功能`
	  
- **创建Git仓库**
	
  进入 `控制面板` -> `共享文件夹`，新建一个共享文件夹 `git_repos` 作为 Git 的仓库目录
	  
- **创建Git账号及授权**
	
  进入 `控制面板` -> `用户账号`，新建一个 Git 用户（可选，仅对新加的用户）
	  
- **赋予用户在** `git_repos` **目录的读写权限**
	
  打开 `Git` 设置界面，对用户勾选，设置为允许访问
	  
### 创建Git版本库
- **服务器建立版本库**
	请通过 SSH 服务（Putty或者ssh客户端）以 `admin` 身份登录 Synology 服务器。
	运行以下命令
	```bash
	#进入 Git 版本库目录
	cd /volume2/git_repos
		  
	mkdir myproj.git
	cd myproj.git 
	# 建立 Git 项目仓库
	git --bare init
	git update-server-info
	```
	这样服务器端 Git 项目已经建立好

- **客户端获取 Git 项目**
	```bash
	git clone ssh://[Git User]@[Syno_Host]/myproj.git
	```

- **客户端 Git 项目相关操作**
	```bash
	cd myproj
	touch readme.txt
	# git
	git init
	git add .
	git commit . -m "myproj..."
	git remote add origin ssh://[GitUser]@[Syno_Host]/volume2/git_repos/myproj.git
	git push origin master
	```

### 参考文档
- [Synology(群晖)的Git安装和配置](https://www.asnidea.com/2017/01/19/git-on-nas-synology/)
- [GIT SERVER ON SYNOLOGY NAS: INSTALLATION AND CONFIGURATIONS](https://blog.netgloo.com/2015/04/20/git-server-on-synology-ds115j-installation-and-configurations/)