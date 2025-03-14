# 此仓库不再更新，移步[新仓库](https://github.com/retro98boy/openwrt)

# 仓库作用

给自己拥有的板子编译OpenWRT/iStoreOS，通过对官方源码打补丁来支持不在源码中的板子

目前支持：

TIANNUO TN3399_V3

HRK MB99-V2

# OpenWRT编译步骤

## 准备环境

推荐使用Ubuntu 22.04容器来编译，容器中安装工具：

```
sudo apt install build-essential clang flex bison g++ gawk \
gcc-multilib g++-multilib gettext git libncurses-dev libssl-dev \
python3-distutils rsync unzip zlib1g-dev file wget
```

## 准备源码

在[此处](https://github.com/openwrt/openwrt/releases)下载OpenWRT源码并解压

在本仓库找到同版本的补丁集，全部复制到源码根目录，执行命令打补丁：

```
cd 源码根目录
for patch_file in *.patch; do echo "Applying patch $patch_file"; patch -p1 < "$patch_file"; done
```

## 添加软件包

添加第三方feed源（可选）：

```
sed -i '$a src-git kenzok8_openwrt_packages https://github.com/kenzok8/openwrt-packages' feeds.conf.default
sed -i '$a src-git kenzok8_small https://github.com/kenzok8/small' feeds.conf.default
```

更新feeds：

```
./scripts/feeds update -a
./scripts/feeds install -a
```

添加零散的软件包（可选）：

```
git clone https://github.com/sbwml/openwrt_pkgs.git ~/sbwml_openwrt_pkgs
cp -r ~/sbwml_openwrt_pkgs/luci-app-airplay2 feeds/luci/applications
cp -r ~/sbwml_openwrt_pkgs/luci-app-socat feeds/luci/applications
cp -r ~/sbwml_openwrt_pkgs/luci-app-zerotier feeds/luci/applications

./scripts/feeds update -a
./scripts/feeds install -a
```

## 编译

仓库中有自用的编译配置，将其复制到源码根目录，使用`make menuconfig`进入配置界面后，通过`<Load>`加载配置文件，然后通过`<Save>`保存到`.config`即可

```
# 根据配置提前下载需要的软件源码，如果跳过这步直接进行下一步，会变成一边下载一边编译
make download V=s -j32
# 编译
make V=s -j32
```

# iStoreOS编译步骤

## 准备环境

推荐使用Ubuntu 22.04容器来编译，容器中安装工具：

```
sudo apt install build-essential clang flex bison g++ gawk \
gcc-multilib g++-multilib gettext git libncurses-dev libssl-dev \
python3-distutils rsync unzip zlib1g-dev file wget python2
```

## 准备源码

克隆22.03分支的源码：

```
git clone https://github.com/istoreos/istoreos.git -b istoreos-22.03
```

将本仓库的补丁集全部复制到源码根目录，执行命令打补丁：

```
cd istoreos
for patch_file in *.patch; do echo "Applying patch $patch_file"; patch -p1 < "$patch_file"; done
```

如果打补丁失败，先回退到34c1939f89，再重新打补丁：

```
git reset --hard 34c1939f89 && git clean -fd
```

## 添加软件包

添加第三方feed源（推荐）：

在[这里](https://fw.koolcenter.com/iStoreOS/r4s/)下载feeds.buildinfo，用里面的内容覆盖掉源码根目录的feeds.conf.default

更新feeds：

```
./scripts/feeds update -a
./scripts/feeds install -a
```

## 编译

仓库中有自用的编译配置，将其复制到源码根目录，使用`make menuconfig`进入配置界面后，通过`<Load>`加载配置文件，然后通过`<Save>`保存到`.config`即可

```
# 根据配置提前下载需要的软件源码，如果跳过这步直接进行下一步，会变成一边下载一边编译
make download V=s -j32
# 编译
make V=s -j32
```
