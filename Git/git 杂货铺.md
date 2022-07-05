- [参考链接](https://blog.csdn.net/qq_37140632/article/details/85786089?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-85786089-blog-110071195.pc_relevant_antiscanv3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-85786089-blog-110071195.pc_relevant_antiscanv3&utm_relevant_index=2)

- 想要分支之间互不干扰，有两种方式
  - git add、git commit 然后进行切换
  - `git stash`
    - 隐藏当前工作现场，这个时候用 git status 查看工作区是干净的，所以就可以放心地去其他分支了。用 `git stash list`可以查看隐藏起来的工作现场
  - 恢复
    - 用 `git stash apply`恢复，但是恢复后，stash 内容并不删除，需要用 `git stash drop`来删除；
    - 用 `git stash pop`，恢复的同时把stash内容也删了，这时候用 `git stash list`就看不到任何 stash 内容了



- 合并冲突时，取消合并
  - `git merge --abort`
- 删除分支
  - `git branch --delete dev`

- 版本回退

  - ```
    git reset --hard HEAD^
    ```

- 只克隆最近一次的commit，而不克隆所有的提交记录
  - git  clone --depyh=1
