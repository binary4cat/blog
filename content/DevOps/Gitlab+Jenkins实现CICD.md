---
title: "Gitlab+Jenkins实现CI/CD"
date: 2019-02-10T14:00:48+08:00
tags: ["CI/CD","Jenkins"]
categories: ["DevOps"]
description : "Gitlab+Jenkins实现CICD"
draft: false
---



# 概述

在这篇文章中，演示一个简单的持续集成的案例，使用Windows的Linux子系统部署Gitlab和Jenkins，实现一套自动测试、自动部署的系统，本次的内容没有涉及到Docker部署，下一篇文章将介绍全部使用Docker部署的持续集成。

## 系统

安装部署使用的系统为Windows下的Linux子系统，具体的系统信息：
![Ubuntu18.04](/image/Snipaste_2019-02-10_14-06-04.png)

# 安装Jenkins

## 安装到Ubuntu系统

安装`repository key`到操作系统：

```shell
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
```

添加包源到`sources.list`中：

```shell
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
```

更新`apt`新仓库

```shell
sudo apt update
```

最后安装Jenkins

```shell
sudo apt install jenkins
```

启动jenkins

```shell
sudo service jenkins start
```

如果显示`[OK]`那么说明Jenkins启动成功了，Jenkins不需要数据库，所以现在可以直接在浏览器中浏览了，在浏览器中打开地址`http://your-domian:8080`。

## 配置Jenkins

第一步会提示输入一段密钥：
![密钥](/image/Image.png)
直接按照提示打开该文件即可：

```shell
sudo vi /var/lib/jenkins/secrets/initialAdminPassword
```

如果提示没有权限，则需要将该文件的权限赋予当前的用户：

```shell
sudo chmod 777  /var/lib/jenkins/secrets/initialAdminPassword
```

接下来会进入安装界面，选择默认的安装即可，等待安装完毕，Jenkins就准备好了。

# 安装Gitlab

## 安装到Ubuntu

首先获取官方的安装脚本到临时文件夹`/tmp`

```shell
cd /tmp
curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh
```

执行该脚本：

```shell
sudo bash /tmp/script.deb.sh
```

从apt安装gitlab

```shell
sudo apt install gitlab-ce
```

由于国内网络的原因，安装过程可能十分缓慢。

![gitlab](/image/Snipaste_2019-02-10_16-45-28.png)

## 配置防火墙

如果您使用的不是WSL的Ubuntu系统，可能需要配置一下防火墙

```shell
sudo ufw allow http
sudo ufw allow https
sudo ufw allow OpenSSH
```

## 配置Gitlab

修改gitlab的域名(地址)，打开`/etc/gitlab/gitlab.rb`文件修改`external_url`的值：
![external_url](/image/Snipaste_2019-02-10_16-54-46.png)

然后重新加载配置文件：

```shell
sudo gitlab-ctl reconfigure
```

# 配置自动构建任务

## 创建任务

选择创建一个流水线任务：
![流水线](/image/Snipaste_2019-02-10_18-27-06.png)

配置一个流水线构建触发的token，用来定位构建器：
![Snipaste_2019-02-10_18-29-59.png](/image/Snipaste_2019-02-10_18-29-59.png)

将Jenkins的构建器地址设置到GitLab中，我们进入GitLab新建一个项目，然后选择菜单的`Integrations`：
![Snipaste_2019-02-10_18-33-17.png](/image/Snipaste_2019-02-10_18-33-17.png)

将刚才在Jenkins中设置的触发器连接填写进去，然后点击“Add webhook”：
![Snipaste_2019-02-10_18-34-58.png](/image/Snipaste_2019-02-10_18-37-00.png)

## 配置Jenkins

去掉Jenkins的安全跨站点访问，否则GitLab的请求就会被限制：
![Snipaste_2019-02-10_18-49-44.png](/image/Snipaste_2019-02-10_18-49-44.png)
![Snipaste_2019-02-10_18-53-33.png](/image/Snipaste_2019-02-10_18-53-33.png)

## 编写Pipeline script

```groovy
pipeline {
    anent any

    stages {
        stage('获取代码') {
            echo "git clone code form gitlab"
            deleteDir()
            git "http://git@192.168.1.12:8088/haijd/NetCore.git"
        }
        stage('运行测试') {
            sh(script: "cd NetCore.Tests && dotnet test", returnStdout: true)
            sh(script: "cd ../NetCore.Web/", returnStdout: true)
        }
        stage('编译') {
            sh(script: "dotnet publish NetCore.sln -c Release ", returnStdout: true)
        }
    }
}
```

至此自动测试编译的脚本就写好了，在代码push到GitLab的时候会触发构建，然后执行上面的脚本，重新获取代码，测试、编译。

# 参考资料
[https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-gitlab-on-ubuntu-18-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-gitlab-on-ubuntu-18-04)
[https://cloudblogs.microsoft.com/opensource/2018/09/21/configure-jenkins-cicd-pipeline-deploy-asp-net-core-application/](https://cloudblogs.microsoft.com/opensource/2018/09/21/configure-jenkins-cicd-pipeline-deploy-asp-net-core-application/)
[https://about.gitlab.com/install/](https://about.gitlab.com/install/)
[https://www.enesdalga.com/en/asp-net-core-en/asp-net-core-auto-deploy-en/](https://www.enesdalga.com/en/asp-net-core-en/asp-net-core-auto-deploy-en/)