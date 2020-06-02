# 一、创建本地库

1、git init        创建本地库

2、git clone  把远程库clone到本地库

3、git add -A   将本地所有的更改添加到暂存区

4、git commit

- git commit  -m "这里写提交的注释"    将暂存区的更改提交到本地库
- git commit -a -m "这里写注释"   直接跳过暂存区，将工作区的更改直接提交到本地库

5、git status    查看文件的状态(可以查件工作区和暂存区的变化，也可以查看暂存区和本地库之间的变化)

6、git diff  [<filename>]

- git diff    查看工作区与暂存区文件内容差异（不写文件名就是查看所有文件）
- git diff --staged   查看暂存区与最后一次提交的文件内容差异

7、git rm

- git rm  <filename>     删除本地区和暂存区的文件,在下次提交时就不会跟踪这个文件了
- git rm --cached <filename>    将文件从本地库和暂存区删除，但不会删除工作区文件，会直接取消git跟踪