# 仓库作用

给自己拥有的板子编译官方OpenWRT，通过对官方源码打补丁来支持不在源码中的板子

目前支持：

TIANNUO TN3399_V3

HRK MB99-V2

# 编译步骤

## 准备环境

使用Ubuntu 20.04容器来编译，容器中安装工具：

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
git clone https://github.com/coolsnowwolf/luci.git ~/coolsnowwolf_luci
cp -r ~/coolsnowwolf_luci/applications/luci-app-airplay2 feeds/luci/applications
cp -r ~/coolsnowwolf_luci/applications/luci-app-socat feeds/luci/applications

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
