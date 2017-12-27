add merge --no-ff haha

www.gitignore.io

git config --global alias.co checkout

<1>

git reset --hard HEAD^  //版本回退

//HEAD^^ HEAD^^^ ...HEAD~100(HEAD指向的的是当前版本，通用 git reset --hard commit_id)

//git reset HEAD^  谨记，如果为未添加--hard,则撤回到暂存区，工作区不回退，可git status查看状态

//commit_id的获取通过 git log | git reflog |  git log --graph --pretty=oneline --abbrev-commit
-------------------
//版本回退的问题
推送到远程仓库的情况下，假设未回退之前为
a->b->c
进行回退后
a->b
修改后，
a->b->d

这时push remote时会失败，因为本地库是a->b->d 远程库 a->b->c 相对于remote库来说，d之后的commit_id对不上了（猜测）

两种方案：
1. push 时强制远程仓库做出改变 后面加参数 --force 不推荐
2. 反向操作 使用git revert <commit_id> 即 a->b->c->e->d =>实现a->b->d （e为b 的快照）

<2>
git checkout -- file 

//将文件在工作区的修改全部撤销，如果已经在本地commit（push到远程仓库，就很残酷了） 可参考之前的版本回退
//总之是让文件回到最近一次 git commit 或 git add 时的状态


<3>
git diff    #是工作区(work dict)和暂存区(stage)的比较
git diff --cached    #是暂存区(stage)和分支(master)的比较

<4> 紧急处理bug
git stash //暂时冰冻当前正在进行的工作，是的git status id NULL

git checkout master //切换到bug分支
git checkout -b issue_102

// 修复bug
// ...

//.. add ..|..commit ..

git checkout master

git merge --no-ff -m "merge bug fix issue_102" issue_102

//bug 修复完毕切回 dev 继续 coding
git checkout dev
git stash list 

//恢复现场
 git stash apply stash@{0} //stash内容还保存在 stash list中，可通过 git stash pop 还原的同时，删除drop stash
 git stash drop stash@{0}
//用git stash 多次冰冻现场时，最新的stash 在列表中始终是stash@{0} 可以猜测其内部是通过栈存储的，其中stash@{0}为栈指针
//不难想象可以用git stash 配合 git stash drop stash@{?}（即"储藏"之后不进行"还原"操作）来达到git checkout -- file 一样的效果
//与git checkout -- file 不同的是 git stash 总是让文件回到最近一次 git commit 的状态（是否git add 到暂存区无影响）


//测试localcommit后，不使用--hard 版本回退控制，回退到缓冲区后，尝试使用git status 还原操作是可行（git checkout --file 也适用<因为最近的一次操作是add>，提交到暂存区的更改| 所做的处理，就是用git stash 代替 --hard 参数）


//还可以尝试，commit之后 在add file

//经过测试，可以认为 git reset --hard <commit_id> 中的--hard 等价于git checkout -- file 或者 git stash ,git stash drop stash@{0}（因为reset 回退到暂存区，之前add到暂存区的内容，也都擦除了）
