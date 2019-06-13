# git

- bash粘贴指令为Shift+Insert, shell为右键
- git config --global credential.helper store
  - 可以将账号密码永久的存在本地，不用每次都输入，输入此命令设置后，下次再输入账号密码之后就会存起来
- 本地分支： 分支名   本地待推送的远端分支： origin 分支名   远端分支：remote 分支名
- git remote -v 查看当前关联的远端仓库
  - 以http:开头的均为http操作方式
  - 以git@开头的均为ssh操作方式
- git remote rm origin 删除当前关联的远端仓库
- git remote add origin [url] 添加关联的远端仓库
- git pull
  - 拉取远端分支最新代码覆盖更新本地分支
- git fetch -all
  - 拉取远端仓库最新代码更新origin仓库，并不会破坏本地仓库
- git rebase origin dev
  - rebase dev分支到当前分支
- git rebase --continue
  - 继续当前的commit rebase
- git rebase --skip
  - 跳过当前的commit rebase 冲突
- git rebase --abort
  - 退出当前rebase，代码回复到rebase之前的状态
- git merge origin dev
  - 将origin dev合并到当前分支,冲突以当前分支为准
  - PS：此命令建议仅用在origin dev分支已经rebase了当前分支之后
- git tag
  - 查看tag
- git tag v1.0.0
  - 给当前分支添加 tag v1.0.1
- git push --tag
  - 推送本地分支tag到远端对应分支
- git tag -d v1.0.1
  - 删除本地分支 tag v1.0.1
- git push origin tag -d v1.0.1
  - 删除远端对应分支 tag v1.0.1
- git tag -l "*-production*" --sort=-creatordate | head -n 1
  - 返回最新的带'-production'字段的tag,例如 v3.6.17-20181219-production
- git branch
  - 查看当前分支
- git branch -d dev-press
  - 删除本地分支 dev-press
- git push origin -d dev-press
  - 删除远端分支 dev-press
- git checkout .
  - 撤销当前工作区内所有改动
  - 慎用
- git checkout dev-press
  - 切换到分支 dev-press
- git checkout -b dev-press
  - 新建分支 dev-press并复制当前分支到新分支，再切换到新分支
- git push origin dev-press
  - 新建分支后使用，则推送本地分支到远端并创建远端dev-press分支
  - 末尾再加个`-u`参数则表示新建后关联分支
- git checkout -b co-bug origin/debug
  - 从远端分支拉取、新建并关联本地分支
- git branch --set-upstream-to origin/debug co-bug
  - 关联远端分支debug和本地分支co-bug
  - --set-upstream-to 可以用 -u 替换
- git push --set-upstream origin dev-zww
  - 新建远端分支dev-zww,并将当前分支与远端dev-zww分支关联
  - --set-upstream 可以用 -u 替换
- git reset --hard origin/master
  - 用origin master分支强行覆盖当前分支
  - PS：危险操作，请先确定当前分支的代码是否全部丢弃
- git reset --hard
  - 退回到上一个commit
- git reset --hard 7aba1ed57d8bdbc9274f8f2af61cd8ffc58a26f4
  - 退回到某个commit （7aba1ed57d8bdbc9274f8f2af61cd8ffc58a26f4 ， 通过git log 查看 commit记录）
- git reset --soft 7aba1ed57d8bdbc9274f8f2af61cd8ffc58a26f4
  - 类似于hard，但是回退只清除commit信息，所有改动依旧保留，commit之后只生成一个commit
- git clean -df
  - 撤销所有增删文件操作（reset和checkout一般只能撤销文件代码层面的修改，无法撤销部分增删文件的改动）
- git status
  - 查看文件改动信息
  - -s后缀为缩写展示
- git add 文件相对地址
  - 保存某个文件的改动
- git add .
  - 保存当前所有文件的改动
- git commit -m "123"
  - 把保存好的文件改动信息生成commit，并标记为“123”
- git commit --amend -m "234"
  - 再次提交当前改动生成commit“234”，并覆盖上一个commit

## 密钥登录

- ssh-keygen -t rsa -b 4096 -C "zhouww@git.reolink.com"
  - 生成 rsa型 4096长度的密钥文件，注释为zhouww@git.reolink.com
- 交互模式提示输入密钥存放地址，比如 /c/Users/Administrator/.ssh/zhouww@git.reolink.com
  - zhouww@git.reolink.com是文件名字
- 交互模式提示输入密码和二次确认密码，输入结束时，提示生成成功
- clip < ~/.ssh/
  - 查看ssh目录下密钥文件（有公钥和私钥）
  - 公钥为带.pub后缀的文件
- clip < ~/.ssh/zhouww\@git.reolink.com.pub
  - 复制公钥
  - 去git的账户settings中切换到SSH Keys这个tab，直接黏贴保存
- 之后每次登陆bash时输入 ssh-add ~/.ssh/zhouww\@git.reolink.com执行，输入私钥密码即可

### 每次登陆时开启ssh，所以直接保存到bash_profile文件，每次打开bash自动执行开启ssh操作

- vi ~/.bash_profile
  - 打开编辑bash_profile界面
  - 最初是命令行模式
  - 输入i进入插入模式（按ESC回到最初的命令行模式）
- 输入以下代码

```shell
  eval $(ssh-agent -s) > /dev/null
  trap 'test -n "$SSH_AGENT_PID" && eval `/usr/bin/ssh-agent -k` > /dev/null' 0
```

- 按ESC回到命令行模式，输入 : 进入底行模式
  - : wq (输入「wq」，存盘并退出vi)
  - : q! (输入q!， 不存盘强制退出vi)
- 代码无误后输入wq确认退出即可

## http式只输入一次账号密码（没有安全性）

- git config --global credential.helper store 设置git账号密码的信息凭证永久保存的地址在store（磁盘中）
  - 第一次输入账号密码后自动保存
  - bash安装时，默认有个credential.helper manager 选项 ，如果勾选了，即是把凭证存储在windows的凭证管理器中的普通凭证中
    - 此时如果要清除，可以去凭证管理器中找到删除，再用git config --system --unset credential.helper清除下设置
  - git config --list | grep credential 可以通过此行命令查看配置