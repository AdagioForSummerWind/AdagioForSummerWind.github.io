# Git


![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20221123201058.png)

### Q2. 什么是Git？

Git 是分布式版本控制系统（DVCS）。它可以跟踪文件的更改，并允许你恢复到任何特定版本的更改。

Git使用 C 语言编写。 GIT 很快，C 语言通过减少运行时的开销来做到这一点。
### **4.Git和Github有什么区别？**

[Git](https://bit.ly/31MeW9b)是一个分布式版本控制系统，用于在软件开发过程中跟踪源代码的变化。它有助于协调程序员之间的工作，但它可用于跟踪任何一组文件中的更改。Git 的主要目标是速度、数据完整性和对分布式非线性工作流的支持。

[GitHub](https://bit.ly/2rVhL7Q)是一个 Git 存储库托管服务，此外它还添加了许多自己的功能。GitHub 提供了基于 Web 的图形界面。它还为每个项目提供访问控制和多种协作功能，以及基本的任务管理工具。

### 命令
git revert HEAD 撤回最近一次提交

git rm [file]	从您的工作目录中删除文件并暂存删除。

git log 	列出当前分支的版本历史。

git show [commit]  
显示指定提交的元数据和内容更改。

git tag [commitID] 
用于为指定的提交提供标签。

git checkout [branch name]用于从一个分支切换到另一个分支。

git checkout -b [branch name]创建一个新分支并切换到它。

### Q7. git pull 和 git fetch 有什么区别？
git pull=git fetch + git merge

### Q9. 什么是 git stash?

首先应该解释 git stash 的必要性。

通常情况下，当你一直在处理项目的某一部分时，如果你想要在某个时候切换分支去处理其他事情，事情会处于混乱的状态。问题是，你不想把完成了一半的工作的提交，以便你以后就可以回到当前的工作。解决这个问题的答案是 git stash。

再解释什么是git stash。

**stash 会将你的工作目录，即修改后的跟踪文件和暂存的更改保存在一堆未完成的更改中，你可以随时重新应用这些更改。**

### Q10. 什么是git stash drop？

通过说明我们使用 `git stash drop` 的目的来回答这个问题。

`git stash drop` 命令用于删除隐藏的项目。默认情况下，它将删除最后添加的存储项，如果提供参数的话，它还可以删除特定项。

下面举个例子。

如果要从隐藏项目列表中删除特定的存储项目，可以使用以下命令：

**git stash list：**它将显示隐藏项目列表，如：

stash@{0}: WIP on master: 049d078 added the index file  
stash@{1}: WIP on master: c264051 Revert “added file\_size”  
stash@{2}: WIP on master: 21d80a5 added number to log

如果要删除名为 stash@{0} 的项目，请使用命令 **git stash drop stash@{0}**。


### **37\. 如何在不从文件系统中删除文件的情况下从 git 中删除文件？**

在 git add 期间必须小心，否则您最终可能会添加您不想提交的文件。但是， git rm 将从暂存区（索引）和文件系统（工作树）中删除它，这可能不是您想要的。

相反，使用 git reset：

`git reset filename`  ＃ 或者

`echo filename >> .gitingore`\# 将它添加到 .gitignore 以避免重新添加它

这意味着 git reset <paths> 与 git add <paths> 正好相反。\

### **39\. 告诉我 Git 中 HEAD、工作树和索引之间的区别。**

- 工作树/工作目录/工作区是您可以查看和编辑的（源）文件的目录树。
- 索引/暂存区是 <baseOfRepo>/.git/index 中的单个大型二进制文件，它列出了当前分支中的所有文件、它们的 SHA-1 校验和、时间戳和文件名——它不是另一个目录其中包含文件的副本。
- HEAD 用于引用当前签出分支中的最后一次提交。


### **42\. Git 中的 rebase 和 merge 有什么区别？**

- 在 Git 中，rebase 命令用于将更改从一个分支集成到另一个分支。它是“合并”命令的替代方法。rebase 和 merge 之间的区别在于 rebase 重写提交历史以产生直接的、线性的连续提交。
- 合并是 Git 将分叉的历史重新组合在一起的方式。git merge 命令帮助您将由 git branch 创建的独立开发线集成到一个分支中。

### ### **44\. 什么是 git cherry-pick？**

命令 git cherry-pick 通常用于将特定提交从存储库中的一个分支引入到另一个分支。另一个常见用途是将提交从维护分支转发或反向移植到开发分支。这与其他方式形成对比，例如 merge 和 rebase 通常将许多提交应用到另一个分支。

考虑：

`git cherry-pick <commit-hash>`
