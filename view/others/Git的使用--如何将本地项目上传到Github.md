 Git的安装就不说了。接下来一步一步的教大家怎么做，简要步骤可以直接看最后的总结。

### 1.我们需要先创建一个本地的版本库（其实也就是一个文件夹）

  你可以直接右击新建文件夹，也可以右击打开Git bash命令行窗口通过命令来创建。
  现在我通过后者来实现，在创建好的文件夹上右键Git bash here打开命令窗口。

### 2.通过命令`git init`把这个文件夹变成Git可管理的仓库

![image.png](https://upload-images.jianshu.io/upload_images/18030682-2dff2fcec53b6b3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 这时你会发现TEST里面多了个.git文件夹，它是Git用来跟踪和管理版本库的。如果你看不到，是因为它默认是隐藏文件，那你就需要设置一下让隐藏文件可见。

### 3.把你的项目粘贴到这个本地Git仓库
这时候你就可以把你的项目粘贴到这个本地Git仓库里面（粘贴后你可以通过`git status`来查看你当前的状态），然后通过`git add`把项目添加到仓库（或`git add .`把该目录下的所有文件添加到仓库，注意点是用空格隔开的）。在这个过程中你其实可以一直使用git status来查看你当前的状态。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-00d0c28b6962a9d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 这里提示你虽然把项目粘贴过来了，但还没有add到Git仓库上，然后我们通过`git add .`把刚才复制过来的项目全部添加到仓库上。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-891c607004ff9207.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 4.用`git commit`把项目提交到仓库
![image.png](https://upload-images.jianshu.io/upload_images/18030682-098ae36394b2b5e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 -m后面引号里面是本次提交的注释内容，这个可以不写，但最好写上，不然会报错，详情自行Google。 好了，我们本地Git仓库这边的工作做完了，下面就到了连接远程仓库（也就是连接Github）
由于本地Git仓库和Github仓库之间的传输是通过SSH加密的，所以连接时需要设置一下.

### 5.创建SSH KEY
先看一下你C盘用户目录下有没有.ssh目录，有的话看下里面有没有id_rsa和id_rsa.pub这两个文件，有就跳到下一步，没有就通过下面命令创建```$ ssh-keygen -t rsa -C "youremail@example.com"```
然后一路回车。这时你就会在用户下的.ssh目录里找到id_rsa和id_rsa.pub这两个文件.   

### 6.Github上创建新项目文件
登录Github,找到右上角的图标，打开点进里面的Settings，再选中里面的SSH and GPG KEYS，点击右上角的New SSH key，然后Title里面随便填，再把刚才id_rsa.pub里面的内容复制到Title下面的Key内容框里面，最后点击Add SSH key，这样就完成了SSH Key的加密。具体步骤也可看下面：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-269077138143befe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 ![image.png](https://upload-images.jianshu.io/upload_images/18030682-6552b5bb9d69bd3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/18030682-b0c6b22f2081a0a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/18030682-b01a91a5cb63cc01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 7.在Github上创建一个Git仓库
​     你可以直接点New repository来创建，比如我创建了一个TEST2的仓库（因为我里面已经有了一个test的仓库，所以不能再创建TEST仓库）。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-067d2ab9d220e424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/18030682-8b81d1a3263b7b00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
创建好之后，复制下面的地址，后边会用到
![image.png](https://upload-images.jianshu.io/upload_images/18030682-cbb9945643db20cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 8.Github仓库和本地仓库关联
在Github上创建好Git仓库之后我们就可以和本地仓库进行关联了，根据创建好的Git仓库页面的提示，可以在本地TEST仓库的命令行输入：

```
$ git remote add origin *粘贴刚才复制的地址*
```
注意origin后面加的是你Github上创建好的仓库的地址。

​        

### 9.关联好之后我们就可以把本地库的所有内容推送到远程仓库（也就是Github）上了
```$ git push -u origin master```
由于新建的远程仓库是空的，所以要加上-u这个参数，等远程仓库里面有了内容之后，下次再从本地库上传内容的时候只需下面这样就可以了：
```$ git push origin master```
上传项目的过程可能需要等一段时间，完成之后是这样的：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-139fa79c59f9a2bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 这时候你再重新刷新你的Github页面进入刚才新建的那个仓库里面就会发现项目已经成功上传了：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-3a440e9bf1df1cf4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


至此就完成了将本地项目上传到Github的整个过程。

另外，这里有个坑需要注意一下，就是在上面第七步创建远程仓库的时候，如果你勾选了Initialize this repository with a README（就是创建仓库的时候自动给你创建一个README文件），那么到了第九步你将本地仓库内容推送到远程仓库的时候就会报一个failed to push some refs to  https://github.com/guyibang/TEST2.git的错。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-5831fa7eaf7f9d42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 这是由于你新创建的那个仓库里面的README文件不在本地仓库目录中，这时我们可以通过以下命令先将内容合并以下：
```$ git pull --rebase origin master```
这时你再push就能成功了。

还有可能创建好之后，项目上会出现这样的提示：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-42e0ece923698699.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
解决方案：
* 首先删除package-lock.json 并同步到git，保证git上面没有lock这个文件，不会出警报；

* 然后在本地文件夹把package-lock.json 还原回来

* 找到一个叫做 .gitignore，把package-lock.json贴在这个文件里。（这个文件是专门填写 需要git忽略掉的文件名字的）

* 再次同步到git，这个时候就会发现，package-lock.json还是在本地文件夹里，但是不在git上，所以也就不会报错了。

  

### 总结

其实只需要进行下面几步就能把本地项目上传到Github
1. 创建本地文件夹，右键文件夹，选择`Git bash here`打开命令窗口。
2. 输入`git init`把这个文件夹变成Git可管理的仓库。
3. 把需要上传的项目粘贴到该文件夹里，然后命令窗口输入`git add .`，把刚才复制过来的项目全部添加到仓库上。
4. 输入`git commit -m '描述'`把项目提交到仓库。
5. 输入`ssh-keygen -t rsa -C "youremail@example.com"`创建SSH KEY(已创建的可忽略这一步)。
6. 把第5步创建好的SSH KEY添加到Github(已添加过的可忽略这一步)。
7. 在Github上创建一个Git仓库，创建好之后复制项目地址。
8. Github仓库和本地仓库关联，命令行输入`git remote add origin *第7步复制的地址*`。
9. `git push -u origin master`把本地库的所有内容推送到远程仓库。



### 设置忽略文件
如果想忽略掉某个文件，不让这个文件提交到版本库中，可以使用修改 .gitignore 文件的方法。如果没有 .gitignore 文件，就自己创建一个，手动创建会提示你输入文件名称，因此，你要用git客户端创建：git客户端进入项目目录，执行 touch  .gitignore 即可创建该文件。
这个文件每一行保存了一个匹配的规则例如：
 *.a       # 忽略所有 .a 结尾的文件
 !lib.a    # 但 lib.a 除外
 /TODO     # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/    # 忽略 build/ 目录下的所有文件
doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
参考文档：[http://www.findme.wang/share/detail/id/440.html](http://www.findme.wang/share/detail/id/440.html)


好了，这里只是总结了Git上传项目的一些基本操作，要想更好地使用Git还需更进一步的学习。参考文档：[Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600)