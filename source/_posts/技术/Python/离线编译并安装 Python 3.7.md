---
title: 离线编译并安装 Python 3.7
date: 2024/07/06 12:04:09
updated: 2024/07/06 12:04:09
categories:
- [技术, Python]
tags:
- Python
- Pyinstaller
---

## 下载 


1. **下载 Python 3.7 源代码**：

   从 [Python 官方网站](https://www.python.org/ftp/python/3.7.12/Python-3.7.12.tgz) 下载 Python 3.7 的源代码：

   ```bash
   wget https://www.python.org/ftp/python/3.7.12/Python-3.7.12.tgz
   ```

2. **下载依赖项**：

   - `openssl-devel`
   - `bzip2-devel`
   - `libffi-devel`
   - `zlib-devel`
   - `xz-devel`
   - `sqlite-devel`
   - `readline-devel`

   使用以下命令下载这些依赖项的 RPM 包：

   ```bash
   yum install --downloadonly --downloaddir=./rpms openssl-devel bzip2-devel libffi-devel zlib-devel xz-devel sqlite-devel readline-devel
   ```

   >  将所有依赖项下载到 `./rpms` 目录中，方便离线传输。



## 拷贝到离线服务器

将下载的 `Python-3.7.12.tgz` 文件和 `rpms` 目录中的 RPM 文件复制到离线服务器上。



##  安装依赖项

在离线服务器上安装依赖项：

```bash
cd /path/to/rpms
sudo yum localinstall *.rpm
```



##  解压并编译

1. **解压 Python 源代码**：

   ```bash
   tar xzf Python-3.7.12.tgz
   cd Python-3.7.12
   ```

2. **配置、编译并安装 Python**：

   ```
   sudo ./configure --enable-optimizations   --enable-shared
   sudo make altinstall
   ```
> 手动编译需要携带` --enable-shared`参数，否则使用Pyinstarller打包软件的时候会报错： 
```bash
raise IOError(msg)
OSError: Python library not found: libpython3.7m.so.1.0, libpython3.7.so.1.0, libpython3.7mu.so.1.0, libpython3.7.so, libpython3.7m.so
This means your Python installation does not come with proper shared library files.
This usually happens due to missing development package, or unsuitable build parameters of the Python installation.

* On Debian/Ubuntu, you need to install Python development packages:
  * apt-get install python3-dev
  * apt-get install python-dev
* If you are building Python by yourself, rebuild with `--enable
```



##  验证安装

验证 Python 3.7 是否安装成功：

```
python3.7 --version
```

看到输出内容为 `Python 3.7.12`。



### 使用虚拟环境

建议在使用新的 Python 版本时创建虚拟环境：

```
# 创建 Python 3.7 虚拟环境
python3.7 -m venv myenv

# 激活虚拟环境
source myenv/bin/activate

# 验证 Python 版本
python --version  # 输出应该是 Python 3.7.x

# 升级 pip
pip install --upgrade pip

# 安装 pyinstaller
pip install pyinstaller

# 使用 pyinstaller 打包脚本
pyinstaller your_script.py
```



（完）

