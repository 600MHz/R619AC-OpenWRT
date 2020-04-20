
# P2W R619AC Patch for Offical Openwrt
# 针对OpenWrt 官方稳定版的竞斗云补丁

This patch is generated from this p[ull request](https://github.com/openwrt/openwrt/pull/2604), but adapted to tag v19.07.2 of [openwrt official repository](https://github.com/openwrt/openwrt). You can use this patch to build your own clear image for your P&W R619AC from officical code.

这个补丁是为`竞斗云`可以方便的编译一个[OpenWrt官方](https://github.com/openwrt/openwrt)稳定版（目前是19.07.2），可以使用官方的opkg源而不会出现版本依赖问题而创建的。代码来自于这个[pull request](https://github.com/openwrt/openwrt/pull/2604) 。你可以使用这个patch 文件为官方代码打上补丁，自己编译不集成任何第三方插件的原生OpenWrt。

## Compiled binary files (LuCI included)

## 编译好的二进制文件（默认设置，仅增加web界面）

You can download the pre-compiled binary files (128M version only) from `bin` folder, use it at your own risk.

在`bin`文件夹下面有我编译好的image（仅含128M版本），对比官方默认配置仅增加了LuCI以方便使用。可以说是非常纯净了。请使用正确的image文件刷机，风险自担。

## Steps

## 食用步骤

This is not for newbees because of lack of explainations.

本步骤不作任何解释说明，不推荐新手使用。


0. Set up compiling environment. Official docker image is recommanded

    创建编译环境，推荐使用官方docker image，方便快捷

    ```
    docker run --rm -v "$(pwd)"/bin/:/home/build/openwrt/bin -it openwrtorg/imagebuilder:ipq40xx-generic-19.07.2
    ```

1.  Clone official repository of Openwrt and switch to the right tag.

    在docker环境下克隆OpenWrt官方仓库，并切换到v19.07.2

    ```
    cd ~
    git clone https://github.com/openwrt/openwrt.git source
    cd source
    git checkout v19.07.2
    ```

2.  Update feeds according to official guide

    按照官方步骤update feeds

    ```
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    ```


3.  Apply this patch

    下载补丁并打上补丁

    ```
    wget https://raw.githubusercontent.com/600MHz/R619AC-OpenWrt/master/r619ac-v19.07.2.patch
    git apply r619ac-v19.07.2.patch
    ```

4.  Configurate before compiling

    配置编译选项

    ```
    make menuconfig
    ```

    在这个“图形界面”里做如下设置：
    * `Target System` -> `Qualcomm Atheros IPQ40XX`  ## 必须
    * `Target Profile` -> `P&W R619AC (128M)`  ## 必须。如果你要编译64M的版本也在这里设置
    * `LuCI` -> `Collections` -> `luci` ## 可选，如果你希望把web界面也编译进去
    其它选项根据自己喜好来设置。退出时保存设置。

5.  Compile

    开始编译

    ```
    make
    ```
    
6.  Finish

    收货

    You may want to copy bin files compiled out to `~/openwrt/bin`, as current path is `~/source` where we did at step 1.

    因为在第一步里把官方源码克隆到了`~/source`目录，而`docker`挂载的是`~/openwrt/bin`目录，所以你需要把编译后的文件复制过去
    ``` 
    cp -r ~/source/bin ~/openwrt/
    ``` 

    Or, you can use `docker cp` to copy the files out of docker container.

    或者使用`docker cp`命令把文件复制出来，这里不详述


## 题外话 (中文福利)
   * 注意128M的版本需要`OpBoot` 版本 >= `1.0.9`
    
   * 可以把第2步的命令中的`--rm`选项去掉，这样退出`shell`之后`docker container`仍然保留，你可以使用`docker start -ai <container_id>`再回去，不会丢失你做的一切改变。

