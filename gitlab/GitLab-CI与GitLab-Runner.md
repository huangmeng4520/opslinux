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
 
 找到token之后，运行下面这条命令注册Runner（当然，除了url和token之外，还需要其他的信息，比如执行器executor、构建目录builds_dir等）。
gitlab-ci-multi-runner register
注册完成之后，GitLab-CI就会多出一条Runner记录，如下图所示：

 ![Shared Runner](https://github.com/Lancger/opslinux/blob/master/images/GitLab-CI-Runner.png)

GitLab-CI会为这个Runner生成一个唯一的token，以后Runner就通过这个token与GitLab-CI进行通信。

那么，问题来了。注册好了的Runner的信息存放在哪儿了呢？
原来，Runner的信息是存放在一个配置文件里面的，配置文件的格式一般是.toml。这个配置文件的存放位置有以下几种情况：

    在类Unix操作系统下（0.5.0之后版本）
        如果是以root用户身份运行gitlab-ci-multi-runner register，那么配置文件默认是/etc/gitlab-runner/config.toml
        如果是以非root用户身份运行gitlab-ci-multi-runner register，那么配置文件默认是~/.gitlab-runner/config.toml
    在其他操作系统下以及0.5.0之前版本
    配置文件默认在当前工作目录下./config.toml

一般情况下，使用默认的配置文件存放Runner的配置信息就可以了。当然，如果你有更细化的分类需求，你也可以在注册的时候通过-c或--config选项指定配置文件的位置。具体查看register命令的使用方法：gitlab-ci-multi-runner register --help。

问题：如果不运行gitlab-ci-multi-runner register命令，直接在配置文件里面添加Runner的配置信息可以吗？
回答：当然不可以。因为gitlab-ci-multi-runner register的作用除了把Runner的信息保存到配置文件以外，还有一个很重要的作用，那就是向GitLab-CI发出请求，在GitLab-CI中登记这个Runner的信息并且获取后续通信所需要的token。


## 让注册好的Runner运行起来

Runner注册完成之后还不行，还必须让它运行起来，否则它无法接收到GitLab-CI的通知并且执行软件集成脚本。怎么让Runner运行起来呢？gitlab-ci-multi-runner提供了这样一条命令gitlab-ci-multi-runner run-single，详情如下：

```
[root@iZ25bjcxoq5Z ~]# gitlab-ci-multi-runner run-single --help
NAME:
   run-single - start single runner

USAGE:
   command run-single [command options] [arguments...]

OPTIONS:
   --name, --description   Runner name [$RUNNER_NAME]
   --limit     Maximum number of builds processed by this runner [$RUNNER_LIMIT]
   --ouput-limit    Maximum build trace size [$RUNNER_OUTPUT_LIMIT]
   -u, --url     Runner URL [$CI_SERVER_URL]
   -t, --token     Runner token [$CI_SERVER_TOKEN]
   --tls-ca-file    File containing the certificates to verify the peer when using HTTPS [$CI_SERVER_TLS_CA_FILE]
   --executor     Select executor, eg. shell, docker, etc. [$RUNNER_EXECUTOR]
   --builds-dir    Directory where builds are stored [$RUNNER_BUILDS_DIR]
   --cache-dir     Directory where build cache is stored [$RUNNER_CACHE_DIR]
   --env     Custom environment variables injected to build environment [$RUNNER_ENV]
   --shell     Select bash, cmd or powershell [$RUNNER_SHELL]
   --ssh-user     User name [$SSH_USER]
   --ssh-password    User password [$SSH_PASSWORD]
   --ssh-host     Remote host [$SSH_HOST]
   --ssh-port     Remote host port [$SSH_PORT]
   --ssh-identity-file    Identity file to be used [$SSH_IDENTITY_FILE]
   --docker-host    Docker daemon address [$DOCKER_HOST]
   --docker-cert-path    Certificate path [$DOCKER_CERT_PATH]
   --docker-tlsverify    Use TLS and verify the remote [$DOCKER_TLS_VERIFY]
   --docker-hostname    Custom container hostname [$DOCKER_HOSTNAME]
   --docker-image    Docker image to be used [$DOCKER_IMAGE]
   --docker-privileged   Give extended privileges to container [$DOCKER_PRIVILEGED]
   --docker-disable-cache   Disable all container caching [$DOCKER_DISABLE_CACHE]
   --docker-volumes    Bind mount a volumes [$DOCKER_VOLUMES]
   --docker-cache-dir    Directory where to store caches [$DOCKER_CACHE_DIR]
   --docker-extra-hosts   Add a custom host-to-IP mapping [$DOCKER_EXTRA_HOSTS]
   --docker-links    Add link to another container [$DOCKER_LINKS]
   --docker-services    Add service that is started with container [$DOCKER_SERVICES]
   --docker-wait-for-services-timeout  How long to wait for service startup [$DOCKER_WAIT_FOR_SERVICES_TIMEOUT]
   --docker-allowed-images   Whitelist allowed images [$DOCKER_ALLOWED_IMAGES]
   --docker-allowed-services   Whitelist allowed services [$DOCKER_ALLOWED_SERVICES]
   --docker-image-ttl     [$DOCKER_IMAGE_TTL]
   --parallels-base-name   VM name to be used [$PARALLELS_BASE_NAME]
   --parallels-template-name   VM template to be created [$PARALLELS_TEMPLATE_NAME]
   --parallels-disable-snapshots  Disable snapshoting to speedup VM creation [$PARALLELS_DISABLE_SNAPSHOTS]
   --virtualbox-base-name   VM name to be used [$VIRTUALBOX_BASE_NAME]
   --virtualbox-disable-snapshots  Disable snapshoting to speedup VM creation [$VIRTUALBOX_DISABLE_SNAPSHOTS]
```


参考资料：  https://www.cnblogs.com/cnundefined/p/7095368.html
