# 一、在windows上安装Git

# 二、设置

由于git是分布式管理工具，需要输入用户名和邮箱作为标识，因此，需要输入用户名和邮箱

```bash

Administrator@DESKTOP-VGM68TT MINGW64 ~/Desktop/胡义飞的文档/公司笔记
$ git config --global user.name "huyifei"

Administrator@DESKTOP-VGM68TT MINGW64 ~/Desktop/胡义飞的文档/公司笔记
$ git config --global user.email "429543246@qq.com"

```

**注意：**git config --global参数，有了这个参数，表示这个机器上所有的Git仓库都会使用这个配置。

# 三、基本用法

## 1、创建版本库

版本库即为仓库（repository），也可以理解为目录，该目录中所有的文件都可以被Git管理，文件的修改删除Git也都能跟踪

**创建huyifei的版本库：**

```bash
Administrator@DESKTOP-VGM68TT MINGW64 ~/Desktop/胡义飞的文档/公司笔记
$ cd d:

Administrator@DESKTOP-VGM68TT MINGW64 /d
$ cd test

Administrator@DESKTOP-VGM68TT MINGW64 /d/test
$ mkdir huyifei

Administrator@DESKTOP-VGM68TT MINGW64 /d/test
$ cd huyifei/

Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei
$ pwd
/d/test/huyifei
```

## 2、添加文件到版本库

- 要添加文件到版本库，需要将这个目录变为git可以管理的仓库，命令：

```bash
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei
$ git init
Initialized empty Git repository in D:/test/huyifei/.git/
```

- 在huyifei目录下创建111.txt文件，内容：123456

- 使用命令将文件添加到暂存区，然后提交到仓库

```bash
#git add:将文件提交到暂存区
#git commit -m:将暂存区文件提交到仓库(单括号内为注释)
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git add 111.txt

Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git commit -m '第一次提交'
[master (root-commit) b11eee1] 绗竴娆℃彁浜? 1 file changed, 1 insertion(+)
 create mode 100644 111.txt
```

## 3、检查是否有未提交的文件

```bash
#git status:检查当前文件状态
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git status
On branch master
nothing to commit, working tree clean
```

## 4、检查文件是否被修改

修改111.txt，然后重新检查状态：

```bash
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   111.txt

no changes added to commit (use "git add" and/or "git commit -a")
#git diff:查看被修改的内容
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git diff 111.txt
diff --git a/111.txt b/111.txt
index 4632e06..c24a733 100644
--- a/111.txt
+++ b/111.txt
@@ -1 +1,2 @@
-123456
\ No newline at end of file
+123456
+i love you
\ No newline at end of file

```

文件被修改后，通过git status发现文件被修改，但是未提交。若想知道文件修改的内容，可以使用git diff的命令查看。

检查无误后，提交。

## 5、查看历史变更记录

```bash
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git commit -m '第二次提交'
[master 1aec8f2] 纰岃瘷闀侀簱鑴﹁劋宄▒? 1 file changed, 2 insertions(+), 1 deletion(-)

Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git log
commit 1aec8f2d619921a18167ec56d2a2b334ba5d93d5 (HEAD -> master)
Author: huyifei <429543246@qq.com>
Date:   Tue Jul 21 09:15:33 2020 +0800

    碌诙镁麓脦脤峤▒

commit 923ebccbd5c34ce61538c4167ac9f5331638680d
Author: huyifei <429543246@qq.com>
Date:   Tue Jul 21 09:13:50 2020 +0800

    111.txt

commit b11eee139bb62be41ae470d6bb7619dd77a6053c
Author: huyifei <429543246@qq.com>
Date:   Tue Jul 21 09:03:48 2020 +0800

    绗竴娆℃彁浜▒

```

查看历史记录：git log

## 6、版本回退

1. 查看当前文件内容

```bash
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ cat 111.txt
123456
i love you
beautiful girl

```

​	2.通过命令执行版本回退

```bash
#git reset --hard HEAD^ 回退版本
$ git reset --hard HEAD^
HEAD is now at 923ebcc 111.txt

Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ cat 111.txt
123456
i love you

```

​	3.回到最新版本

```bash
#git reflog 获得历史版本号
#git reset --hard ...  回退到当前选定的版本号
$ git reflog
923ebcc (HEAD -> master) HEAD@{0}: reset: moving to HEAD^
1aec8f2 HEAD@{1}: commit: 碌诙镁麓脦脤峤▒
923ebcc (HEAD -> master) HEAD@{2}: commit: 111.txt
b11eee1 HEAD@{3}: commit (initial): 绗竴娆℃彁浜▒

$ git reset --hard 1aec8f2
HEAD is now at 1aec8f2 纰岃瘷闀侀簱鑴﹁劋宄▒?

$ cat 111.txt
123456
i love you
beautiful girl
```

# 四、将本地文件推送到github仓库

1.检查文件是否还有未提交或者修改的，然后将文件提交到github仓库，命令：

```bash
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git remote add origin https://github.com/huyifei404/GitTest

```

解析：

$ git remote add origin https://github.com/huyifei404/GitTest将本地仓库和github仓库进行关联，在操作时将地址换成自己的。

2.执行命令

```bash
Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git remote add origin https://github.com/huyifei404/huyifei.git

Administrator@DESKTOP-VGM68TT MINGW64 /d/test/huyifei (master)
$ git push -u origin master
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (9/9), 696 bytes | 696.00 KiB/s, done.
Total 9 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/huyifei404/huyifei.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.

```



