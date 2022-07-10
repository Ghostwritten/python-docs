#  python 3.10.0 源码编译安装

## 1. 安装编译依赖工具
```bash
 yum install gcc openssl-devel gcc-c++ compat-gcc-34 compat-gcc-34-c++
```
## 2. 下载python 3.10.0

```bash
 curl -O https://www.python.org/ftp/python/3.10.0/Python-3.10.0.tgz
#crul -O url 下载文件（使用文件本来的名字）。
#curl -o new_name url 下载文件（将文件重新命名为 new_name）
或者
wget https://www.python.org/ftp/python/3.10.0/Python-3.10.0.tgz

#解压
tar zxf Python-3.10.0.tgz
```

 - -z：使用 gzip 压缩或解压缩；
 - -x：解包；
 - -f：指定文件。
 - 使用 `gzip -l Python-3.10.0.tgz` 可以查看压缩率。

##  3. 编译安装 Python

```bash
cd Python-3.10.0
./configure --enable-optimizations --with-ssl --prefix=/usr/local/python-3.10.0
```

 - `--enable-optimizations` 用于优化编译；
 - -`-prefix=/usr/local/python-3.10.0` 用于以结构化的方式，将 Python 安装到 `/usr/local/python-3.10.0`。
 - 二进制文件在 `/usr/local/python-3.10.0/bin`；
 - 头文件在 `/usr/local/python-3.10.0/include`；
 - 库文件在 `/usr/local/python-3.10.0/lib`；
 - 其它的资源文件在 `/usr/local/python-3.10.0/share`，如帮助文档。

如果不配置 `--prefix`，安装文件将分散在多个位置：

 - 二进制文件默认在 `/usr/local/bin`；
 - 头文件在 `/usr/local/include`；
 - 库文件默认在 `/usr/local/lib`；
 - 其它的文件在 `/usr/local/share`。

编译成二进制：

make

漫长的编译过程~~~

编译时可能会出现很多警告，例如，centOS 最小化安装时没有安装图形化界面，因此编译 Python 中的 tkinter 模块时可能会出现警告。

如果编译失败，可以尝试重新编译，很多时候再编译一次就能成功。你既可以执行 make clean，清除之前的编译文件后再次 make， 也可以全部删除，然后从头再解压、configure、make 一次。

```bash
#检查编译结果
make test

#安装软件
make altinstall
```

设置全局环境变量 `PATH`，并为 Python 设置一个别名 py。在目录 `/etc/profile.d/` 下新建一个文件，此处命名为 `python.sh`，在文件中写入：

```bash
export PATH=/usr/local/python-3.10.0/bin:$PATH
alias py='/usr/local/python-3.10.0/bin/python3.10'
```

使配置文件立即生效：

```bash
source /etc/profile.d/python.sh
```
除了设置环境变量 PATH，另一种方法是在已有的 PATH 目录下（如 /usr/bin、/usr/sbin）建立 Python 和 pip 的软链接：

```bash
ln -s /usr/local/python-3.10.0/bin/python3.10 /usr/bin/python3.10
ln -s /usr/local/python-3.10.0/bin/pip3.10 /usr/bin/pip3.10
```

添加帮助文档。在 /etc/man_db.conf 中添加一条 MANPATH：

```bash
MANDATORY_MANPATH                       /usr/local/python-3.10.0/share/man
```

添加之后的 man_db.conf 文件，应该如下所示：

```bash
MANDATORY_MANPATH                       /usr/man
MANDATORY_MANPATH                       /usr/share/man
MANDATORY_MANPATH                       /usr/local/share/man
MANDATORY_MANPATH                       /usr/local/python-3.10.0/share/man
```

##  4. 体验
在命令行输入 py、python3.10、pip3.10 等命令，检查是否可用

```bash
$  pip3
pip3     pip3.10  
$ python3
python3            python3.10         python3.10-config  python3-config     
```
