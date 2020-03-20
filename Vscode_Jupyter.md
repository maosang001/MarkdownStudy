#  在Vscode中使用Jupyter Notebooks

***

## 简介

* Jupyter Notebokks将markdown文本和可执行代码结合到一起

## 在本地使用Jupyter Notebooks

### 安装环境

* 在使用Jupyter Notebooks之前，必须要部署一个安装了Jupyter的python环境。
* 环境安装好以后，你就可以打开一个Jupyter Notebook连接到Jupyter Server。Vscode的python扩展插件在notebook editor中会默认打开一个Jupyter Notebook（.ipynb）。

### 创建运行.ipynb文件

* 直接创建xx.ipynb文件，当你选择改文件时，将自动打开一个Jupyter Notebook来运行code cell。

* 通过界面上的相关按钮运行、保存、切换cell。

* 选中cell，按L键可进行/取消行编号

* 点击variables图标，显示、检查、筛选变量。

  你还可以双击某行，或者使用变量旁的Show variable in data viewer查看变量的详细视图。

* 双击plot画出的图，可以更详细的查看图形变量（比如放大缩小，导出等）

### 连接远程Jupyter Server

* 可以连接远程Jupyter Server将计算密集型任务放到远程服务器运行。
* 连接步骤：
  * 运行 **Python: Specify Jupyter server URI** 命令
  * 按指示填入uri

***

# 遇到的问题

## 未安装jupyter

* 使用pip install jupyter安装jupyter

## 安装jupyter后无法启动

* 报错信息是：[Errno 99] Cannot assign requested address

* 运行 jupyter notebook --generate-config 生成配置文件 jupyter_notebook_config.py 

* 修改

  ```python
  c.NotebookApp.ip = '127.0.0.1'
  c.NotebookApp.open_brower = False
  ```

* 运行命令jupyter notebook启动jupyter server

## Vscode无法连接Jupyter Server

* 修改vscode配置Python: Specify Jupyter server URI，指向已启动的Jupyter Server