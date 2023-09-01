# git

# 一.创建版本库

```jsx
git init
```

该命令在指定文件创建可以管理的仓库

![Untitled](git%20a0858ea87e3d4ebab7b940bbdbab7a3a/Untitled.png)

git add XXXX      ———**添加到暂存区**

git commit        ———**提交到仓库**

git reset --hard HEAD^———**回退到上个版本**

git reset --hard HEAD^^———**回退到上上个版本**

git reset --hard HEAD~100———**回退到前100个版本**

git log———查看更改日志

**git checkout --readme.txt———把readme.txt文件在工作区做的修改全部撤销，这里有2种情况，如下：**

**1.readme.txt自动修改后，还没有放到暂存区，使用 撤销修改就回到和版本库一模一样的状态。
2.另外一种是readme.txt已经放入暂存区了，接着又作了修改，撤销修改就回到添加暂存区后的状态。
对于第二种情况，我想我们继续做demo来看下，假如现在我对readme.txt添加一行 内容为6666666666666，我git add 增加到暂存区后，接着添加内容7777777，我想通过撤销命令让其回到暂存区后的状态。**

# 二.**理解工作区与暂存区的区别？**

- 工作区：就是你在电脑上看到的目录，比如目录下testgit里的文件(.git隐藏目录版本库除外)。或者以后需要再新建的目录文件等等都属于工作区范畴。
- 版本库(Repository)：工作区有一个隐藏目录.git,这个不属于工作区，这是版本库。其中版本库里面存了很多东西，其中最重要的就是stage(暂存区)，还有Git为我们自动创建了第一个分支master,以及指向master的一个指针HEAD。

使用Git提交文件到版本库有两步：

1.是使用 git add 把文件添加进去，实际上就是把文件添加到暂存区。

2.使用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支上。我们继续使用demo来演示下：

三.配置github

如果在多个远程设备上操作，记得开始进行今日的操作前要先：

> git pull
> 

完成所有操作准备离开时要记得：

> git add .
> 
> 
> git commit -m ‘xx’
> 
> git push
> 

tip: 要保证本地库和server是同步的，不然忘记先pull，若其他远程设备上已经push了新的内容，自己这里是push不上去的，只能进行git pull合并，这个操作还会覆盖你已经更改了的部分，会非常痛苦qwq。

![Untitled](git%20a0858ea87e3d4ebab7b940bbdbab7a3a/Untitled%201.png)