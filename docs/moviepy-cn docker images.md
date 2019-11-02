## 首先是系统的环境问题。
linux 安装 moviepy需要很多依赖，安装起来费神费力。配置起来也非常麻烦，最简单的办法是直接使用他人构建好的镜像文件。

##  再就是字体显示问题。
镜像中的imagmagick不支持中文的字体。生成的视频中文乱码，搜索了好!

长时间，决定自己手动构建一个镜像。参考的文章链接：

[linux追加中文字库，解决imagemagick 中文乱码的问题](https://www.cnblogs.com/dunkbird/p/5623209.html)

[Linux(Ubuntu，Cent OS)环境安装mkfontscale mkfontdir命令以及中文字库](https://blog.csdn.net/soulmate_P/article/details/8785642)

步骤简单分为几步：

- 拷贝本地Windows下的font（选择你想要的）到镜像中。
- 镜像安装构建字体的依赖
- 构建字体文件夹
- build 镜像

放一下Dockerfile

```
FROM dkarchmervue/moviepy:latest
# 我在同级目录下创建了一个windows_fonts文件夹，里面放着从window下拷贝过来的文件。考本到镜像的字体文件夹下。
COPY ./wondow_fonts/ /usr/share/fonts/windows/
# 更改ubuntu镜像源，dkarchmervue/moviepy是基于ubuntu14.04，找一个镜像源，在本地创建一个sources.list的文件，拷贝到镜像中就可以。
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak
COPY sources.list /etc/apt/sources.list 
RUN apt-get update
# 安装添加字体的依赖
RUN apt-get install ttf-mscorefonts-installer -y && apt-get install fontconfig -y\
     && apt-get install fontconfig \
    && cd /usr/share/fonts/windows/ && chmod 777 * && mkfontscale && mkfontdir && fc-cache
WORKDIR /work/
```
这样就制作了一个基于`dkarchmervue/moviepy` 的带中文字体的镜像。
然后直接运行 `docker build -t moviepy_cnfonts .` 即可创建一个名为 moviepy_cnfonts的镜像。可以把这个镜像替代`dkarchmervue/moviepy:latest`这个镜像作为运行 python文件的基础镜像。

运行moviepy官方的结尾特效 end_effect
![](https://img2018.cnblogs.com/blog/1200357/201909/1200357-20190919202017204-216254823.png)