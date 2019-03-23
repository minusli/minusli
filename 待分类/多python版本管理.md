# mac多python版本管理
## pyenv
- 安装pyenv：`brew install pyenv`
- 配置rc(.bash_profile or .zshrc)
    ```
    # 末尾添加两行，并且source
    # 初始化pyenv必要的环境，包括一些文件夹
    if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi
    # 设置pyenv的root目录（其中包括各个python版本的shims）
    export PYENV_ROOT=/usr/local/var/pyenv
    ```
- 查看当前python：`pyenv version`
- 查看已安装python：`pyenv versions`
- 安装python：`pyenv install -v 3.7.2`
    - 安装提示zlib错误：
        ```
        zipimport.ZipImportError: can't decompress data; zlib not available
        ```
        - 解决办法（[参考](https://github.com/danhper/asdf-python/issues/43)）
            ```
            brew install zlib
            export LDFLAGS="-L/usr/local/opt/zlib/lib"
            export CPPFLAGS="-I/usr/local/opt/zlib/include"
            export PKG_CONFIG_PATH="/usr/local/opt/zlib/lib/pkgconfig"
            export KEEP_BUILD_PATH=true
            ```
- 为新安装的python创建shims: `pyenv rehash`
- 切换python:
    ```
    pyenv global system
    pyenv local 3.7.2
    pyenv shell 3.7.2
    # 撤销切换
    pyenv local 3.7.2 --unset
    ```

## virtualenv&virtualenvwrapper
- 安装：`pip install virtualenv
    - 支持多个python独立环境
- 安装：`pip install virtualenvwrapper`
    - 方便多个python环境之间切换
- 环境配置
    ```
    #创建目录用来存放虚拟环境
    mkdir $HOME/.virtualenvs
    
    #在. bash_profile 或者 .zshrc 追加两行
    export WORKON_HOME=$HOME/.virtualenvs
    source /usr/local/bin/virtualenvwrapper.sh
    
    运行： 
    source ~/.bashrc
    source ~/.zshrc
    
    此时virtualenvwrapper就可以使用了。
    ```
- 根据当前python，创建虚拟环境，无sitepackage依赖
    - `mkvirtualenv --no-site-packages luffy`
- 列举所有虚拟环境：`workon`
- 切换虚拟环境：`workon luffy`
- 删除虚拟环境：`rmvirtualenv luffy`
- 退出虚拟环境：`deactive`

## 联合使用pyenv和virtualenv
```
#确保切换成功，我建议 source .zshrc 一下在切换
#安装全新的Python2.7.10版本
pyenv install 2.7.10
pyenv rehash

#切换到刚安装的这个版本
pyenv local 2.7.10
#确保切换成功
source .zshrc
#验证一下版本,pip发现里面包很少
pip list
#验证版本
python -V
#务必在这个新的2.7.10中安装
pip install virtualenv
pip install virtualenvwrapper
#务必
source .zshrc
#创建2.7.10的开发环境
mkvirtualenv py2dev

#创建完某版本的开发环境后务必退出，当前虚拟环境，不然就是虚拟环境中在创建了。
deactivate
#退出2.7.10环境
pyenv local --unset 2.7.10
source .zshrc
```