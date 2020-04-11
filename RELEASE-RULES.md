# Reolink Web 开发&部署&发布规范

## 0. 概述

本文描述 Reolink 云平台的 Web 开发、发布、部署过程中的相关规则，包括：

- 版本管理规则
- 代码管理规则
- 版本部署规则

其应用范围包括但不限于如下项目：

- Reolink 官网
- Reolink Cloud Web 客户端
- Reolink 账户中心 Web 客户端
- Reolink 管理后台 Web 版

### 0.1. 术语

- **发布（`Release`）**：指构建版本并交付给下游的过程。
- **部署（`Deploy`）**：指将版本内容上传到运行环境并配置，使之开始运行的过程。

## 1. 版本篇

### 1.1. 版本分类

版本分为两大类：

- 一是按稳定的（月周期）计划执行的版本，称为“主线版本”。
- 二是主线版本开发期间，由于临时、紧急的改动，而无法跟随主线版本计划的额外版本，称为“支线版本”。

### 1.2. 版本命名

版本命名规则很简单，如下格式：

```
version=<date><type>[<index>]
```

其中，

- `<date>` 是版本的发布日期，精确到月，也就是 `YYYYMM` 格式。
- `<type>` 是版本的类型，取值为 `主线版本` 或 `支线版本`。
- `<index>` 是版本的序号，对于主线版本，只有补丁版本需要；而支线版本则必须有序号。

例子：

```
202001主线版本
202001支线版本01
202002主线版本
202002支线版本01
202002支线版本02
```

## 2. Git 篇

### 2.1. 操作规范

#### 2.1.1. 开发团队内，每个成员必须使用自己的分支

在同一个项目内，每个开发人员不得共用分支，每个人都在自己的分支上进行开发。

#### 2.1.2. 每个开发任务/功能必须单独使用一个分支

在一个版本内，每个人可能被分到不止一个开发任务，其中每个任务的重要性不一致。

为了精细化代码-任务的管理粒度，必须每个任务单独使用一个分支，

#### 2.1.3. 合并代码时，使用一个新的分支进行合并

版本开发完毕，进行代码合并时，不能直接将各个任务分支合并到主线分支。而是要从主线分支复制一个新的分支出来，在新的分支里，合并其他分支。最后确定无误了才能合并到主线分支。

例子：

假如主线分支是 `master`，这个版本【201912主线版本】要合并的分支有 `a`, `b`, `c`。

这时候，从 `master` 分支复制一个分支 `mainline-202012` 并切换进去，然后在 `mainline-202012` 分支合并 `a`, `b`, `c`。

#### 2.1.4. 任务依赖分支可以自行合并

假如任务 A 依赖于任务 B，那么可以在 `dev-xxx-a` 分支上直接合并 `dev-yyy-b` 的内容。

**但是注意，作为 `dev-xxx-a` 分支的负责人的 xxx，必须负责合并 `dev-xxx-b`。**

#### 2.1.5. 始终在开发分支上进行开发

在一个版本代码彻底冻结并发布前，所有开发者只能在任务的开发分支上进行修改，不得在版本分支上进行修改。

在开发分支上修改完毕后，再将改动并入版本分支中。

#### 2.1.6. 在版本正式发布上线后删除开发分支

对于已经并入版本分支的开发分支，在（且仅在）版本正式发布上线后，可以删除。

#### 2.1.7. 绝对禁止将版本分支合并到开发分支

开发分支必须保持其纯净，不得随意合并，导致代码污染。

因此，绝对禁止从开发分支合并版本分支。

如果要在版本分支上进行某个功能的后续开发，请从版本分支上复制一个新的开发分支出来，在新的开发分支上进行开发。

#### 2.1.8. 禁止使用 `git rebase`

在现有规模上，使用 `git rebase` 容易导致分支损坏，造成内容丢失。
因此代码合并不再使用 `git rebase` 进行代码合并，统一由代码合并负责人在版本分支上使用 `git merge` 命令进行分支合并。

#### 2.1.9. 严格遵循 CommitLint 的规范进行 git commit 提交

Git 仓库的 commit 使用 Commit Linter 进行规范化管理，所有提交的 commit message 必须符合如下格式：

```
<type>(<scope>): <subject>

[body]
```

其中：

- `<type>` 【必填】是本次提交的操作类型
- `<scope>` 【必填】是本次提交的影响范围
- `<subject>` 【必填】是本次提交的简要说明
- `[body]` 【可选】是本次提交的详细说明

如果 subject 无法准确描述，请换行填写 `[body]`

#### 2.1.10. 禁止快速合并分支，必须手写 commit message

由于快速合并分支容易造成误操作，因此禁止使用。

由于 commit linter 的限制，合并分支的时候必须手写 commit message，格式如下：

```
merge(branch): merged branch '<source>' into '<target>'

[body]
```

其中：

- `<source>` 【必填】被合并的源分支
- `<subject>` 【必填】并入的目标分支
- `[body]` 【可选】是本次提交的详细说明

如果合并过程中遇到了冲突，那莪 `[body]` 为必填项。

#### 2.1.11. 版本构建和版本代码合并必须分由两人进行操作

合并代码时，由版本代码合并者负责，版本构建者在旁协助和审阅。

代码合并完成后，版本构建者在版本分支 review 合并结果，并签发 tag ，进行版本构建。

#### 2.1.12. 合并代码时，代码合并者必须使用 GPG 签名

> 参考：[Git Commit GPG 签名说明](https://help.github.com/en/github/authenticating-to-github/managing-commit-signature-verification)。

平时开发过程中不强求使用 GPG 签名 commit，但是对于代码合并操作者，在这过程中必须使用其自己的 GPG 密钥进行签名。

> 在合并代码前，通过如下命令开启 GPG 签名
>
> ```sh
> git config --global user.signingkey 'XXXXXXX' # 只需设置一次
> git config --global gpg.program 'gpg' # 只需设置一次
> git config --global commit.gpgsign 'true' # 开启 commit 签名
> ```
>
> 并在合并完毕后，可以通过如下命令关闭签名。
>
> ```sh
> git config --global commit.gpgsign 'false' # 关闭 commit 签名
> ```

#### 2.1.13. 构建版本前，由版本负责人负责打 tag 并使用 GPG 签名

> 参考：[Git Tag GPG 签名说明](https://help.github.com/en/github/authenticating-to-github/signing-tags)。

构建版本前，版本构建者应当在 GitLab 上检查该版本分支的合并结果，如有任何合并错误的地方，指出并打回重新合并。此外还必须检查合并操作中 commit 的签名是否有效，**如果其中任何一个合并过程没有合法签名，则代码合并无效。**

在一切检查完毕后，签署版本 tag。

> 在签发 Tag 前，通过如下命令开启 GPG 签名
>
> ```sh
> git config --global user.signingkey 'XXXXXXX' # 只需设置一次
> git config --global gpg.program 'gpg' # 只需设置一次
> git config --global tag.gpgsign 'true' # 开启 tag 签名
> ```
>
> 并在 Tag 签发完毕后，可以通过如下命令关闭签名。
>
> ```sh
> git config --global tag.gpgsign 'false' # 关闭 tag 签名
> ```

### 2.2. 分支命名规范

#### 2.2.1. 开发分支

分支命名规范如下：

```
dev-<developer-name>-<task-name>
```

其中：

- `<developer-name>` 是开发人员的名称，一般是拼音首字母缩写。
- `<task-name>` 是开发任务的英文代号，一般用一个少于 **16** 个字母的代号。

> 如果团队内两个人的拼音简写相同，请两人使用`拼音全称`。如果`拼音全称`也相同，那么请使用各自的`英文名`。

以lgn开发“轻量级链路追踪系统”任务为例：

- `lgn` 的拼音缩写为 `lgn`。
- `轻量级链路追踪系统`翻译为英文可以简写为 `trace-system`

于是这个分支就命名为：`dev-lgn-trace-system`。

#### 2.2.2. 版本分支

版本分两大类，主线版本和支线版本。

主线版本（mainline）是正常节奏的计划版本。

支线版本（subline）属于计划外的版本，一般用于处理高优先紧急任务。

版本分支的命名方式为：

```
v<plan-date>-<type>[-<version-index>]
```

其中

- `<type>` 是指版本类型，即 `mainline` 和 `subline`。
- `<plan-date>` 是指版本计划发布的日期，格式为 `YYYYMM`，如 `202002主线版本` 中，`<plan-date>` 就是 `202002`。
- `<version-index>` 是可选的版本号，仅对 `subline` 使用，如【202002支线版本01】的`<version-index>` 就是 `1`

举例：

```
v202002-mainline
v202002-subline-1
v202002-subline-2
v202002-subline-3
```

### 2.3. Tag 命名规范

#### 2.3.1. 测试版本

当一个版本需要提交测试时，需要在版本分支里打 tag，命名规则如下：

```
<branch-name>-<operator>-<full-date>
```

其中

- `<branch-name>` 是版本分支的名称
- `<operator>` 是打 tag 的人的名称，一般是拼音首字母缩写。
- `<full-date>` 是打 tag 时的日期，格式为 `YYYYMMDD`。

> 如果团队内两个人的拼音简写相同，请两人使用`拼音全称`。如果`拼音全称`也相同，那么请使用各自的`英文名`。

例如，`lgn`在 2020-03-04 发布的测试版本【202002主线分支】，命名为：

```
v202002-mainline-lgn-20200304
```

#### 2.3.2. 正式版本

正式版本是指一个通过了测试的版本，所以直接给现有的版本 tag 取个带后缀 `-production` 的 tag 别名即可。即：

```
<test-version-tag>-production
```

如：（20200304转测试，20200317测完发布上线）

```sh
git checkout v202002-mainline-lgn-20200304
git tag v202002-mainline-lgn-20200317-production
```

#### 2.3.3. 补丁版本

在一个版本发布后，如果有补丁版本，则在后面加上补丁版本序号即可：

```
<branch-name>-patch-<patch-index>-<operator>-<full-date>[-production]
```

例如：

```
v202002-mainline-patch-1-lgn-20200307
v202002-mainline-patch-1-lgn-20200307-production
v202002-subline-1-patch-1-lgn-20200305
v202002-subline-1-patch-1-lgn-20200305-production
```

## 3. Docker 篇

### 3.1. 操作规范

#### 3.1.1. 同时发布版本 Tag 和环境 Tag

构建一个镜像时，同时给它两个 Tag，一个是版本 Tag，一个是环境 Tag。

以 Git Tag `v202002-mainline-lgn-20200304` 为例，构建测试版本时：

```sh
REGISTRY_URL=xxxxx.com/reolink/project-name
BUILD_ENV=sandbox
GIT_TAG=v202002-mainline-lgn-20200304

ENV_TAG=$REGISTRY_URL:$BUILD_ENV

VERSION_TAG=$ENV_TAG-$GIT_TAG

docker build -t $VERSION_TAG .

docker tag $VERSION_TAG $ENV_TAG

docker push $VERSION_TAG
docker push $ENV_TAG
```

这样可以令环境 Tag 始终指向最新的版本镜像。从而实现在部署最新版本时，通常只需要使用环境 Tag 即可，而不需要知道具体版本。

#### 3.1.2. Web 项目的镜像只提供文件内容

对于 Web 项目，构建的镜像，只负责提供文件的内容，统一把项目内容放在镜像的 `/data/` 目录下，如：

```dockerfile
FROM alpine:latest

VOLUME [ "/data" ]

COPY dist /data
```

#### 3.1.3. 使用同一个 Bridge 网桥

对于功能服务，部署容器时，在同一台主机上必须使用相同的 Bridge 网桥，否则无法相互访问。

### 3.2. Tag 命名规范

#### 3.2.1. 版本 Tag

版本 Tag 格式为：

```
<env>-<git-tag>
```

其中：

- `<env>` 为要部署的环境，为 `production` 或 `sandbox`
- `<git-tag>` 即该版本在 Git 仓库中对应的 Tag。

如：

```
sandbox-v202002-mainline-lgn-20200304
production-v202002-mainline-lgn-20200304
```

> 线上版本的镜像 Tag 名称可以省略后缀 `-production`。
> 而不必写作 `production-v202002-mainline-lgn-20200304-production`。

#### 3.2.2. 环境 Tag

环境 tag 是指给环境部署用的 tag，目前只有如下两个：

- `sandbox`
- `production`

### 3.3. Docker 仓库命名规范

#### 3.3.1. 前端代码仓库

命名格式：

```
web-<name>
```

即使用 `web-` 前缀。

#### 3.3.2. 微服务仓库

命名格式：

```
services-<name>
```

即使用 `services-` 前缀。

#### 3.3.3. 公共基础镜像仓库

命名格式：

```
env-<name>
```

即使用 `env-` 前缀。

## 4. 发布篇

### 4.1. 交付内容

发布意味着一个版本已经构建完毕，此时必须交付如下内容：

#### 4.1.1. 源代码

提供整个版本相关所有项目的版本源代码快照的打包合集。（仅当前 commit）

每个项目单独一个目录，使用 `tar` 命令打包成一个 `tgz` 格式的文件。

#### 4.1.2. Docker 镜像

整个版本所有项目应当构建对应版本的镜像，推送到镜像仓库。

除此之外，还应导出并生成镜像文件，作为发布的一部分。

#### 4.1.3. 部署文档

编写详细的、可读的部署文档，要求任何一个后端开发人员拿到这份文档，都应当能按照上面的指示进行正确的部署。

#### 4.1.4. 修改日志

版本的详细修改日志。

版本发布时，各个开发人员提供各自的修改日志，由版本构建者进行汇总。

### 4.2. 交付流程

#### 4.2.1. 测试版本

##### 4.2.1.1. 代码整合

在相关项目的 Git 仓库中建立版本分支，合并各个开发分支的代码。并打上版本 Tag。

##### 4.2.1.2. 构建镜像

将相关项目分别构建测试版本的 Docker 镜像，推送到镜像仓库服务器上。

并且将镜像导出生成镜像文件。

##### 4.2.1.3. 部署测试环境

使用 Docker-Compose 将版本镜像部署到测试环境。

##### 4.2.1.4. 提交测试

在禅道上建立测试版本信息，并提交给测试组进行测试。

#### 4.2.2. 生产版本

##### 4.2.1.1. 构建镜像

从最后一个稳定的测试版本代码构建生产版本的 Docker 镜像，推送到镜像仓库服务器上。

并且将镜像导出生成镜像文件。

##### 4.2.1.2. 部署生产环境

使用 Docker-Compose 将版本镜像部署到生产环境。

部署的时候注意两台服务器轮流部署，实现无缝切换。
