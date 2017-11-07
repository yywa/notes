# Linux上yum镜像源切换

- ## **备份原镜像文件，以免出错后可以恢复**

  ```shell
  mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
  ```

- ## **下载新的CentOS-Base.repo**

  - ### CentOS 5

    ```
    - wget -O /etc/yum.repos.d/CentOS-Base.repohttp:// mirrors.aliyun.com/repo /Centos-5.repo
    ```

  - ### CentOS 6

    ```shell
    - wget -O /etc/yum.repos.d/CentOS-Base.repohttp:// mirrors.aliyun.com/repo /Centos-6.repo
    ```


- ## 运行yum makecache 生成缓存

  ```shell
  yum makecache
  ```


- ## 其他yum源

  - 网易开源镜像站:http://mirrors.163.com/

  - 中科大的Linux安装镜像源：[http://centos.ustc.edu.cn/](http://centos.ustc.edu.cn/)


  - 搜狐的Linux安装镜像源：[http://mirrors.sohu.com/](http://mirrors.sohu.com/)


  - 北京首都在线科技：[http://mirrors.yun-idc.com/](http://mirrors.yun-idc.com/)

    ​