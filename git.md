### 一. 版本控制
    可以帮助程序员进行代码的追踪、维护、控制等一系列操作

### 二. 版本控制的功能：
    1. 不同版本的存储管理；
    2. 重大版本的备份维护；
    3. 恢复之前的项目版本；
    4. 记录项目的更改；
    5. 多人开发的代码合并；
    
### 三. 集中式版本控制和分布式版本控制：
    1. 集中式版本控制SVN、CVS
        1. 使用单一的、集中管理的服务器，保存所有文件的修订版本；
        2. 协同开发人员通过客户端连接到服务器，取出最新的文件或者提交更新
        3  缺点：
            中央服务器不能出现故障。
                1.如果服务器宕机了，宕机期间就不能更新提交代码，程序员就无法协同开发了；
                2.如果中心数据库所在磁盘发生损坏，有没有做备份，会丢失所有数据，例如所有的提交记录都没有了，就没法
                   恢复到旧的版本，程序员本地的代码可能是最新的，但是没有对应的提交记录。
    2. 分布式版本控制git
        1. 客户端并不只是提取最新版本的文件快照，而是把代码仓库完整地克隆下来，包括完整的历史记录；
        2. 即使服务器发生故障，都可以用任何一个镜像出来的本地仓库恢复；

### 四. git Bash-CMD-GUI的区别：
    1. git bash就是一个shell，是windows下的命令行工具，可以执行linux命令，推荐使用git bash；
       git bash是基于cmd的，在cmd的基础上增加一些新的命令和功能；
    2. 在安装git时，会安装git cmd，使得windows的cmd也可以运行git 命令，使用git cmd也会有一个cmd外形的命令行；
    3. git GUI，可以使用图形用户界面来运行git命令；

### 五. git的配置：
    git config --global user.name 'easonsfan'
    git config --global user.email '1275422527@qq.com'
    git config -l：查看设置列表

### 六. 初始化git仓库：
    1. 使用 git init 在本地初始化一个仓库
    2. 使用 git clone 从远程仓库克隆一个仓库，推荐，可以免除复杂的设置  

### 七. git文件状态划分：
    1. 未跟踪：新增加的文件未被git仓库管理，为未跟踪状态untracked;   
    2. 已跟踪：添加到git仓库管理的文件为已跟踪状态；  
        1. staged：暂存区状态。未跟踪状态或者modified状态的文件，通过 git add xxx 或者 git add . 命令，把文件添加到暂存区，状态也对应改为staged；   
        2. unmodified：  未修改状态。在暂存区的文件被提交commit到仓库时，文件状态会改为unmodified状态；   
        3. modified：修改状态。已经commit的文件，被修改后会变成modified状态。   

    一般的文件状态改变如下：  
        4. 新文件状态为untracked；   
        5. 使用 git add xxx 把新文件添加到暂存区，此时文件状态变为statged；   
        6. 使用 git commit -m '备注' 把暂存区的文件都提交的本地仓库，此时文件变为unmodified状态；  
        7. 如果此时所有文件都为unmodified状态，修改其中一个文件，此文件会变为modified状态，这时重复第2、3步就可以了  
    命令：  
        git add . ： 所有的文件添加到暂存区；  
        git add <file>：具体的文件添加到暂存区；  
        git commit -m  '备注'：提交暂存区的文件到本地仓库，备注为必填且不能为空；  
        git commit -a -m '备注'：被修改文件或者新增文件直接提交到本地仓库；  
        git restore <file>：把修改过的文件还原，同样可以用 . 还原所有modified状态文件，进入了暂存区的不能还原；  
        git restore --staged <file>：把暂存区的文件还原回去，同样可以用 . 还原所有暂存区的文件；   
        git status：获取当前未提交的文件状态；  
        git log：获取提交日志  

### 八. git忽略文件：
    通过.gitignore文件配置不需要版本控制的文件

### 九. 版本回退
    1. 校验和：也就是commit id，记录着每一次提交的id，可以使用id回退到某一次提交；
    2. git log可以查看提交日志，git log --pretty=online可以只查看commit id和备注；
    3. git reflog 查看每一次提交日志和回退日志；
    回退指令：
        1. git reset --hard HEAD^：回退到上一个提交，1个^表示回退1步，2个^就是2步；
        2. git reset --hard HEAD~100：表示回退100步，~数字 可以设置回退多少步；
        3. git reset --hard id：回退到指定提交id的位置；

### 十. git服务器和远程仓库：
    每个git服务器存放多个仓库，这些仓库就是远程仓库，而git服务器一般是放在云服务器上，例如腾讯云、阿里云等
    git服务器有3种：
        github：开源项目可以放在这里
        gitee：国内使用方便
        gitlab：国内使用不方便，一般不用这个
    也可以自己搭建git服务器，大多数公司是使用gitlab软件搭建git服务器。

### 十一. 远程仓库的创建和验证：
    1. 创建：
        在github或者gitee上创建一个仓库
    2. 验证：
        在创建了仓库后，在操作远程仓库时需要验证，验证方式一般用以下2个：
            1.https：通过输入账号密码作为凭证；
                使用这种方式的话，git会有凭证系统来处理，下面的保存凭证的几种方式，在安装git时会有选择，默认选了第4个：
                    1. 不使用缓存，每次操作远程仓库都会提示输入账号密码；
                    2. cache模式，存放在内存中，但是15分钟后会清除凭证；
                    3. store模式，把凭证以明文的方式存放在磁盘中，永远不会过期；
                    4. mac电脑 osxkeychain 模式，把凭证缓存到系统用户的钥匙串中；
                    5. windows系统使用 git credential manager for windows 辅助工具保存凭证，安装git时同时安装了，也默认使用这个工具；
            2.基于ssh的密钥；
                ssh：secure shell(安全外壳协议，简称ssh)是一种加密的网络传输协议，以非对称加密实现身份验证。
                手动生成一对公钥和私钥，然后把公钥存放在git服务器中，私钥放在电脑本地，这样就实现了身份验证，可以使用以下命令生成公钥和秘钥：
                ssh-keygen -t ed25519 -C '邮箱'        -t后面表示加密算法的类型
                公钥和私钥一般存放在C:/Users/huangxiansheng/.ssh文件夹当中，id_ed25519文件为私钥，id_ed25519.pub为公钥

### 十二. 管理远程服务器
    1. 查看远程仓库
        git remote：返回远程仓库的别名，默认是origin
        git remote -v：返回远程仓库详细信息，别名origin和远程仓库地址
    2. 把本地仓库(使用 git init 本地创建的仓库，而不是clone下来的仓库)与远程仓库建立连接
        git remote add <shortname> <url>    shortname:远程仓库在本地的别名，一般仓库地址都是很长的域名地址，所以取个短的  url:仓库地址
        但是想git pull和git push，还需要解决2个问题：
        1.因为不知道是要和远程仓库的哪个分支合并，需要通过设置--set-upstream origin <branchname> 来指定分支；
        2.合并分支时，2个分支有不相关的历史记录，需要在git merge时设置--allow-unrelated-histories来合并
    3. 管理远程仓库
      git remote rename originalName newName：重命名远程仓库
      git remote remove <shortname>：删除本地仓库与远程仓库的连接

### 十三. git标签(tag)
    tag的作用是区分每个代码版本，如果单纯用commitid来区分的话，不能确定哪个commitid比较重要，到时回退也不知道找哪个commitid
    1. 本地仓库创建tag：
        一般在本地commit之后，通过给本地的commit加上tag，而且一个commit可以重复创建tag，所以注意每个commit创建一个tag就可以了
        1. 轻量标签：直接通过命令 git tag <tagname> 创建tag
        2. 附注标签：给tag添加备注，git tag -a <tagname> -m '备注'
    2. 把本地tag添加到远程仓库：
        添加单个：git push <shortname> <tagname>
        添加全部：git push <shortname> --tags
    3. 删除本地和远程tag：
       删除本地：git tag -d <tagname>
       删除远程：git push <shortname> -d <tagname>
    4. 切换到某个tag(检出tag)：
        git checkout <tagname>
        检出到某个tag时，不会删除这个tag之后的其他tag，也不能在这个tag里修改代码，必须新建一个分支才能修改。如果硬要修改并且commit，
        切换回分支后修改记录会被删除，相当于没效果

### 十四. git原理
    1. 每个git提交的记录都是一个commit 对象，对象里记录着提交文件信息、提交作者、提交时间以及前一次的提交对象；
    2. tag、master分支及其他分支都是指向这些提交对象的，而head指针又指向master分支(master和其他分支是一样的普通的分支，只不过master是默认存在的分支)；
    3. master文件始终指向当前master分支的最后一次提交对象，其他分支也是一样；
    4. 每增加一个commit对象，head指针和master文件都会同时往前移动；
    5. 创建一个分支后，当前的commit对象就像一棵树的树枝一样，多了一个分支文件，新分支文件还是指向当前的commit对象，以后的提交和其他分支互不相干；
    6. 切换分支时，head指针会指向对应的分支文件，显示对应的提交信息；

### 十五. 本地分支管理
    1. 创建分支：git branch <branchname>
       切换到分支：git checkout <branchname>
       创建分支并且切换到分支：git checkout -b <branchname>
    2. 查看分支：git branch
       查看分支同时查看最后一次提交：git branch -v
       查看所有合并到当前分支的分支：git branch --merged
       查看所有没有合并到当前分支的分支：git branch --no-merged
    3. 删除当前分支：git branch -d <branchname>
       强制删除某一个分支：git branch -D <branchname>
    分支的使用：
        当某个tag有bug时，从master分支切换到有bug的tag，这时需要创建一个hotfix分支来修复bug，这个hotfix分支的使用就跟平时开发一样就行，
        当bug改完了，为了跟master分支同步，需要切换回master分支，在master分支执行git merge hotfix命令合并hotfix分支，这时修改过的文件会有
        冲突，把文件修改到你想要的就行，最后提交修改，一般改完bug后这个hotfix分支删除就行。   

### 十六. 远程分支管理：
    1. 推送本地分支到远程仓库：
        git push origin <branchname> // 远程仓库不能有这个分支
        推送完之后，远程分支和本地分支没有建立关系，需要使用git push --set-upstream origin <branchname>把远程分支设置为上游分支以后才可以使用git push简写。
    2. 克隆下来的仓库，只有默认分支master，要想和远程的其他分支建立连接，使用以下命令：
        git checkout --track origin/<branchname> // 远程仓库要有这个分支
            这个命令做了以下3步：
            1.创建一个本地分支； 
            2.切换到这个本地分支；
            3.让本地分支跟踪远程相同名字的分支
        这个是上面命令的简写：
        git checkout <branchname>  // 远程仓库要有这个分支
        *************远程仓库新增了分支，本地想要通过这些命令跟踪最新分支，需要git pull一下获取最新分支信息**************
    3. 删除远程分支：
        git push origin -d/--delete <branchname>

### 十七. 从远程仓库拉取更新代码：
    1. git fetch + git merge:
        git fetch先把最新代码拉取到本地暂存区，git merge把最新代码和本地代码合并，有冲突时需要解决冲突
    2. git pull
       是git fetch + git merge的简写，会直接合并代码
    要在对应分支拉取代码才有用
    
   