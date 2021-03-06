# 用Sphinx +Python搭建在线文档

参加工作快两年了，也不是第一次使用Sphinx +Python搭建页面，但是因为懒，很多工作中收获的编程技巧和框架都没有沉淀下来，于是现在把在线文档捡起来，将工作中不断实践的技术路线沉淀下来。

有关Sphinx的介绍，还没有了解的话可以自行百度或访问其官方网站：[Sphinx](https://www.sphinx-doc.org/en/master/index.html)。

默认环境是搭建在Ubuntu环境下，不熟悉Linux操作的同学请先入门Linux操作

## 1 安装 Sphinx

最方便的方法是使用**apt-get**命令安装：

```shell
sudo apt-get install python3-sphinx
```

更多安装方式可以参考官方提供的介绍：[Install Sphinx](https://www.sphinx-doc.org/en/master/usage/installation.html)。

## 2 创建工程

没有什么特殊需求的话，直接在官方提供的[快速开始](https://www.sphinx-doc.org/en/master/usage/quickstart.html)项目的基础上修改即可：

```shell
mkdir cplusplus.org.cn && cd cplusplus.org.cn
sphinx-quickstart
```

其中有一些需要输入的基本信息，根据自己的情况输入即可：

![quickstart](./images/quickstart.png)

创建成功后，目录结构如下图所示：

![project_tree](./images/project_tree.png)

- build目录

  用于保存构建输出的目录

- source目录

  用于放置文档的源文件(.md/.rst)

- make.bat

  批处理脚本

- Makefile

### 2.1 让页面显示在浏览器中

可以看到，在使用`sphinx-quickstart`之后，在`source`目录下生成了默认的一个索引文件`index.rst`。

为了第一时间看到静态网页的效果，先不对其进行编辑，在项目目录下执行：

```shell
make html
```

执行完毕后，可以看到在`build`目录下，生成了如下结构的文件：

![build_dir](./images/build_dir.png)

接下来就是托管静态网页，据我了解有以下几种方法：

- Github+ReadtheDocs代理
- Nginx/Apache代理
- Django代理

这其中，我只尝试过使用Nginx进行网页托管，并且使用Nginx代理也应该是最方便、稳定、成熟的一种方式。，而对于Nginx服务器的安装和部署，已经不再属于本文的主线内容，可以直接使用`sudo apt-get install nginx`在本机进行安装，也可以参考菜鸟教程中关于Nginx安装的描述：[Docker 安装 Nginx](https://www.runoob.com/docker/docker-install-nginx.html)。

安装完成Nginx服务后，找到Nginx默认的`index.html`所在目录，我的是`/usr/share/nginx`，直接将刚刚build目录下生成的`html`文件拷贝到`/var/www`：

```shell
sudo cp -r html /var/www
# 重新启动Nginx服务
sudo nginx -s reload
```

接下来在浏览器中输入`127.0.0.1`就可以看到生成的默认界面了。

![nginx_default_web](./images/nginx_default_web.png)

### 2.2 安装python依赖

除了为了实现效果更好的文档并使其支持markdown格式文件，需要安装以下模块：

```shell
sudo python3 -m pip install sphinx sphinx-autobuild recommonmark sphinx-markdown-tables sphinx_copybutton
```

安装后，修改`source/conf.py`文件，在文件中顶部添加以下代码，导入`AutoStructify`模块：

```python
from recommonmark.transform import AutoStructify
```

将默认的extensions选项中，加入以下内容：

```python
# -- General configuration ---------------------------------------------------

# Add any Sphinx extension module names here, as strings. They can be
# extensions coming with Sphinx (named 'sphinx.ext.*') or your custom
# ones.
extensions = [
    'sphinx_copybutton',
    'sphinx.ext.mathjax',
    'sphinx_markdown_tables',
    'recommonmark'
]
```

在文件的最后，加入以下代码，以完成对`.md`文件的支持：

```python
# The suffix of source filenames.
source_parsers = {
	'.md': 'recommonmark.parser.CommonMarkParser',
}
source_suffix = ['.rst', '.md']
```

### 2.3 编辑索引文件

一切就绪后，需要通过`index.rst`文件来组织网页和文章的结构。

`index.rst`文件使用的是一种类似于markdown和html的文本标记语言，对其具体的语法和介绍请参考官网：[reStructuredText Primer](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html)。

我的`index.rst`文件如下：

```rst
.. Rui's zone documentation master file, created by
   sphinx-quickstart on Thu Jun 16 23:53:27 2022.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Rui's zone's documentation!
======================================

.. important::
   - 本网站仅作为个人学习笔记整理，未授权用于任何商业用途，不承担任何因使用本站内容而导致的责任
   - 如有侵权请联系作者删除(liangrui5526@126.com)

.. toctree::
   :hidden:
   :maxdepth: 2
   :caption: Sphinx博客搭建

   articles/web/sphinx.md

.. toctree::
   :hidden:
   :maxdepth: 2
   :caption: Boost库精选

   articles/cpp/boost/index.rst

.. toctree::
   :hidden:
   :maxdepth: 2
   :caption: 高效开发

   articles/cpp/efficiency/vcpkg_boost.md
   articles/cpp/efficiency/vcpkg_install_windows.md
```

### 2.4 生成网页

在工程的根目录下，执行以下命令完成网页的生成和部署：

```shell
make html && sudo cp -r build/html /var/www
```

网页效果：

![web_primary](images/web_primary.png)
