### 1.svn add 命令 递归目录下所有文件
在所在文件夹下打开命令窗口，输入如下命令即可：

```
svn add . --no-ignore --force
```
### 2. 提交时报错 xx is already locked.Please execute the 'Clean up' command.
**解决办法：**不要鼠标右键选择 clean up，此时只需打开命令窗口，进入到锁定文件目录下，输入`svn cleanup`回车即可。
有些可能会遇到回车后报错：**svn 不是内部或外部命令**，那说明svn的命令行工具没有安装，需要先把原来的svn卸载掉，重新安装。
走到如下图所示这一步的时候，点击打叉右边的小三角，然后选中第一个，继续next安装。
![image.png](https://upload-images.jianshu.io/upload_images/18030682-043d142cb8c31e29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/18030682-4bb81dba1062b111.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装好之后，再回到上面的步骤打开命令行窗口，进入到锁定文件目录下，输入`svn cleanup`回车即可。

### 3. git提交报错 
`dst refspec V2.7_charge matches more than one
error: failed to push some refs to 'http://192.168.51.165:90/eicloud/eicloud-frontend.git'`
查了资料发现是tag和分支重名造成的，解决方法：先删除tag，重新打其他名字的tag，然后再提交就可以了
1.
删除本地tag：git tag -d tag-name
删除远程tag：git push origin :refs/tags/tag-name

2. 重新打tag（新tag不要跟分支同名哦）
3. 再提交代码就可以了