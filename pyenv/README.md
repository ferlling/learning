# [pyenv](https://github.com/pyenv/pyenv)
## 1 介绍
在多Python版本的环境中，使用pyenv可以方便地管理和切换各Python版本。
通过pyenv-virtualenv插件，可以管理virtualenv环境。
## 2 原理
### 2.1 PATH
当运行Python或pip等命令时，会从PATH包含的一堆目录中，寻找文件名与运行命令相同的可执行文件。在PATH中寻找时，寻找目录从PATH中最左侧目录开始，依次向右，直至找到匹配文件。
### 2.2 Shims
pyenv将shims目录加入到PATH最前面，形如：

    $(PYENV_ROOT)/shims:/usr/local/bin:/usr/bin:/bin

shims目录下是轻量级的可执行文件（python、pip等），把python相关命令传递给pyenv，而不是直接运行系统python环境。
当运行python、pi、nosetests等命令时，系统实际行为如下（以pip为例）：

1. PATH中寻找文件名为"pip"的可执行文件（下文将这种文件称为"shim"）；
2. "(PYENV_ROOT)/shims"目录下找到pip；
3. 运行"(PYENV_ROOT)/shims/pip"，该文件会将命令及参数传递给pyenv执行。

pyenv通过"rehash"过程来维护shims目录，来管理每个python版本中的每个python相关命令。
（比如：pyenv 2.7.15中，pip install nose后，$HOME/.pyenv/versions/2.7.15/bin目录下会生成nosetests可执行程序；然后，pyenv会通过"rehash"过程，保证shims目录下也会有nosetests的shim文件；这样，运行nosetests shim时，会将命令和参数传递给pyenv，由其决定实际运行的版本。）
### 2.3 选择Python版本
当执行shim时，pyenv按以下顺序决定使用的Python版本：
1. PYENV_VERSION环境变量。可通过命令"pyenv shell"来设置，仅当前shell session有效。
2. 当前目录下的.python_version文件。可以通过命令"pyenv local"修改当前目录下的.python_version文件。
3. 寻找每级父目录下的.python_version文件，直至找到根目录为止；
4. "(PYENV_ROOT)/version"文件。可以通过"pyenv global"命令修改。如果该文件不存在，则使用"system" Python。
### 2.4 Python安装位置
一旦pyenv决定了您的应用程序应使用的Python版本，就会将命令传递给相应安装好的python。
每个Python版本安装在如下目录：

    ${PYENV_ROOT}/.pyenv/versions/${python_version}

对于pyenv而言，不同版本只是"${PYENV_ROOT}/.pyenv/versions"下的不同目录。
## 3 安装
### 3.1 通用安装方法
1. 下载pyenv至目录"$HOME/.pyenv"（也可自行选择其他目录）:

       $ git clone https://github.com/pyenv/pyenv.git ~/.pyenv

2. 设置环境变量：

       $ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
       $ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile

   注意：zsh使用"~/.zshenv"。

3. 使用"pyenv init"初始化设置:

       $ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
    
4. 重启shell使环境设置生效：

       $ echo $SHELL
       或者
       $ source ~/.bash_profile

5. 使用pyenv安装Python版本之前，[安装编译依赖](https://github.com/pyenv/pyenv/wiki)。

6. 安装具体Python版本，比如：
    
       $ pyenv install 2.7.15

   注意:
    * 可以通过"pyenv install -l"查询支持的Python版本。
    * 安装Python版本时，如遇问题，参考文档[Common Build Problems](https://github.com/pyenv/pyenv/wiki/Common-build-problems)。

### 3.2 升级
### 3.3 卸载
