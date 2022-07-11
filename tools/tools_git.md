# git 使用说明

### 1.git config
##### git 配置文件
+ Git有三个配置文件分别是版本库级别的配置文件、全局配置文件（用户主目录下
	和系统级配置文件（/etc目录下）。其中版本库级别配置文件的优先级最高，全局配
	置文件其次，系统级配置文件优先级最低。这样的优先级设置就可以让版本库.git目
	录下的config文件中的配置可以覆盖用户主目录下的Git环境配置。而用户主目录下
	的配置也可以覆盖系统的Git配置文件。
	
##### use case
	git config -e  //编辑版本库的配置文件
	git config -e --global  //编辑全局配置文件
	git config -e --system  //便捷系统配置文件
	git config --global user.name "your name"  //设置全局user.name
	git config --global user.email your_email  //设置全局user.email
	git config user.name //读取配置项user.name
	
### 2.git 暂存区
在版本库.git目录下，有一个index文件，它实际上就是一个包含文件索引的目录树，像是一个虚拟的工作区，也就是git暂存区。在这个虚拟工作区的目录树中，记录了文件名、文件的状态信息（时间戳、文件长度等）。文件的内容并不存储其中，而是保存在Git对象库.git/objects目录中，文件索引建立了文件和对象库中对象实体之间的对应。下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系。
 ![图1]() 

+ 说明：
  + 图中左侧为工作区，右侧为版本库。在版本库中标记为index的区域是暂存区（stage，亦称index），标记为master的是master分支所代表的目录树。
  + 图中可以看出此时HEAD实际是指向master分支的一个“游标”。所以图示的命令中出现HEAD的地方可以用master来替换。
  + 图中的objects标识的区域为Git的对象库，实际位于.git/objects目录下
  + 当对工作区修改（或新增）的文件执行git add命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。
  + 当执行提交操作（git commit）时，暂存区的目录树写到版本库（对象库）中，master分支会做相应的更新。即master最新指向的目录树就是提交时原暂存区的目录树
  + 当执行git reset HEAD命令时，暂存区的目录树会被重写，被master分支指向的目录树所替换，但是工作区不受影响。
  + 当执行git rm –cached <file>命令时，会直接从暂存区删除文件，工作区则不做出改变。
  + 当执行git checkout .或者git checkout – <file>命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区的改动。
  + 当执行git checkout HEAD .或者git checkout HEAD <file>命令时，会用HEAD指向的master分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动
  
### 3.git 版本库
+ git的版本库放在工作区的根目录下，即工作区根目录下的.git目录。所有的版本控制操作（除了和其他远程版本库之间的互操作）都在本地即可完成
。使用git grep命令可以对版本库中的文件进行内容搜索。例如：git grep 'hello world'
+ 当工作区中包含了子目录，在子目录中执行Git命令时，如何定位版本库呢？实际上，当在Git工作区目录下执行操作的时候，会对目录依次向上递归查找.git 目录，找到的.git目录就是工作区对应的版本库，.git所在的目录就是工作区的根目录，文件.git/index记录了工作区文件的状态（实际上是暂存区的状态）.例如在非Git工作区执行git命令，会因为找不到.git目录而报错。


### 4.文件忽略
##### 说明：
+ Git提供了文件忽略功能。当对工作区某个目录或者某些文件设置了忽略后，再执行git status查看状态时，被忽略的文件即使存在也不会显示为未跟踪状态，甚至根本感觉不到这些文件的存在。
+ git文件忽略通过.gitignore文件实现。.gitignore文件可以放在任何目录下，它的作用范围是其所处的目录及其子目录。因此我们一般在工作区根目录下维护一个.gitignore文件

### 5.git rev-parse
##### 说明：
+ 命令git rev-parse是Git的一个底层命令，其功能非常丰富（或者说杂乱）

##### use case:

	git rev-parse --symbolic --branches //显示分支
	git rev-parse --symbolic --tags //显示tag
	git rev-parse --symbolic --glob=refs/*  //显示定义的所有引用
	git rev-parse  HEAD //显示HEAD对应的SHA1哈希值(commit号)
	git rev-parse --git-dir //显示显示版本库.git目录所在的位置
	git rev-parse --show-toplevel //显示工作区根目录。
	git rev-parse --show-prefix //显示相对于工作区根目录的相对目录。
    git rev-parse --show-cdup //显示从当前目录（cd）后退（up）到工作区的根的深度

### 6.git status
##### 说明：
 + git status 查看文件状态
 
##### use case:
	git status //显示文件状态
	git status -s //精简模式，显示文件状态
	git status -b //显示文件状态的同时，显示当前分支信息
	
	

### 7.git log
##### 说明：
+ 显示提交历史

##### use case:
	git log //显示提交历史
	git log -p //按补丁格式显示每个更新之间的差异
    git log --word-diff //按 word diff 格式显示差异
    git log --stat	//显示每次更新的文件修改统计信息
    git log --shortstat	 //只显示 --stat 中最后的行数修改添加移除统计
    git log --name-only	 //仅在提交信息后显示已修改的文件清单。
    git log --name-status	//显示新增、修改、删除的文件清单。
    git log --abbrev-commit //仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。
    git log --relative-date //使用较短的相对时间显示（比如，“2 weeks ago”）
    git log --graph	 //显示 ASCII 图形表示的分支合并历史。
    git log --pretty  //使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。
    git log --oneline	--pretty=oneline --abbrev-commit 的简化用法


### 8.git add
##### 说明：
+ 将文件(改动)添加到暂存区

##### use case:
	git add <file> //将文件添加到暂存区
	git add -u //将（被版本库追踪的）本地文件的变更（修改、删除）全部记录到暂存区中
	git add -A //将工作区中所有改动以及新增文件添加到暂存区
	git add -i //进入一个交互式界面, 在此界面中可以选择性添加文件到暂存区

### 9.git rm
##### 说明：
+ 在本地删除如果要反映在暂存区中
+ 使用rm命令删除文件，文件只是在本地进行了删除，尚未加到暂存区（提交任务）中。也就是说：直接在工作区删除，对暂存区和版本库没有任何影响。
+ 对于不想删除的文件执行git checkout -- \<file>可以让文件在工作区重现。

##### use case:
	git rm <file> //将文件在本地删除，并且反应在暂存区里
	git rm -f //强制将文件在本地删除，并且反应在暂存区里
	git rm --cached //将文件在暂存区里移除
	git rm -r //递归删除目录
	
	

### 10.git diff
##### 说明:
+ 通过使用不同的参数调用git diff命令，可以对工作区、暂存区、HEAD中的内容两两比较。下面的这个图，展示了不同的git diff命令的作用范围
 ![图2]()

##### use case:
	git diff //工作区和暂存区比比较
	git diff --cached //暂存区和head比较
	git diff HEAD //工作区和HEAD比较
	git diff <tag A> <tag B> //比较两个tag
	git diff <tag A> //比较工作区和tag A
	git diff --cached <tag A> //比较暂存区和tag A
	git diff <commit1> <commit1> //比较commit1 和 commit2
	git diff <path1> <path2> //比较两个文件或目录
	git diff --word-diff //逐词比较

### 11.git blame
##### 说明：
+ 文件追溯：在软件开发过程中当发现Bug并定位到具体的代码时，Git的文件追溯命令可以指出是谁在什么时候，什么版本引入的此Bug。
当针对文件执行git blame命令，就会逐行显示文件，在每一行的行首显示此行最早是在什么版本引入的，由谁引入。

	
### 12.git commit
##### 说明：
+ 不要使用git commit -a.实际上Git的提交命令（git commit）可以带上-a参数，对本地所有变更的文件执行提交操作，包括本地修改的文件，删除的文件，但不包括未被版本库跟踪的文件。这个命令的确可以简化一些操作，减少用git add命令标识变更文件的步骤，但是如果习惯了使用这个“偷懒”的提交命令，就会丢掉Git暂存区带给用户最大的好处：对提交内容进行控制的能力。

##### use case:
	git commit -m <msg>  //将<msg>的内容作为提交说明
	git commit -F <file>  //使用文件内容作为提交说明
	git commit --allow-empty  //允许空提交
	git commit --allow-empty-message  //允许提交说明为空
	git commit --amend //修改提交说明

### 13.git stash
##### 说明：
+ 命令git stash可以用于保存和恢复工作进度

##### use case:
	git stash //保存当前工作进度。会分别对暂存区和工作区的状态进行保存。
    git stash list //显示进度列表。此命令显然暗示了git stash可以多次保存工作进度，并且在恢复的时候进行选择
    git stash pop [<stash>] //从该<stash>(（来自于git stash list显示的列表）)中恢复。恢复完毕也将从进度列表中删除<stash>。
    git stash pop [–index] //除了恢复工作区的文件外，还尝试恢复暂存区。
    git stash apply [–index] [<stash>] //除了不删除恢复的进度之外，其余和git stash pop命令一样
    git stash drop [<stash>] //删除一个存储的进度。缺省删除最新的进度。
    git stash clear //删除所有存储的进度
    git stash branch <branchname> <stash> //基于进度创建分支
 

### 14.git checkout
##### 说明：
+ 检出命令（git checkout）是Git最常用的命令之一，同样也很危险，因为这条命令会重写工作区。

##### 语法：
	用法一： git checkout [-q] [<commit>] [--] <paths>...
	用法二： git checkout [<branch>]
	用法三： git checkout [-m] [[-b|--orphan] <new_branch>] [<start_point>]
	
	上面列出的第一种用法和第二种用法的区别在于，第一种用法在命令中包含路径<paths>。
	为了避免路径和引用（或者提交ID）同名而冲突，可以在<paths>前用两个连续的短线（减号）作为分隔。
	
	第一种用法的<commit>是可选项，如果省略则相当于从暂存区（index）进行检出。
	因此重置一般用于重置暂存区（除非使用--hard参数，否则不重置工作区），而检出命令主要是覆盖工作区
	（如果<commit>不省略，也会替换暂存区中相应的文件）。
	
	第一种用法（包含了路径<paths>的用法）不会改变HEAD头指针，主要是用于指定版本的文件覆盖工作区中对应的文件。
	如果省略<commit>，会拿暂存区的文件覆盖工作区的文件，否则用指定提交中的文件覆盖暂存区和工作区中对应的文件。
	
	第二种用法（不使用路径<paths>的用法）则会改变HEAD头指针。
	
	第三种用法主要是创建和切换到新的分支（<new_branch>），新的分支从<start_point>指定的提交开始创建。
	新分支和我们熟悉的master分支没有什么实质的不同，都是在refs/heads命名空间下的引用。

##### 实际完成的操作：
+ 下面的版本库模型图描述了git checkout实际完成的操作
  ![图3]()
+ 实际完成的操作：
 + git checkout branch。检出branch分支。要完成如图的三个步骤，更新HEAD以指向branch分支，以branch指向的树更新暂存区和工作区。
 + git checkout、git checkout HEAD 汇总显示工作区、暂存区与HEAD的差异
 + git checkout -- \<filename> 用暂存区中filename文件来覆盖工作区中的filename文件。相当于取消自上次执行git add filename以来（如果执行过）本地的修改。这个命令很危险，因为对于本地的修改会悄无声息的覆盖，毫不留情。
 + git checkout branch -- \<filename> 维持HEAD的指向不变。将branch所指向的提交中的filename替换暂存区和工作区中相应的文件。注意会将暂存区和工作区中的filename文件直接覆盖
 + git checkout -- . 或写做 git checkout . 注意：git checkout命令后的参数为一个点（“.”）。这条命令最危险！会取消所有本地的修改（相对于暂存区）。相当于将暂存区的所有文件直接覆盖本地文件，不给任何确认的机会！

### 15.git reset
##### 说明：
+ 重置命令（git reset）是Git最常用的命令之一，也是最危险，最容易误用的命令

##### 语法：
	用法一： git reset [-q] [<commit>] [--] <paths>...
	用法二： git reset [--soft | --mixed | --hard | --merge | --keep] [-q] [<commit>]
	
	上面列出了两个用法，其中 <commit> 都是可选项，可以使用引用或者提交ID，
	如果省略 <commit> 则相当于使用了HEAD的指向作为提交ID。
	
	上面列出的两种用法的区别在于，第一种用法在命令中包含路径<paths>。
	为了避免路径和引用（或者提交ID）同名而冲突，可以在<paths>前用两个连续的短线（减号）作为分隔。
	
	第一种用法（包含了路径<paths>的用法）不会重置引用，更不会改变工作区，而是用指定提交状态（<commit>）下的文件（<paths>）替换掉暂存区中的文件。
	例如命令git reset HEAD <paths>相当于取消之前执行的git add <paths>命令时改变的暂存区。
	
	第二种用法（不使用路径<paths>的用法）则会重置引用。根据不同的选项，可以对暂存区或者工作区进行重置。

##### 实际完成的操作:
+ 下图，显示了不同的参数对第二种重置语法的影响。
![图4]()
+ 命令格式:git reset [–soft | –mixed | –hard ] [<commit>]
	+ 使用参数--hard，如：git reset –hard <commit> 会执行上图中的1、2、3全部的三个动作:
		+ 1.替换引用的指向。引用指向新的提交ID。
		+ 2.替换暂存区。替换后，暂存区的内容和引用指向的目录树一致。
		+ 3.替换工作区。替换后，工作区的内容变得和暂存区一致，也和HEAD所指向的目录树内容相同。
	+ 使用参数--soft，如:git reset –soft <commit>。会执行上图中的操作1。即只更改引用的指向，不改变暂存区和工作区.
	+ 使用参数--mixed或者不使用参数（缺省即为--mixed），如:git reset <commit>。会执行上图中的操作1和操作2。即更改引用的指向以及重置暂存区，但是不改变工作区。
+ use case：
	+ git reset、git reset HEAD：
		+ 仅用HEAD指向的目录树重置暂存区，工作区不会受到影响，相当于将之前用git add命令更新到暂存区的内容撤出暂存区。引用也未改变，因为引用重置到HEAD相当于没有重置。
	+ git reset -- \<filename> 、git reset HEAD \<filename> 
		+ 仅将文件filename撤出暂存区，暂存区中其他文件不改变。相当于对命令git add filename的反向操作。
	+ git reset –soft HEAD^ 
		+ 工作区和暂存区不改变，但是引用向前回退一次。当对最新提交的提交说明或者提交的更改不满意时，撤销最新的提交以便重新提交。
	+ git reset HEAD^、git reset –mixed HEAD^
	   + 工作区不改变，但是暂存区会回退到上一次提交之前，引用也会回退一次。
	+ git reset –hard HEAD^
		+ 彻底撤销最近的提交。引用回退到前一次，而且工作区和暂存区都会回退到上一次提交的状态。自上一次以来的提交全部丢失 
 

	
### 16.git revert
##### 说明：
+ 反向提交：要想修正一个错误历史提交的正确做法是反转提交，即重新做一次新的提交，相当于错误的历史提交的反向提交，修正错误的历史提交。
+ git revert 不会改变已有的提交和已有的提交历史，而是会增加新的提交。

##### use case:
	git revert HEAD //运行该命令相当于将HEAD提交反向再提交一次
	git revert HEAD^ //运行该命令相当于将倒数第二次提交反向再提交一次

### 17.git tag
### 18.git branch
### 19.git fetch
### 20.git merge
### 21.git pull
### 22.git push
 	
	