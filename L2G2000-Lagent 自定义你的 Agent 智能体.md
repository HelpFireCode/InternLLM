# 一、前置知识

以下为结合训练营、网络资料与GPT的整合，不乏CV工程，但求看懂看明白。

## 1.Agent

### 1.1 定义

> 智能体（Agent），作为人工智能领域的一个重要概念，是指能够自主感知环境、做出决策并执行行动的系统。它具备自主性、交互性、反应性和适应性等基本特征，能够在复杂多变的环境中独立完成任务。智能体的出现，标志着人工智能从简单的规则匹配和计算模拟向更高级别的自主智能迈进。
>
> 智能体的核心在于其具备的学习和决策能力。通过学习算法和数据分析，智能体能够从海量数据中提取有用的信息，形成自己的知识库。在决策过程中，智能体能够综合考虑各种因素，运用逻辑推理、概率统计等方法，做出最优的决策。这种能力使得智能体在解决复杂问题时具有显著的优势。[^wolai]

也就是类人。大模型能做到的是根据文字反馈信息，此时的信息是基于已训练的内容。那随着网络内容的海量生成，若想通过大模型得到某新闻事件的最新进展，此时的回复可能并不正确。因此，若能使大模型使用工具获取最新内容送入prompt中，便具有与时俱进的能力。这只是一方面，为了便于理解想到的这个例子。若把新闻置换成信号、传感器数据，智能体便能根据这些数据来做出行动。

换句话说，使得大模型具备了使用工具的能力。

### 1.2 优缺点

> 智能体的基本特征在于其自主性、交互性、反应性和适应性。这些特征使得智能体能够在不同的环境中独立行动，与其他智能体或人类进行交互，对外部刺激做出反应，并根据经验调整自身的行为。
>
> 自主性是智能体的核心特征之一，它使得智能体能够在没有人类干预的情况下自主决策和行动。例如，在智能家居系统中，智能体可以根据室内温度和湿度自动调节空调和加湿器，以提供最舒适的居住环境。这种自主决策的能力使得智能体能够在实际应用中发挥巨大的作用。
>
> 交互性是智能体的另一个重要特征，它使得智能体能够与其他智能体或人类进行交流和合作。例如，在自动驾驶汽车中，智能体需要与交通信号灯、其他车辆和行人进行交互，以确保安全行驶。通过与其他实体的交互，智能体可以更好地理解环境，并做出更加明智的决策。这种交互性也使得智能体在团队协作和人机交互等领域具有广泛的应用前景。
>
> 反应性是指智能体能够对外部刺激做出及时的反应。例如，在机器人领域中，智能体需要能够感知环境的变化，并做出相应的动作来适应。这种反应性的要求使得智能体需要具备快速的处理能力和准确的感知能力。通过不断学习和优化，智能体可以逐渐提高自身的反应速度和准确性，从而更好地适应复杂多变的环境。
>
> 适应性是智能体的最后一个基本特征，它使得智能体能够根据经验调整自身的行为。通过不断学习和积累经验，智能体可以逐渐优化自身的决策和行动策略，以适应不同的环境和任务。这种适应性的要求使得智能体需要具备强大的学习能力和自我优化能力。例如，在智能推荐系统中，智能体需要根据用户的反馈和行为数据不断调整推荐策略，以提高推荐的准确性和用户满意度。
>
> 智能体的基本特征使得它能够在不同领域中发挥巨大的作用。随着技术的不断发展和优化，智能体的应用前景将越来越广阔。正如著名科学家霍金所说：“人工智能的发展将改变我们的生活，其潜力比火或电更大。”智能体的出现和发展将为我们带来更多的便利和创新。[^wolai]

### 1.3 两种范式

#### 1.3.1 通用智能体范式

> 这种范式强调模型无需依赖特定的特殊标记（special token）来定义工具调用的参数边界。模型依靠其强大的指令跟随与推理能力，在指定的**system prompt**框架下，根据任务需求自动生成响应。这种方式让模型在推理过程中能更灵活地适应多种任务，不需要对Tokenizer进行特殊设计。
>
> **优势**：
>
> - 灵活适应不同任务，无需设计和维护复杂的标记系统。
> - 适合快速迭代，降低微调和部署的复杂性。
> - 更易与多模态输入（如文本和图像）结合，扩展模型的通用性。
>
> **劣势**：
>
> - 由于没有明确标记，调用工具时的错误难以捕捉和纠正。
> - 在复杂任务中，模型生成可能不够精准，导致工具调用的准确性下降。
>
> **（1）ReAct**：将模型的推理分为**Reason**和**Action**两个步骤，并让它们交替执行，直到得到最终结果：
>
> - **Reason**：生成分析步骤，解释当前任务的上下文或状态，帮助模型理解下一步行动的逻辑依据。
> - **Action**：基于Reason的结果，生成具体的工具调用请求（如查询搜索引擎、调用API、数据库检索等），将模型的推理转化为行动。
>
> **（2）ReWoo**：全称为**Reason without Observation**，是在ReAct范式基础上进行改进的Agent架构，针对多工具调用的复杂性与冗余性提供了一种高效的解决方案。相比于ReAct中的交替推理和行动，ReWoo直接生成一次性使用的**完整工具链**，减少了不必要的Token消耗和执行时间。同时，由于工具调用的规划与执行解耦，这一范式在模型微调时不需要实际调用工具即可完成。
>
> - **Planner**：用户输入的问题或任务首先传递给Planner，Planner将其分解为多个逻辑上相关的计划。每个计划包含推理部分（Reason）以及工具调用和参数（Execution）。Task List按顺序列出所有需要执行的任务链。
> - **Worker**：每个Worker根据Task List中的子任务，调用指定工具并返回结果。所有Worker之间通过共享状态保持任务执行的连续性。
> - **Solver阶段**：Worker完成任务后，将所有结果同步到Solver。Solver会对这些结果进行整合，并生成最终的答案或解决方案返回给用户。

也就是说，该范式下的智能体，具备普适性。只需要在prompt中多指定些信息就能够完成智能体的动作。

#### 1.3.2 模型特化智能体范式

> 在这种范式下，模型的工具调用必须通过特定的**special token**明确标记。如InternLM2使用`<|action_start|>`和`<|action_end|>`来定义调用边界。这些标记通常与模型的Tokenizer深度集成，确保在执行特定任务时，能够准确捕捉调用信息并执行。
>
> **优势**：
>
> - 特定标记明确工具调用的起止点，提高了调用的准确性。
> - 有助于模型在部署过程中避免误调用，增强系统的可控性。
> - 提高对复杂调用链的支持，适合复杂任务的场景。
>
> **劣势**：
>
> - 需要对Tokenizer和模型架构进行定制，增加开发和维护成本。
> - 调用流程固定，降低了模型的灵活性，难以适应快速变化的任务。
>
> **（1）InternLM2案例分析：** 工具调用使用了如`<|plugin|>`、`<|interpreter|>`、`<|action_start|>`和`<|action_end|>`等特殊标记，确保每个调用都符合指定的格式。模型在执行任务时，依靠这些标记与系统紧密协作，保障任务的精准执行。文档链接：[InternLM-Chat Agent](https://github.com/InternLM/InternLM/tree/main/agent)

理解：不需要再在提示词里面多说什么，因为模型已经设置好了。

### 1.4 应用场景

#### 智能家居

> 智能体在智能家居中的应用已经日益普及，它们通过集成先进的AI技术，为家庭带来了前所未有的便捷和舒适。以智能音箱为例，亚马逊的Echo和谷歌的Home等智能音箱，通过语音识别和自然语言处理技术，能够响应用户的语音指令，控制家中的各种智能设备，如灯光、空调、电视等。据市场研究公司Strategy Analytics的数据显示，到2023年全球智能音箱的出货量预计将超过2.2亿台，显示出智能体在智能家居领域的巨大市场潜力。
>
> 智能体在智能家居中的应用不仅限于语音控制。它们还可以通过学习和适应家庭成员的习惯和需求，实现更加智能化的家居管理。例如，智能恒温器可以根据家庭成员的作息时间和室内温度自动调节暖气或空调，以达到节能和舒适的效果。据美国能源部的一项研究，使用智能恒温器可以节省约10%的能源成本。

- 应用：智能音箱，智能恒温器。
- 目标：自动调整设备，节约能源。
- 传感器：语音指令，家庭习惯。
- 执行器：灯光空调电视开关、温度调节。



# 二、实践过程

## 1.配置环境

```bash
# 创建环境
conda create -n lagent python=3.10 -y
# 激活环境
conda activate lagent
# 安装 torch
conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=12.1 -c pytorch -c nvidia -y
# 安装其他依赖包
pip install termcolor==2.4.0
pip install streamlit==1.39.0
pip install class_registry==2.1.2
pip install datasets==3.1.0
```

![image-20250211173620183](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211173620234.png)

![image-20250211173840442](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211173840497.png)



源码方式安装框架lagent

```bash
# 创建目录以存放代码
mkdir -p /root/agent_camp4
cd /root/agent_camp4
git clone https://github.com/InternLM/lagent.git
cd lagent && git checkout e304e5d && pip install -e . && cd ..
pip install griffe==0.48.0
```



## 2.agent之已有插件

申请API

![image-20250211174453852](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211174453926.png)

写入环境变量👇

> ```bash
> export token='your_token_here'
> ```

实现web_api_demo.py

> ```python
> import copy
> import os
> from typing import List
> import streamlit as st
> from lagent.actions import ArxivSearch
> from lagent.prompts.parsers import PluginParser
> from lagent.agents.stream import INTERPRETER_CN, META_CN, PLUGIN_CN, AgentForInternLM, get_plugin_prompt
> from lagent.llms import GPTAPI
> 
> class SessionState:
>     """管理会话状态的类。"""
> 
>     def init_state(self):
>         """初始化会话状态变量。"""
>         st.session_state['assistant'] = []  # 助手消息历史
>         st.session_state['user'] = []  # 用户消息历史
>         # 初始化插件列表
>         action_list = [
>             ArxivSearch(),
>         ]
>         st.session_state['plugin_map'] = {action.name: action for action in action_list}
>         st.session_state['model_map'] = {}  # 存储模型实例
>         st.session_state['model_selected'] = None  # 当前选定模型
>         st.session_state['plugin_actions'] = set()  # 当前激活插件
>         st.session_state['history'] = []  # 聊天历史
>         st.session_state['api_base'] = None  # 初始化API base地址
> 
>     def clear_state(self):
>         """清除当前会话状态。"""
>         st.session_state['assistant'] = []
>         st.session_state['user'] = []
>         st.session_state['model_selected'] = None
> 
> 
> class StreamlitUI:
>     """管理 Streamlit 界面的类。"""
> 
>     def __init__(self, session_state: SessionState):
>         self.session_state = session_state
>         self.plugin_action = []  # 当前选定的插件
>         # 初始化提示词
>         self.meta_prompt = META_CN
>         self.plugin_prompt = PLUGIN_CN
>         self.init_streamlit()
> 
>     def init_streamlit(self):
>         """初始化 Streamlit 的 UI 设置。"""
>         st.set_page_config(
>             layout='wide',
>             page_title='lagent-web',
>             page_icon='./docs/imgs/lagent_icon.png'
>         )
>         st.header(':robot_face: :blue[Lagent] Web Demo ', divider='rainbow')
> 
>     def setup_sidebar(self):
>         """设置侧边栏，选择模型和插件。"""
>         # 模型名称和 API Base 输入框
>         model_name = st.sidebar.text_input('模型名称：', value='internlm2.5-latest')
>         
>         # ================================== 硅基流动的API ==================================
>         # 注意，如果采用硅基流动API，模型名称需要更改为：internlm/internlm2_5-7b-chat 或者 internlm/internlm2_5-20b-chat
>         # api_base = st.sidebar.text_input(
>         #     'API Base 地址：', value='https://api.siliconflow.cn/v1/chat/completions'
>         # )
>         # ================================== 浦语官方的API ==================================
>         api_base = st.sidebar.text_input(
>             'API Base 地址：', value='https://internlm-chat.intern-ai.org.cn/puyu/api/v1/chat/completions'
>         )
>         # ==================================================================================
>         # 插件选择
>         plugin_name = st.sidebar.multiselect(
>             '插件选择',
>             options=list(st.session_state['plugin_map'].keys()),
>             default=[],
>         )
> 
>         # 根据选择的插件生成插件操作列表
>         self.plugin_action = [st.session_state['plugin_map'][name] for name in plugin_name]
> 
>         # 动态生成插件提示
>         if self.plugin_action:
>             self.plugin_prompt = get_plugin_prompt(self.plugin_action)
> 
>         # 清空对话按钮
>         if st.sidebar.button('清空对话', key='clear'):
>             self.session_state.clear_state()
> 
>         return model_name, api_base, self.plugin_action
> 
>     def initialize_chatbot(self, model_name, api_base, plugin_action):
>         """初始化 GPTAPI 实例作为 chatbot。"""
>         token = os.getenv("token")
>         if not token:
>             st.error("未检测到环境变量 `token`，请设置环境变量，例如 `export token='your_token_here'` 后重新运行 X﹏X")
>             st.stop()  # 停止运行应用
>             
>         # 创建完整的 meta_prompt，保留原始结构并动态插入侧边栏配置
>         meta_prompt = [
>             {"role": "system", "content": self.meta_prompt, "api_role": "system"},
>             {"role": "user", "content": "", "api_role": "user"},
>             {"role": "assistant", "content": self.plugin_prompt, "api_role": "assistant"},
>             {"role": "environment", "content": "", "api_role": "environment"}
>         ]
> 
>         api_model = GPTAPI(
>             model_type=model_name,
>             api_base=api_base,
>             key=token,  # 从环境变量中获取授权令牌
>             meta_template=meta_prompt,
>             max_new_tokens=512,
>             temperature=0.8,
>             top_p=0.9
>         )
>         return api_model
> 
>     def render_user(self, prompt: str):
>         """渲染用户输入内容。"""
>         with st.chat_message('user'):
>             st.markdown(prompt)
> 
>     def render_assistant(self, agent_return):
>         """渲染助手响应内容。"""
>         with st.chat_message('assistant'):
>             content = getattr(agent_return, "content", str(agent_return))
>             st.markdown(content if isinstance(content, str) else str(content))
> 
> 
> def main():
>     """主函数，运行 Streamlit 应用。"""
>     if 'ui' not in st.session_state:
>         session_state = SessionState()
>         session_state.init_state()
>         st.session_state['ui'] = StreamlitUI(session_state)
>     else:
>         st.set_page_config(
>             layout='wide',
>             page_title='lagent-web',
>             page_icon='./docs/imgs/lagent_icon.png'
>         )
>         st.header(':robot_face: :blue[Lagent] Web Demo ', divider='rainbow')
> 
>     # 设置侧边栏并获取模型和插件信息
>     model_name, api_base, plugin_action = st.session_state['ui'].setup_sidebar()
>     plugins = [dict(type=f"lagent.actions.{plugin.__class__.__name__}") for plugin in plugin_action]
> 
>     if (
>         'chatbot' not in st.session_state or
>         model_name != st.session_state['chatbot'].model_type or
>         'last_plugin_action' not in st.session_state or
>         plugin_action != st.session_state['last_plugin_action'] or
>         api_base != st.session_state['api_base']    
>     ):
>         # 更新 Chatbot
>         st.session_state['chatbot'] = st.session_state['ui'].initialize_chatbot(model_name, api_base, plugin_action)
>         st.session_state['last_plugin_action'] = plugin_action  # 更新插件状态
>         st.session_state['api_base'] = api_base  # 更新 API Base 地址
> 
>         # 初始化 AgentForInternLM
>         st.session_state['agent'] = AgentForInternLM(
>             llm=st.session_state['chatbot'],
>             plugins=plugins,
>             output_format=dict(
>                 type=PluginParser,
>                 template=PLUGIN_CN,
>                 prompt=get_plugin_prompt(plugin_action)
>             )
>         )
>         # 清空对话历史
>         st.session_state['session_history'] = []
> 
>     if 'agent' not in st.session_state:
>         st.session_state['agent'] = None
> 
>     agent = st.session_state['agent']
>     for prompt, agent_return in zip(st.session_state['user'], st.session_state['assistant']):
>         st.session_state['ui'].render_user(prompt)
>         st.session_state['ui'].render_assistant(agent_return)
> 
>     # 处理用户输入
>     if user_input := st.chat_input(''):
>         st.session_state['ui'].render_user(user_input)
> 
>         # 调用模型时确保侧边栏的系统提示词和插件提示词生效
>         res = agent(user_input, session_id=0)
>         st.session_state['ui'].render_assistant(res)
> 
>         # 更新会话状态
>         st.session_state['user'].append(user_input)
>         st.session_state['assistant'].append(copy.deepcopy(res))
> 
>     st.session_state['last_status'] = None
> 
> 
> if __name__ == '__main__':
>     main()
> 
> ```

参考课程文档代码，如上👆引用内容。

接着启动👇

```bash
streamlit run agent_api_web_demo.py
```

在本地powershell中输入执行端口映射

```bash
ssh -CNg -L 8501:127.0.0.1:8501 root@ssh.intern-ai.org.cn -p <你的 SSH 端口号>
```

询问gpt后的结果👇

> 这段命令用于通过SSH建立本地与远程服务器之间的隧道，具体解析如下：
>
> 1. **`ssh`**: 启动SSH客户端，用于连接远程服务器。
>
> 2. **`-C`**: 启用压缩，传输数据时进行压缩，提升速度。
>
> 3. **`-N`**: 不执行远程命令，仅用于端口转发。
>
> 4. **`-g`**: 允许远程主机连接本地转发端口。
>
> 5. **`-L 8501:127.0.0.1:8501`**: 设置本地端口转发。将本地的8501端口转发到远程服务器的127.0.0.1:8501。
>
> 6. **`root@ssh.intern-ai.org.cn`**: 以root用户连接远程服务器`ssh.intern-ai.org.cn`。
>
> 7. **`-p <你的 SSH 端口号>`**: 指定SSH连接的端口号，替换`<你的 SSH 端口号>`为实际端口。
>
> **总结**：该命令通过SSH隧道将本地的8501端口转发到远程服务器的8501端口，适用于访问远程服务或绕过防火墙。



不使用插件时，询问“帮我搜索最新版本的MindSearch论文“结果如下👇

![image-20250211183759433](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211183759552.png)



使用插件后，结果如下👇

![image-20250211183834606](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211183834723.png)





## 3.agent之diy插件——以天气工具为例

根据文档进行代码更改后，在没有使用插件的情况下，询问天气时，如下所示👇

![image-20250211185628793](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211185628931.png)



使用插件后，如下所示👇

![image-20250211185822724](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211185822866.png)

## 4.多智能体之博客搭建

![image-20250211191244779](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211191244900.png)

执行结果如上👆

## 5.上传huggingface

传递好token👇

![image-20250211191517703](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211191517761.png)

测试可见性👇

![image-20250211191927640](https://typora-urname.oss-cn-beijing.aliyuncs.com/git/20250211191927704.png)

实验多次失败。回头再找原因，接着闯关了……😂





# 参考资料

[^wolai]: https://www.wolai.com/cUCxuiNAQYJcpaYxCvR1QU

