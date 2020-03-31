# git

## vscode终端使用bash

- 在settings.json中加入"terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe"
- 右键vscode图标，在属性中的兼容性里只勾选“以管理员身份运行”

## git命令

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
- git fetch --all
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
- git remote prune origin
  - 清理本地的远程分支记录
- git branch
  - 查看当前分支
- git branch dev-press
  - 新建分支 dev-press，但当前分支不会切换到dev-press分支
- git branch dev-press origin/master
  - 复制origin/master到新分支 dev-press，但当前分支不会切换到dev-press分支
- git branch -d dev-press
  - 删除本地分支 dev-press
- git branch -D dev-press
  - 强行删除本地分支 dev-press
- git push origin -d dev-press
  - 删除远端分支 dev-press
- git checkout .
  - 撤销当前工作区内所有改动
  - 慎用
- git checkout dev-press
  - 切换到分支 dev-press
- git checkout v0.1.0
  - 切换到tag为v0.1.0的代码状态（不是分支，此时还需要git checkout -b 一个新分支出来并切换出来）
- git checkout -b dev-press
  - 新建分支 dev-press并复制当前分支或tag到新分支，再切换到新分支
- git push origin dev-press
  - 新建分支后使用，则推送本地分支到远端并创建远端dev-press分支
  - 末尾再加个`-u`参数则表示新建后关联分支
- git checkout -b co-bug origin/debug
  - 从origin/debug分支拉取、新建并关联本地分支
- git checkout -b co-bug v0.1.0
  - 从v0.1.0复制新建co-bug分支，并切换当前分支到co-bug
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
  - 撤销到上一个commit
- git reset --hard 7aba1ed57d8bdbc9274f8f2af61cd8ffc58a26f4
  - 撤销到某个commit （7aba1ed57d8bdbc9274f8f2af61cd8ffc58a26f4 ， 通过git log 查看 commit记录）
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
- 删除 **.git** 仓库中的大文件
  - Git 维护着一个微型的文件系统，其中的文件也被称作数据对象
  - 所有的数据对象均存储于项目下面的 .git/objects中
  - 它会把你每次提交的文件的全部内容都会记录下来，所有当误操作把大文件提交到git仓库中，后续即使删除了文件，.git仓库依旧会有大文件记录
  - 参考[git内部原理](http://iissnan.com/progit/html/zh/ch9_0.html)
  - 步骤：
    - 切到项目根目录
      - 查看项目中哪个文件占用的空间比较大（非必要）
      - du -d 1 -h
    - 查看.git中占用空间最多的五个文件
      - git rev-list --objects --all | grep "$(git verify-pack -v .git/objects/pack/*.idx | sort -k 3 -n | tail -5 | awk '{print$1}')"
    - 删除历史提交过的大文件
      - git filter-branch --force --index-filter 'git rm -rf --cached --ignore-unmatch big-file.jar' --prune-empty --tag-name-filter cat -- --all
      - big-file.jar 指的是查看出来的文件名或者目录也可以
      - 如果显示 **xxxxx unchanged**, 说明repo里没有找到该文件, 请检查路径和文件名是否正确，重复上面的脚本，把所有你想删除的文件都删掉
    - 推送修改后的repo
      - git push origin master -f
    - 清理和回收空间
      - 虽然我们已经删除了文件, 但是我们的repo里面仍然保留了这些objects, 等待垃圾回收(GC), 所以我们要用命令彻底清除它, 并收回空间
      - rm -rf .git/refs/original/
      - git reflog expire --expire=now --all
      - git gc --prune=now

## 密钥登录

- ssh-keygen -t rsa -b 4096 -C "zhouww@git.reolink.com"
  - 生成 rsa型 4096长度的密钥文件，注释为zhouww@git.reolink.com
- 交互模式提示输入密钥存放地址，比如 /c/Users/dom/.ssh/zhouww@git.reolink.com
  - zhouww@git.reolink.com是文件名字
- 交互模式提示输入密码和二次确认密码，输入结束时，提示生成成功
- ls ~/.ssh/
  - 查看ssh目录下密钥文件（有公钥和私钥）
  - 公钥为带.pub后缀的文件
- clip < ~/.ssh/zhouww\@git.reolink.com.pub
  - 复制公钥
  - 去git的账户settings中切换到SSH Keys这个tab，直接黏贴保存
- 之后每次登陆bash时输入 ssh-add ~/.ssh/zhouww\@git.reolink.com执行，输入私钥密码即可

### 每次登陆时开启ssh，所以直接保存到bash_profile文件，每次打开bash自动执行开启ssh操作

- 启动bash，会使用bashrc文件，而使用bash进行登录操作，比如登录服务器，会使用bash_profile文件
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

### vscode内启动bash，使用 ssh-add 添加密钥进 ssh-agent 高速缓存的操作 会出现无法连接的情况

- 在 `.bash_profile` 同级目录下的 `.bashrc` 文件（没有则新建） 加入以下代码即可正常操作

```shell
# Add following code at the end of ~/.bashrc

# Check if ~/.pid_ssh_agent exists.
if [ -f ~/.pid_ssh_agent ]; then

    source ~/.pid_ssh_agent

    # Check process of ssh-agent still exists.
    TEST=$(ssh-add -l)

    if [ -z "$TEST" ]; then # Reinit if not.
        NEED_INIT=1
    fi
else
    NEED_INIT=1 # PID file doesm't exist, reinit it.
fi

# Try start ssh-agent.
if [ ! -z "$NEED_INIT" ]; then
    echo $(ssh-agent -s) | sed -e 's/echo[ A-Za-z0-9]*;//g' > ~/.pid_ssh_agent # save the PID to file.
    source ~/.pid_ssh_agent
fi
```

### ssh-add的一些指令

- ssh-add ~/.ssh/id_dsa
  - 把专用密钥添加到 ssh-agent 的高速缓存中
- 以下为参数选项
  - `-D`  删除ssh-agent中的所有密钥
  - `-d [file]`  从ssh-agent中的删除密钥
  - `-L`  显示ssh-agent中的公钥
  - `-l`  显示ssh-agent中的密钥
  - `-t [file]` life：对加载的密钥设置超时时间，超时ssh-agent将自动卸载密钥
  - `-X` 对ssh-agent进行解锁
  - `-x` 对ssh-agent进行加锁

## http式只输入一次账号密码（没有安全性）

- git config --global credential.helper store 设置git账号密码的信息凭证永久保存的地址在store（磁盘中）
  - 第一次输入账号密码后自动保存
  - bash安装时，默认有个credential.helper manager 选项 ，如果勾选了，即是把凭证存储在windows的凭证管理器中的普通凭证中
    - 此时如果要清除，可以去凭证管理器中找到删除，再用git config --system --unset credential.helper清除下设置
  - git config --list | grep credential 可以通过此行命令查看配置
