# VSCode配置Python虚拟运行环境
* 该笔记参考VSCode官方文档 https://code.visualstudio.com/docs/python/environments 
* 这里默认已经安装VSCode、Python扩展，已经安装Python虚拟环境

##  <mark>VSCode中配置Python环境</mark>
* Python环境由一个解释器+任意数量的已安装包组成，VSCode的Python扩展为处理不同Python环境提供了支持

* 你可以随时切换Python环境，来帮助你使用不同的Python解释器或版本库，以测试运行你项目的不同部分.

* 可通过Python: Select Interpreter命令来选择特定的Python环境,该命令显示可用的全局环境、conda环境和虚拟环境<font color=Green>（python扩展会自动搜索1、python标准安装路径；2、位于工作区的虚拟环境；3、python.venvPath设置的路径；4、详细参考https://code.visualstudio.com/docs/python/environments#_where-the-extension-looks-for-environments）</font>，类似下图：

  ![alt tag](https://code.visualstudio.com/assets/docs/python/environments/interpreters-list.png)
选择一个Python解释器将在当前工作区的settings.json文件中注入python.pythonPath节点。

## <mark>VSCode中Python环境激活使用</mark>
* 使用Terminal:Create New Intergrated Terminal命令，可以可以启动激活当前指定的Python环境终端
* 使用Python:Run Python File in Terminal命令，可以启动激活当前指定的Python环境终端，并在该终端中运行当前.py文件

## <mark>VSCode中Python调试环境配置</mark>
* 调试时python解释器设置的优先级如下：
	1. launch.json文件配置的pythonPath节点
	2. 工作区间设置中的python.pythonPath节点
	3. 用户设置中的python.pythonPath节点

* 生成python调试配置launch.json的步骤如下：
	1. 点击如下红圈按钮：
	![alt tag](https://code.visualstudio.com/assets/docs/python/debugging/debug-settings-warning-icon.png)
	2. 选择Python File：
	![alt tag](https://code.visualstudio.com/assets/docs/python/debugging/debug-configurations.png)
	3. VSCode将显示Python扩展提供的默认配置如下：
	![alt tag](https://code.visualstudio.com/assets/docs/python/debugging/configuration-json.png)

* 可通过下图的”添加配置“按钮和对应列表添加其他调试配置：
	![alt tag](https://code.visualstudio.com/assets/docs/python/debugging/add-configuration.png)

	
	