# 一、sourceTree免注册安装

前提：安装Git

1.  下载SourceTree安装文件
2.  双击安装，会出现如下界面：![image](https://upload-images.jianshu.io/upload_images/18030682-1dcac8423631a4de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.  关闭这个界面

4.  C:\Users\当前用户文件夹\AppData\Local\Atlassian\SourceTree添加accounts.json文件，内容如下：

```
[
  {
    "$id": "1",
    "$type": "SourceTree.Api.Host.Identity.Model.IdentityAccount, SourceTree.Api.Host.Identity",
    "Authenticate": true,
    "HostInstance": {
      "$id": "2",
      "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountInstance, SourceTree.Host.AtlassianAccount",
      "Host": {
        "$id": "3",
        "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountHost, SourceTree.Host.AtlassianAccount",
        "Id": "atlassian account"
      },
      "BaseUrl": "https://id.atlassian.com/"
    },
    "Credentials": {
      "$id": "4",
      "$type": "SourceTree.Model.BasicAuthCredentials, SourceTree.Api.Account",
      "Username": "",
      "Email": null
    },
    "IsDefault": false
  }
]
```

5.  编辑文件C:\Users\当前用户文件夹\AppData\Local\Atlassian\SourceTree.exe_Url_2pp5ph5o52ezn1jhauso0c3bpdeplqgk\3.1.3.3158\user.config，configuration userSettings SourceTree.Properties.Settings 之下添加以下 6 行内容：

```
 <setting name="AgreedToEULA" serializeAs="String">
        <value>True</value>
</setting>
<setting name="AgreedToEULAVersion" serializeAs="String">
        <value>20200602</value>
</setting>
```

6.  重新运行sourceTree，选择最后一项：我不需要mercurial

# 二、SourceTree的使用

配置本地文件名和邮箱
```
git config --global user.name "Your name"
git config --global user.email your_email@gmail.com
```
#常用命令
```
git bash here       git clone url //克隆项目到本地
git status //查看修改的文件
git add . //保存所有修改过的文件
git commit -m "此次提交描述" //保存到本地git
git push origin master  //上传 (要推到的远程分支名称  例如分支名称是aaa  git push origin aaa)
```

## 1.提交推送

git　add  file_1 file_2  //添加暂存文件

git commit -m 'msg1' //添加文件增加备注

git pull //拉取

git push //推送至远程

正确提交流程：暂存-->提交-->获取-->拉取（解决冲突-->提交）-->推送

## 2.贮藏工作区

git stash    //存储工作区

git stash save 'msg1'   //存储工作区并备注

git stash list  //展示存储区列表

git stash apply  //应用存储区第一个 （不删除）

git stash pop    //应用存储区第一个 （并删除）

git stash drop //删除存储区最新的内容

git stash@{0}   *apply / pop / drop  第1个  ，git stash list获取下标

## 3.回撤版本

git reset --soft 34524234  // 将版本重置到34524234 版本，

git reset --soft HEAD^ 将版本重置到上一个版本

git reset --hard  34524234

简单总结一下，其实就是--soft 、--mixed以及--hard是三个恢复等级。使用--soft就仅仅将头指针恢复，已经add的缓存以及工作空间的所有东西都不变。如果使用--mixed，就将头恢复掉，已经add的缓存也会丢失掉，工作空间的代码什么的是不变的。如果使用--hard，那么一切就全都恢复了，头变，aad的缓存消失，代码什么的也恢复到以前状态。

## 4.提交回滚

git revert HEAD -n //回滚到上一个版本

git commit -m '回滚到该次提交' 

git  push

## 5.版本标签记录 tag

git tag  //查看已有的标签

git tag v1.0,0 //标记最新的提交记录标签v1.0.0

git tag v1,0.0 1234234 //标记指定的提交

git tag -d  v1.0.0 //删除名为v1.0.0的标签

git push origin v1.0.0 //推送远程

git push origin :refs/tags/v1.0.0 //删除远程仓库中的标签

## 6.创建分支

git branch //查看分支 -a查看所有分支

git branch dev //在当前分支基础上，以最新版本创建名为dev的分支

git branch dev d12312312 //以d12312312为版本记录创建dev分之

git checkout dev //切换分之到dev

git  checkout -b dev //创建并切换分之

git branch -d dev //删除本地dev分之

git push origin dev:dev // 推送到远程分之

git push --delete origin dev //删除远程dev分支

## 7.合并分支

git checkout master 切换分之

git merge de //合并de分支到master

## 8.拉取分支

git fetch  //获取远程更新

git push //将本地分支推送到远程

## 9.操作远程分支

git branch -r   查看所有远程分支 -a所有分支

git checkout -b dev --trach origin/dev //创建dev通过远程分支dev 并切换 --trach可追踪

git branch -r -d origin/dev //删除远程dev分支

git push origin :dev 

## 10.查看修改记录

git log index.html //查看目标文件提交信息记录

git log -p index.html //查看目标文件所有提交修改内容

## 11.挑选版本合并 cherry-pick

git  checkout   dev //要合并到dev必须先切换到dev

git  cherry-pick   d123123 // 将制定版本合并到当前分支，并且保留当次提交信息


**总结：**
source tree是非常好用的git gui 工具，它的每个操作和git命令是有对应关系的：

抓取就相当于git fetch,
拉取就相当于git pull,
推送就相当于git push,
暂存（储藏）就相当于git stash,
遴选就相当于git cherry-pick,
右键审查选定的修改日志就相当于git log
右键审查选定项目相当于git blame
右键提交回滚就相当于git revertreact