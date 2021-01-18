# Git常用命令

![](https://pic1.zhimg.com/v2-e9edaef2103785c164876ff8b19b45ac_b.png)

## config

```sh
# 一般配置
git --version   //查看git的版本信息
git config --global core.editor vim
git config --global user.name   //获取当前登录的用户
git config --global user.email  //获取当前登录用户的邮箱
git config --global credential.helper store //永久保存用户名密码
# 运行一下命令缓存输入的用户名和密码：
git config --global credential.helper wincred
# 清除掉缓存在git中的用户名和密码
git credential-manager uninstall
```

## checkout/reset/commit/add



![](/Users/xiaohei/private/notebook/pic/687474703a2f2f6d61726b6c6f6461746f2e6769746875622e696f2f76697375616c2d6769742d67756964652f62617369632d75736167652e7376673f73616e6974697a653d74727565.svg)



![](../pic/687474703a2f2f6d61726b6c6f6461746f2e6769746875622e696f2f76697375616c2d6769742d67756964652f72657365742d636f6d6d69742e7376673f73616e6974697a653d74727565.svg)

```shell
git checkout -- <file name> # 撤销工作区的修改
git reset HEAD <file name> # 撤销暂存区的修改
git reset --hard <id> # 回退到历史版本
git reset --hard HEAD^ # 回退到上个版本;上上版本是HEAD^^，也可用HEAD~2表示，以此类推
git rm index.html --cached # 使用--cached 表示只删除缓存区中的内容
```







![](../pic/687474703a2f2f6d61726b6c6f6461746f2e6769746875622e696f2f76697375616c2d6769742d67756964652f62617369632d75736167652e7376673f73616e6974697a653d74727565.svg)

## diff

![](../pic/687474703a2f2f6d61726b6c6f6461746f2e6769746875622e696f2f76697375616c2d6769742d67756964652f646966662e7376673f73616e6974697a653d74727565.svg)

```shell
git diff 						# 暂存区和工作区
git diff --cached 	# 暂存区和历史区
git diff HEAD/master 		# 历史区和工作区（修改）
```



## Merge 三方合并

![](../pic/687474703a2f2f6d61726b6c6f6461746f2e6769746875622e696f2f76697375616c2d6769742d67756964652f6d657267652e7376673f73616e6974697a653d74727565.svg)

## other


```shell
# 远程仓库
push -u # -u参数 upstream
git push origin master -u   # 获取最新代码
git remote add origin <url>  # 连接远程仓库 
git remote -v # 查看远程
git remote rm origin # 删除远程仓库 

```

```shell
git reflog # 查看所有版本的id
git log --oneline --graph --decorate
```

```
# 与标签有关的
- 为当前版本打标签：git tag 标签名
- 为历史版本打标签：git tag 标签名 该版本ID
- 指定标签说明：git tag –a 标签名 –m "标签说明" [可选：版本ID]
- 查看所有标签：git tag
- 查看某一标签：git show 标签名
- 删除某一标签：git tag –d 标签名
```

```shell
# 暂存
git stash apply 
git stash drop # 丢掉保存的内容
```

```shell
git cherry-pick  # 命令「复制」一个提交节点并在当前分支做一次完全一样的新提交。
```

```
git-tips:  https://github.com/521xueweihan/git-tips
```

### 统计

1.根据用户名时间段统计 

```
git log --author="username" --since=2018-01-01 --until=2019-12-31 --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -
```

2.查看提交者排名前N位

```
git log --pretty='%aN' | sort | uniq -c | sort -k1 -n -r | head -n 5
```

3.提交数统计

```
git log --oneline | wc -l
```

4.根据用户名统计

```shell
git log --author="liupengyu" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -
```

5.根据时间段统计 

```
git log --since=2018-01-01 --until=2018-12-31 --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'
```

6.统计每个人的增删行数

```shell
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s ", add, subs, loc }' -; done
```

7.贡献者统计

```
git log --pretty='%aN' | sort -u | wc -l
```