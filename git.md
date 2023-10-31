# git

## vscode终端使用bash

- 在settings.json中加入"terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe"
- 右键vscode图标，在属性中的兼容性里只勾选“以管理员身份运行”

## git命令

- bash粘贴指令为Shift+Insert, shell为右键
- git config credential.helper store --global
  - 可以将账号密码永久的存在本地，不用每次都输入，输入此命令设置后，下次再输入账号密码之后就会存起来
- 本地分支： 分支名   本地待推送的远端分支： origin 分支名   远端分支：remote 分支名
- git remote -v 查看当前关联的远端仓库
  - 以http:开头的均为http操作方式
  - 以git@开头的均为ssh操作方式
- git rm -r --cached 文件/文件夹名字 取消文件被版本控制
  - add过的文件，通过.gitignore， 无法取消跟踪，必须要先取消版本控制后才能生效
  - 切分支后，不被跟踪的文件会被删除
- git update-index --assume-unchanged /path/to/file #忽略跟踪
  - git update-index --no-assume-unchanged /path/to/file #恢复跟踪
  - 可以强制本地不跟踪指定文件
  - 相对路径必须要 ./ 开头
- git remote rm origin 删除当前关联的远端仓库
- git remote add origin [url] 添加关联的远端仓库
- git remote prune origin
  - 清理本地的远程分支记录
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
- git merge origin/dev
  - 将origin dev合并到当前分支,冲突以当前分支为准
- git merge origin/dev --no-ff
  - 将origin dev合并到当前分支,冲突以当前分支为准, graph 树中两分支记录不会自动合并
- git merge origin/dev --no-commit
  - 将origin dev合并到当前分支,冲突以当前分支为准, 合并时即使没有冲突也不会自动合并打commit，此时可以手动检查差异
- git tag
  - 查看tag
- git tag -l "*-production*" --sort=-creatordate | head -n 1
  - 返回最新的带'-production'字段的tag,例如 v3.6.17-20181219-production
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
  - 查看本地工作空间的分支
- git branch -r
  - 查看本地远端仓库的分支
- git branch -v
  - 查看本地工作空间的分支及对应的最新commit记录
- git branch -vv
  - 查看本地所有的分支及对应的远端分只
- git branch -rv
  - 查看本地远端仓库的分支及对应的最新commit记录
- git branch -av
  - 查看本地所有的分支及对应的最新commit记录
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
  - 切换到分支 dev-press, 如果本地没有此分支而本地待推送的远端仓库有，则会依据远端仓库的分支创建对应的本地分支并关联
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
- git branch co-bug --set-upstream-to origin/debug
  - 关联远端分支debug和本地分支co-bug
  - --set-upstream-to 可以用 -u 替换
- git push origin dev-zww --set-upstream
  - 新建远端分支dev-zww,并将当前分支与远端dev-zww分支关联
  - --set-upstream 可以用 -u 替换
- git reset --hard origin/master
  - 用origin master分支强行覆盖当前分支
  - PS：危险操作，请先确定当前分支的代码是否全部丢弃
- git reflog
  - 可以找出所有曾经存在的commit，包括被reset清除的，可以在此找出误删的commit号
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
  - 保存某个文件的改动, 存入暂存区
- git add .
  - 保存当前所有文件的改动，存入暂存区
- git commit -m "123"
  - 把保存好的文件改动信息生成commit，并标记为“123”
- git commit --amend
  - 会进入vi模式，第一行是最新的commit信息，可以进行修改
- git commit --amend -m "234"
  - 再次提交当前改动生成commit“234”，并覆盖上一个commit
- 删除 **.git** 仓库中的大文件
  - Git 维护着一个微型的文件系统，其中的文件也被称作数据对象
  - 所有的数据对象均存储于项目下面的 .git/objects中
  - 它会把你每次提交的文件的全部内容记录下来，当误操作把大文件提交到git仓库中，后续即使删除了文件，.git仓库依旧会有大文件记录
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

### 修改历史某个commit信息

- 只能通过 git commit --amend 命令修改commit信息，而这个命令只能修改最新的commit的信息，所以需要通过git rebase -i HEAD~n 来筛选出最新的n个commit，这个命令会进入vi模式，输入i，选择你要修改的那个commit（一次修改只能修改一个），把 'pick' 改为 'edit'，按 'ESC'， 输入 ':wq' 自动保存退出， 此时，你要修改的那个commit会暂时被放置为最新的commit，然后通过 git commit --amend 命令在vi界面修改第一行显示的commit信息，保存退出后， git rebase --continue 继续下一个改动，直至改完

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

### ~/.bash_profile 与 ~/.bashrc 的区别

- ~/.bash_profile: 每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次! 默认情况下,它设置一些环境变量, 它与 bashrc 是父与子的关系
- ~/.bashrc：该文件包含专用于某个用户的bash shell的bash信息,当该用户登录时以及每次打开新的shell时,该文件被读取
- 打开bash命令行窗口（调用的是git/git-bash.exe），只会执行~/.bash_profile
- 打开vscode中的bash（调用的是git/bin/bash.exe），只会执行~/.bashrc
- 在 ~/.bash_profile 中 调用 ~/.bashrc , 在 ~/.bashrc 中编写代码就能兼容以上两种情况

### 为了避免每次登陆时开启ssh，可以直接保存到bash_profile文件

- 启动bash
- vi ~/.bash_profile
  - 打开编辑bash_profile界面
  - 最初是命令行模式
  - 输入i进入插入模式（按ESC回到最初的命令行模式）
- 输入以下代码

```shell
  eval $(ssh-agent -s) > /dev/null
  # 0 指的是退出bash时
  # 退出bash时关掉agent程序
  trap 'test -n "$SSH_AGENT_PID" && eval `/usr/bin/ssh-agent -k` > /dev/null' 0
```

- 按ESC回到命令行模式，输入 : 进入底行模式
  - : wq (输入「wq」，存盘并退出vi)
  - : q! (输入q!， 不存盘强制退出vi)
- 代码无误后输入wq确认退出即可

### 多进程共用同一个ssh-agent

- 在 `.bash_profile` 同级目录下的 `.bashrc` 文件（没有则新建） 加入以下代码即可正常操作

```shell
# ~/.bash_profile 中

# ~/.bash_profile 中 不能有关闭agent的程序，比如 上面的trap操作
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi


# ~/.bashrc 中

# ---此为electron windows设置代码签名证书路径的配置，注释是oem的配置，看情况修改

export CSC_LINK="~/reolink.pfx"

export CSC_KEY_PASSWORD=82Fj#^f28fWk836ll

# export CSC_LINK="~/uniden_solo.pfx"

# export CSC_KEY_PASSWORD=qmNi66pA7F30x#JK

# export CSC_LINK="~/ludafarm.pfx"

# export CSC_KEY_PASSWORD=

# -----Check if ~/.pid_ssh_agent exists.
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


# ---以下为bash上启用vpn的快捷方法
proxy () {
  export http_proxy="http://127.0.0.1:10801"
  export https_proxy=$http_proxy
  export socks5_proxy="socks5://127.0.0.1:10800"
  echo "HTTP Proxy on"
}

# noproxy
noproxy () {
  unset http_proxy
  unset https_proxy
  echo "HTTP Proxy off"
}
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

## git 配置增删改

- 查看配置
  - git config --global --list
  - git config --global user.name
- 增加配置
  - git config  --global --add user.name 随便
- 删除配置
  - git config  --global --unset user.name
- 修改配置
  - git config  --global user.name 真随便
- 文件换行符推荐全部改为LF
  - git config  --global core.autocrlf input
  - git config  --global core.safecrlf true
  - vscode settings 中 搜索 EOL，设置为LF

## git 自定义命令

- git config --global alias.name  "the operation command"
  - 例如git config --global alias.pr "pull --rebase" //重命名git rebase操作
- 自定义合并命令
  - 例如 git config --global alias.cmp '!f() { git add -A && git commit -m "日志" && git push; }; f'
  - shell里方法声明不需要声明标志
- 相当于在 .gitconfig文件中添加

```text
[alias]
  last = log -1 HEAD
  lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
  tags = tag -n1 -l
  subupdate = submodule update --init --recursive
  rupdate = remote update origin -p
```

## gitsubmodule 使用和注意事项

### 子模块的使用

- 项目中，/src 和 /injection 目录使用了 gitsubmodule。下面按照项目开发流程的顺序介绍具体使用方式和注意事项

#### clone 项目

- 默认子模块目录下无任何内容。需要在项目根目录执行如下命令完成子模块的下载：
  - git submodule init 后 git submodule update
  - 或者 git submodule update --init --recursive
- clone 项目后，如果需要新增子项目
  - `git submodule add <url> <path>`
  - url为子模块的git地址，path为该子模块存储的目录路径
  - 执行成功后，git status会看到项目中修改了.gitmodules，并增加了一个新文件（为刚刚添加的路径）
  - git commit提交即完成子模块的添加（把当前子项目的信息提交到主项目的git信息里）

#### 子模块的维护和修改

- 准备对 submodule 做修改和维护时，注意在切换到子模块目录后，查看当前所在分支。如果刚执行过子模块的初始化更新，则子模块默认当前分支不在任何一个分支上，HEAD 指向是某个具体的提交记录。具体如：/e/projects/device-web-client/injection ((da79e69...))，此时需要切换到需要修改的分支上，然后在进行开发。其余操作都相同
- 如果不慎忘记切换分支，但还未提交，直接切换分支即可。但是如果已经做了提交，可以用 `cherry-pick` 命令挽救。具体做法如下：
  1. 用 `git checkout your-branch` 将 HEAD 从游离状态切换到 your-branch 分支, 这时候，git 会报 warning 说有一个提交没有在 branch 上，记住这个提交的 change-id（假如change-id为 aaaaa)
  2. 用 `git cherry-pick aaaa` 来将刚刚的提交作用在 your-branch 分支上
  3. 用 `git push` 将更新提交到子模块的远程仓库中
- 分支之间合并更新等操作执行前，最好先将子模块中代码删除，只留文件夹，主项目和子项目同时需要更新时，先更新主项目

#### 子模块的更新

- 当前项目
  - 如果 submodule 在当前项目中更新后，父项目中依赖的版本号也需要更新。需要在 `git pull` 拉取主项目更新之后，调用 `git submodule update` 命令来更新 submodule 的提交信息。否则可能会在之后的维护中把旧的 submodule 依赖信息提交了。因此开发过程中应注意：
  1. `git pull` 之后，执行 `git status` 查看是否有子模块更新
  2. `git add` 之后也可以查看是否有子模块更新，注意查看

- 非当前项目
  - 子模块的维护者提交了更新后，使用了该模块的其他项目维护者，`git pull` 不包含最新的子模块提交信息。如果需要最新代码，需要切换到子模块目录，拉取子模块最新代码之后，项目才会包含最新子模块代码。
  - 步骤如下：
    1. 进入到子模块目录，`git pull`
    2. `git log` 查看提交记录，如果有更新进入步骤 3
    3. 返回主项目根目录，git commit更新主项目git信息

#### 替换/删除子模块

- 如果子模块的项目维护地址发生了变化，或者需要替换子模块，就需要删除原有的子模块
- 删除子模块较复杂，步骤如下：
  1. `rm -rf` 子模块目录   删除子模块目录及源码
  2. 删除项目目录下 .gitmodules 文件中子模块相关条目
  3. 删除 .git/config 文件中子模块相关条目
  4. `git rm --cached` 子模块名称
  5. git commit 更新主项目git信息

## git补丁功能

- 当两个分支的差异改动或者某个分支上的某几个commit需要应用到其他分支上时，可以使用补丁功能

### 不同分支对比差异后打补丁

- 分支dev和master的对比差异改动需要应用到dev-a上

```shell
git checkout dev
#生成patch
git diff master > patch
git checkout dev-a
#打patch
git apply patch
#提交
git commit -m "apply dev patch"
```

- 生成git专用补丁(推荐)

```shell
git checkout dev
#生成0001-dev.patch
git format-patch -M master
git checkout dev-a
#打patch
git am 0001-dev.patch
```

- git diff生成的Patch兼容性强。如果你在修改的代码的官方版本库不是Git管理的版本库，那么你必须使用git diff生成的patch才能让你的代码被项目的维护人接受
- 对于git diff生成的patch，你可以用git apply --check 查看补丁是否能够干净顺利地应用到当前分支中
- 如果git format-patch 生成的补丁不能打到当前分支，git am会给出提示，并协助你完成打补丁工作，你也可以使用git am -3进行三方合并
- 由于git format-patch生成的补丁中含有这个补丁开发者的名字，因此在应用补丁时，这个名字会被记录进版本库

### 根据指定commit打补丁

```shell
#从当前分支最新提交点往下共生成3个补丁
git format-patch -3
#生成指定commit号的补丁
git format-patch -1 [指定commit号]
```

## git工作流

- 集中式工作流
- 功能分支工作流
- gitflow工作流
- forking工作流（一般用在开源项目）
