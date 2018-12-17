## 一、持续集成（Continuous Integration）

要了解GitLab-CI与GitLab Runner，我们得先了解持续集成是什么。

       持续集成是一种软件开发实践，即团队开发成员经常集成他们的工作，通常每个成员每天至少集成一次，也就意味着每天可能会发生多次集成。
    每次集成都通过自动化的构建（包括编译，发布，自动化测试)来验证，从而尽快地发现集成错误。许多团队发现这个过程可以大大减少集成的问题，
    让团队能够更快的   开发内聚的软件。
    
看完这段话，估计还是有点懵。怎么理解呢？我是这样理解的：

    软件集成是软件开发过程中的一个环节，这个环节的工作一般会包括以下流程：合并代码---->安装依赖---->编译---->测试---->发布。软件集成的工作一般会比较细碎繁琐，为了不影响开发效率，以前软件集成这个环节一般不会经常进行或者只会等到项目后期再进行。但是有些问题，如果等到后期才发现，解决问题的代价很大，有可能导致项目延期或者失败。因此，为了尽早发现软件集成错误，鼓励团队成员应该经常集成他们的工作，通常每个成员每天应该至少集成一次。这就是所说的持续集成。所以说，持续集成是一种软件开发实践。

    软件集成的工作细碎繁琐，以前是由人工完成的。但是现在鼓励持续集成，那岂不是要累死人，还影响开发效率。所以，应该考虑将软件集成这个工作自动化，这就出现了所谓的持续集成系统。

    持续集成详情见百度百科-持续集成


## 二、GitLab-CI
    GitLab-CI就是一套配合GitLab使用的持续集成系统（当然，还有其它的持续集成系统，同样可以配合GitLab使用，比如Jenkins）。而且GitLab8.0以后的版本是默认集成了GitLab-CI并且默认启用的。

## 三、GitLab-Runner
    那GitLab-Runner又是什么东东呢？与GitLab-CI有什么关系呢？

    GitLab-Runner是配合GitLab-CI进行使用的。一般地，GitLab里面的每一个工程都会定义一个属于这个工程的软件集成脚本，用来自动化地完成一些软件集成工作。当这个工程的仓库代码发生变动时，比如有人push了代码，GitLab就会将这个变动通知GitLab-CI。这时GitLab-CI会找出与这个工程相关联的Runner，并通知这些Runner把代码更新到本地并执行预定义好的执行脚本。

    所以，GitLab-Runner就是一个用来执行软件集成脚本的东西。你可以想象一下：Runner就像一个个的工人，而GitLab-CI就是这些工人的一个管理中心，所有工人都要在GitLab-CI里面登记注册，并且表明自己是为哪个工程服务的。当相应的工程发生变化时，GitLab-CI就会通知相应的工人执行软件集成脚本。

如下图所示： 

 ![gitrunner架构图](https://github.com/Lancger/opslinux/blob/master/images/git.png)

GitLab-CI与GitLab-Runner关系示意图

Runner可以分布在不同的主机上，同一个主机上也可以有多个Runner。

### Runner类型

    GitLab-Runner可以分类两种类型：Shared Runner（共享型）和Specific Runner（指定型）。

     Shared Runner：这种Runner（工人）是所有工程都能够用的。只有系统管理员能够创建Shared Runner。

     Specific Runner：这种Runner（工人）只能为指定的工程服务。拥有该工程访问权限的人都能够为该工程创建Shared Runner。


## 四、GitLab-Runner的安装与使用
我的操作系统是：Centos 7.0 64位
安装gitlab-ci-multi-runner

    添加yum源

    curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.rpm.sh | sudo bash

    安装

    yum install gitlab-ci-multi-runner

    这里是官网的安装教程，其它操作系统的请参考
    https://gitlab.com/gitlab-org/gitlab-ci-multi-runner

使用gitlab-ci-multi-runner注册Runner

安装好gitlab-ci-multi-runner这个软件之后，我们就可以用它向GitLab-CI注册Runner了。

向GitLab-CI注册一个Runner需要两样东西：GitLab-CI的url和注册token。
其中，token是为了确定你这个Runner是所有工程都能够使用的Shared Runner还是具体某一个工程才能使用的Specific Runner。

如果要注册Shared Runner，你需要到管理界面的Runners页面里面去找注册token。如下图所示：

 ![Shared Runner](https://github.com/Lancger/opslinux/blob/master/images/Shared%20Runner.png)

如果要注册Specific Runner，你需要到项目的设置的Runner页面里面去找注册token。如下图所示：

 ![Specific Runner](https://github.com/Lancger/opslinux/blob/master/images/Specific%20Runner.png)

参考资料：  https://www.cnblogs.com/cnundefined/p/7095368.html
