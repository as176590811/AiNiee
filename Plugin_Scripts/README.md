
# 插件系统编写指南
欢迎来到插件系统编写指南！本文档将帮助您了解如何为项目编写插件


## 目录
1. [插件编写步骤](#插件编写步骤)
2. [继承插件基类](#继承插件基类)
3. [事件触发说明](#事件触发说明)
4. [示例代码](#示例代码)
5. [插件事件介绍](#传入参数介绍)
6. [贡献指南](#贡献指南)



## 插件编写步骤
1. **环境准备**
   确保您的开发环境满足以下要求：
   - Python 3.9
   - 相关依赖库（请查看`requirements.txt`）
2. **创建插件文件**
   在项目的`Plugin_Scripts`目录下创建新的子文件夹，并创建新的Python文件，例如`my_plugin.py`。
3. **编写插件代码**
   按照以下模板编写您的插件代码，并确保继承自`PluginBase`类。


## 继承插件基类
所有插件必须继承自`PluginBase`类。以下是`PluginBase`类的简化定义：
```python
class PluginBase:
    def __init__(self):
        self.name = "Unnamed Plugin"
        self.description = "No description provided."

    def load(self):
        """加载插件时调用"""
        pass

    def on_event(self, event_name, configuration_information, event_data):
        """处理事件"""
        pass

```
在编写插件时，您需要创建一个继承自`PluginBase`的新类，并实现必要的方法。



## 事件触发说明
插件可以通过重写`on_event`方法来监听和响应事件。以下是事件触发的基本流程：

1. **重写事件处理方法**
   在您的插件类中重写`on_event`方法，该方法将接收事件名称和数据。

2. **监听事件**
   在`on_event`方法内部，根据事件名称执行相应的逻辑。

以下是部分事件示例：
- `preproces_text`: 读取原文文件到缓存中，开始请求前触发。
- `postprocess_text`: 翻译完成，输出文件前触发。



## 示例代码
以下是一个简单的插件示例，它继承自`PluginBase`并监听了`preproces_text`事件：
```python
from ..Plugin_Base.Plugin_Base import PluginBase
class GreetingPlugin(PluginBase):
    def __init__(self):
        super().__init__()
        self.name = "example_Plugin"
        self.description = "This is an example plugin."

    def load(self):
        print(f"[INFO]  {self.name} loaded!")

    def on_event(self, event_name, configuration_information, event_data):
        # 事件触发
        if event_name == "preproces_text":

            # 如果翻译日语或者韩语文本时，则去除非中日韩文本
            if  configuration_information.source_language == "日语" or  configuration_information.source_language == "韩语":
                
                # 过滤文本
                self.preproces_text(event_data)

                print(f"[INFO]  Non-Japanese/Korean text has been filtered.")
```



## 插件事件介绍
本插件会触发以下事件，并提供相应的参数供其他插件或功能模块使用。

### 事件：preproces_text

1. **触发位置**

    读取原文文件到缓存中，开始请求前触发。

2. **传入参数**

    | 参数名 | 类型 | 描述 |
    | ------ | ---- | ---- |
    | event_name | string | preproces_text |
    | configuration_information | class | 全局类，包含了在整个应用范围内共享的的配置信息 |
    | event_data | list | 全局缓存文本数据，格式与导出的缓存文件一致 |

- `configuration_information`: 全局配置类，用于获取和设置应用程序的配置信息。
    下面是部分配置信息变量，如果需要获取更多配置信息，可以到Config.py文件中查看，基本在__init__(self,script_dir)与 Read_Configuration_File (self)中
    ```python
        self.script_dir = script_dir          # 根目录路径
        self.resource_dir = os.path.join(script_dir, "Resource") # 配置文件路径
        self.plugin_dir = os.path.join(script_dir, "Plugin_Scripts") # 插件脚本路径

        self.translation_project = "" # 翻译项目,与UI显示的内容一致
        self.translation_platform = "" # 翻译平台,与UI显示的内容一致
        self.source_language = "" # 文本原语言
        self.target_language = "" # 文本目标语言
        self.Input_Folder = "" # 存储输入文件夹
        self.Output_Folder = "" # 存储输出文件夹

        self.lines_limit_switch = True  # 行数开关         
        self.lines_limit = 15  # 行数限制
        self.tokens_limit_switch = False   # tokens开关       
        self.tokens_limit = 2000  # tokens限制
        self.user_thread_counts = 1 # 用户设置的线程数
        self.running_thread_counts= 1  # 实际设置的线程数
        self.pre_line_counts = 0 # 上文行数
        self.cot_toggle = False # 思维链开关
        self.cn_prompt_toggle = False # 中文提示词开关
        self.text_clear_toggle = False # 清除首尾非文本字符开关
        self.preserve_line_breaks_toggle = False # 换行替换翻译开关
        self.conversion_toggle = False #中文字形转换开关
        self.round_limit = 6 # 拆分翻译轮次限制
        self.retry_count_limit = 1 # 错误回复重试次数限制

        self.mixed_translation_toggle = False # 混合翻译开关
        self.mixed_translation_settings = {}  #混合翻译相关信息


        self.prompt_dictionary_switch = False   #   提示字典开关
        self.pre_translation_switch = False #   译前处理开关
        self.post_translation_switch = False #   译后处理开关
        self.custom_prompt_switch = False #   自定义prompt开关
        self.add_example_switch = False #   添加示例开关


        self.model_type = ""             #模型选择
        self.apikey_list = [] # 存储key的列表
        self.key_index = 0  # 方便轮询key的索引
        self.base_url = 'https://api.openai.com/v1' # api请求地址
        self.max_tokens = 4000
        self.RPM_limit = 3500
        self.TPM_limit = 10000000


        self.openai_temperature_initialvalue = 0        #AI的随机度，0.8是高随机，0.2是低随机,取值范围0-2
        self.openai_top_p_initialvalue = 0              #AI的top_p，作用与temperature相同，官方建议不要同时修改
        self.openai_presence_penalty_initialvalue = 0  #AI的存在惩罚，生成新词前检查旧词是否存在相同的词。0.0是不惩罚，2.0是最大惩罚，-2.0是最大奖励
        self.openai_frequency_penalty_initialvalue = 0 #AI的频率惩罚，限制词语重复出现的频率。0.0是不惩罚，2.0是最大惩罚，-2.0是最大奖励
        self.sakura_temperature_initialvalue = 0        
        self.sakura_top_p_initialvalue = 0             
        self.sakura_frequency_penalty_initialvalue = 0 
        self.anthropic_temperature_initialvalue   =  0 
        self.google_temperature_initialvalue   =  0 
        self.cohere_temperature_initialvalue   =  0 

        # 缓存数据以及运行状态
        self.cache_list = [] # 全局缓存文本数据
        self.Running_status = 0  # 存储程序工作的状态，0是空闲状态
                                # 1是正在接口测试状态,6是翻译任务进行状态，9是正在暂停状态，10是已暂停状态,11是正在取消状态，0也是已取消状态
    ```  

    - `event_data`: 全局缓存文本数据，格式与导出的缓存文件一致。
    ```python
        """
        缓存数据以列表来存储，分文件头（第一个元素）和文本单元(后续元素)，文件头数据结构如下:
        1.项目类型： "project_type"
        2.项目ID： "project_id"

        文本单元的部分数据结构如下:
        1.翻译状态： "translation_status"   未翻译状态为0，已翻译为1，正在翻译为2，不需要翻译为7
        2.文本索引： "text_index"
        3.名字： "name"
        4.原文： "source_text"
        5.译文： "translated_text"
        6.存储路径： "storage_path"
        7.存储文件名： "storage_file_name"
        8.翻译模型： "model"                         
        等等

        """
    ```

### 事件：postprocess_text

1. **触发位置**

    翻译完成，输出翻译文件前触发。

2. **传入参数**

    | 参数名 | 类型 | 描述 |
    | ------ | ---- | ---- |
    | event_name | string | postprocess_text |
    | configuration_information | class | 全局类，包含了在整个应用范围内共享的的配置信息 |
    | event_data | list | 全局缓存文本数据，格式与导出的缓存文件一致 |



### 事件：complete_text_process

1. **触发位置**

    用户使用“手动导出翻译文件功能”，在翻译文件导出前触发。

2. **传入参数**

    | 参数名 | 类型 | 描述 |
    | ------ | ---- | ---- |
    | event_name | string | postprocess_text |
    | configuration_information | class | 全局类，包含了在整个应用范围内共享的的配置信息 |
    | event_data | list | 全局缓存文本数据，格式与导出的缓存文件一致 |



### 事件：complete_text_process

1. **触发位置**

    每次请求，接受到AI的回复后触发。

2. **传入参数**

    | 参数名 | 类型 | 描述 |
    | ------ | ---- | ---- |
    | event_name | string | complete_text_process |
    | configuration_information | class | 全局类，包含了在整个应用范围内共享的的配置信息 |
    | event_data | string | AI补全生成的全部文本 |

3. **返回参数**
    | 参数名 | 类型 | 描述 |
    | ------ | ---- | ---- |
    | complete_text | string | 处理完成的回复内容 |




### 事件：translation_completed

1. **触发位置**

    翻译完成，写出翻译文件，任务即将退出前。

2. **传入参数**

    | 参数名 | 类型 | 描述 |
    | ------ | ---- | ---- |
    | event_name | string | complete_text_process |
    | configuration_information | class | 全局类，包含了在整个应用范围内共享的的配置信息 |
    | event_data | None | None |



## 贡献指南
1. Fork本项目
2. 创建您的特性分支 (`git checkout -b my-new-feature`)
3. 提交您的改动 (`git commit -am 'Add some feature'`)
4. 将您的分支推送到GitHub (`git push origin my-new-feature`)
5. 创建新的Pull Request