

# 环境

`ros noetic`

`ubuntu 20.04`

`全局梯子`

# 安装

``` bash
# 安装依赖
sudo apt-get update
sudo apt-get install -y python3-wstool python3-rosdep ninja-build stow

# wstool操作
mkdir catkin_ws
cd catkin_ws
wstool init src
wstool merge -t src https://raw.githubusercontent.com/cartographer-project/cartographer_ros/master/cartographer_ros.rosinstall
wstool update -t src

# rosdep操作
sudo rosdep init
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y

# 编译abseil
src/cartographer/scripts/install_abseil.sh

# 编译cartographer
catkin_make_isolated --install --use-ninja
```

# 遇到的问题

## `rosdep`初始化错误

``` bash
ERROR: cannot download default sources list from:
https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list
Website may be down.
```

解决:

``` bash
sudo mkdir -p /etc/ros/rosdep/sources.list.d
cd /etc/ros/rosdep/sources.list.d
# 将https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list中的内容复制到`/etc/ros/rosdep/sources.list.d`中即可
```

[(ㄏ￣▽￣)ㄏ 回来啦~ (cnblogs.com)](https://www.cnblogs.com/xiaoaug/p/17790507.html)

## `libabsl-dev`错误

``` bash
ERROR: the following packages/stacks could not have their rosdep keys resolved
to system dependencies:
cartographer: [libabsl-dev] defined as "not available" for OS version [noetic]
```

解决:

删除`cartographer `包下`package.xml`中的`libabsl-dev`一行即可

[解决“cartographer: [libabsl-dev\] defined as “not available“ for OS version [bionic]”问题_cartographer: [libabsl-dev] defined as "not availa-CSDN博客](https://blog.csdn.net/weixin_64889621/article/details/127145227?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-127145227-blog-131330983.235^v43^pc_blog_bottom_relevance_base6&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

# 参考

[Compiling Cartographer ROS — Cartographer ROS documentation (google-cartographer-ros.readthedocs.io)](https://google-cartographer-ros.readthedocs.io/en/latest/compilation.html#building-installation)