
## 遇到问题的解决方案参考

(Anaconda 查看、添加、删除 安装源)[https://blog.csdn.net/David_jiahuan/article/details/104544957]

(Conda 替换镜像源方法尽头，再也不用到处搜镜像源地址)[https://blog.csdn.net/adreammaker/article/details/123396951]

(解决：Collecting package metadata (current_repodata.json)/ Solving environment)[https://blog.csdn.net/ermmtt/article/details/132628639]

(【Python】conda镜像配置，.condarc文件详解，channel镜像)[https://blog.csdn.net/Code_LT/article/details/134928013]

(安装CUDA以及GPU版本的pytorch)[https://blog.csdn.net/qq_66608435/article/details/141606279]

(【CUDA】【PyTorch】安装 PyTorch 与 CUDA 11.7 的详细步骤)[https://blog.csdn.net/Stromboli/article/details/142705892]

## 源

豆瓣(douban) http://pypi.douban.com/simple/

## 显示已配置的源
conda config --show-sources

## 一、管理conda：

（1）检查conda版本

conda --version

（2）获取版本号

conda --version或 conda -V

（3）列出所有的环境

conda env list

conda list命令用于查看conda下的包，而conda env list命令可以用来查看conda创建的所有虚拟环境。

（4）查看环境管理的全部命令帮助

conda env -h

（5）conda升级

在命令行中或者anaconda prompt中执行命令进行操作。

conda update conda升级conda

conda update anaconda升级anaconda前要先升级conda

conda update --all升级所有包

（6）conda升级后释放空间

在升级完成之后，我们可以使用命令来清理一些无用的包以释放一些空间：

conda clean -p删除没有用的包

conda clean -t删除保存下来的压缩文件（.tar）

## 二、管理环境

（1）创建环境

conda create -n env-name [list of package]

-n env-name是设置新建环境的名字，list of package是可选项，选择要为该环境安装的包。

如果我们没有指定安装python的版本，conda会安装我们最初安装conda时所装的那个版本的python。

若创建特定python版本的包环境，需键入conda create -n env-name python=3.6

（2）激活环境
Linux，OS X:

source activate env-name

Windows：

activate env-name

小技巧：

新的开发环境会被默认安装在你conda目录下的envs文件目录下。你可以指定一个其他的路径；

（3）切换到base环境

如果要从你当前工作环境的路径切换到系统根目录时，键入：

Linux，OS X:

conda source deactivate

Windows:

conda deactivate

（4）复制一个环境

通过克隆来复制一个环境。这儿将通过克隆snowfllakes来创建一个称为flowers的副本。

conda create -n flowers --clone snowflakes
通过conda env list来检查目前拥有的环境

（5）删除一个环境

如果你不想要这个名为flowers的环境，就按照如下方法移除该环境：

conda env remove -n flowers

## 三、管理包

（1）安装包 或 安装特定版本的包

conda install package-name

conda install package-name==version

（2）查看所有已安装包

conda list

（3）卸载包

conda remove package-name

（4）更新包

更新一个包

conda update package-name

更新所有包

conda update --all

（5）搜索包

conda search search-term，可以模糊搜索

## 四、把环境添加到jupyter notebook

首先通过activate进入想要添加的环境中，然后安装ipykernel，接下来就可以进行添加了。

pip install ipykernel

python -m ipykernel install --name Python36Python36可以取与环境名不一样的名字，但方便起见建议统一

（1）查看已添加到jupyter notebook的kernel

我们可以使用jupyter kernelspec list来查看已添加到jupyter notebook的kernel。

显示如下：

PS C:\Users\25387> jupyter kernelspec list
Available kernels:
  python3    D:\Anaconda\anaconda\share\jupyter\kernels\python3

（2）删除指定的kernel

若想删除某个指定的kernel，可以使用命令jupyter kernelspec remove kernel_name来完成。

由于python是不向后兼容的，分开环境可以避免语法版本不一引起的错误，同时这也可以避免工具包安装与调用的混乱。

## 升级pytorch
conda install pytorch torchvision -c pytorch