# SVN

- 此处主要介绍[tortoisesvn](https://tortoisesvn.net)

## vscode中关于svn的插件

- SVN：会在左侧菜单栏出现一个SVN项（打开SVN项目时），其中包括**repositories**和**file history**两个tab
  - repositories: 包含远端仓库里所有的commit信息
  - file history：包含当前选中的文件或者文件夹的commit信息
  - 右键文件选项会多出一些svn操作选项
- TortoiseSVN: 当使用**TortoiseSVN**时在vsocde界面提供一些便利选项操作

## 创建SVN项目

- 右键空白地方，选项栏会多出TortoiseSVN选项，它的子菜单有所有的可操作性选项
- 在一个空文件夹内：右键 -> TortoiseSVN -> create repositories here, 可新建一个svn本地仓库
- 在本地仓库以外的地方：右键 -> SVN Checkout -> 填写仓库地址（本地或远端）和目标地址 -> 创建成功

## SVN代码管理

- 部分概念详[SVN使用](https://www.cnblogs.com/zhoumiao/p/5459552.html)
- 可在vscode使用TortoiseSVN插件或直接在文件夹中右击文件选择操作
- 可单独对文件或文件夹进行操作

## SVN分支管理

- 案例参考[分支合并](https://jingyan.baidu.com/article/6c67b1d66522652787bb1ef6.html)
- 和git不同的是SVN的分支都是实际的文件夹
