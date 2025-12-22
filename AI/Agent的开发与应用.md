# 大语言模型

## 大规模语言模型介绍

> 大语言模型是以深度学习为基础，从大量未带标签的文本中学习众多模型参数。

> 深度学习是机器学习的分支，特点拥有多层神经网络（所以称为深度）。深度神经网络可以自动学习分层数据表示，从每一层的输入数据中提取更抽象的特征。

人工神经网络的基本构件是人工神经元，也称节点或单元。神经元被组织成了若干层，它们之间连接通过加权表示关系的强度。权重代表了模型训练过程被优化的参数。

非结构化文本数据作为模型输入的数据时需要两项基本活动：

1. 词元化：将一段文本分解为词元的较小单元的过程。目的是创建文本的结构化表示，便于机器学习模型处理。
2. 嵌入：每个词元会被转化为一个密集的数字向量。在语言模型训练中学到，捕捉词元之间的语义关系。数字表示法允许模型对词元进行数据运算，理解词元出现的语境。

### 反向传播

> 是深度学习中用于训练神经网络的一种算法。有两个阶段：前向传递通过网络计算输出；反向传递，网络通过比较预期结果与实际结果，尽量减小二者误差或损伤来进行学习，目的找到最佳权重集，达到新数据的精准预测。

### 贝叶斯定理

> 根据新证据来更新假设的概率。

$$
P(A|B)=\frac{P(B|A)P(A)}{P(B)}=\frac{P(B|A)P(A)}{\sum_{j=1}^n P(A_{j})P(B|A_{j})}
$$

- ‌**P(A|B)**‌：在B发生条件下A发生的概率（后验概率）
- ‌**P(B|A)**‌：在A发生条件下B发生的概率（似然）
- ‌**P(A)**‌：事件A的先验概率
- ‌**P(B)**‌：事件B的边缘概率（可通过全概率公式计算）

### 非线性激活函数

> 用于对神经网络的输出进行归一化处理。比如Softmax函数

### 模型训练步骤

1. 数据收集
2. 数据预处理：对数据进行清洗、过滤和格式化的过程。包括去除重复数据、噪声或敏感信息。
3. 模型架构：选择神经网络类型（如transformer）、结构（如仅解码器等）、层数和规模、注意力机制、激活函数等。
4. 模型初始化：为大语言模型的权重和偏置项分配初始值的过程。
5. 模型预训练：向模型输入成批数据并计算**损失函数**来更新权重和偏置项的过程。通过**优化算法**（如随机梯度下降算法）将损失最小化，该算法通过反向传播机制尽可能的减少权重和偏置项损失。
6. 微调：基础模型通过一个由（提示、理想响应）元组组成的数据集进行监督训练。输出称为监督微调（SFT）模型
7. 基于人类反馈的强化学习（RLHF）：奖励模型（结合人类偏好训练的另一个模型），反复优化监督微调模型

### 模型评估

* 通用语言理解评估（GLUE）和超级通用语言理解评估（SuperGLUE）：用户衡量大语言模型在各种语言的理解任务上的性能。GLUE得分越高，代表在不通任务领域中的泛化能力越强。
* 大规模多任务语言理解（MMLU）：使用零样本或少量样本测量语言模型所学到的知识
* HellaSwag：根据语言模型在给定语境中生成合理且符合常识的连续语句能力的评估
* TruthfulQA：评估模型生成问题回答时的准确性。
* AI2推理挑战（ARC）：衡量模型的推理能力，促进可执行复杂自然语言理解任务的模型开发

## Transformer

### 早期实验

> 梯度是一种度量模型内部参数（权重）微调后性能提高的指标。

1. 循环神经网络（RNN）：具有递归连接，允许信息跨时间步长持续存在，但存在梯度消失或爆炸的问题。循环神经网络会根据损失函数的梯度调整权重，以减少预测结果与实际目标之间的差异。训练中梯度变得极小或极大就会出现问题。在捕捉长距离依赖性存在局限。
2. 长短期记忆（LSTM）：可解决梯度消失的问题，引入门控机制，在更长的序列中保存重要信息。

### 架构

* 输入嵌入：词元分析器输入文本的向量表示
* 位置编码：提供了输入序列中有关单词的位置，使模型能够理解词元的顺序
* 多头注意力层：对输入数据的不同部分进行并行操作，从而产生多个表征，从多个角度聚合信息。
* 加法和归一化层：将层的输出添加到原始输入中，应用层进行归一化来稳定和加速训练过程。有助于缓解梯度问题，提高序列数据性能。
* 前馈层：使用非线性函数，将注意力层的归一化输出转换为适合的输出表示形式。
* 输出嵌入（右移）：解码器在目标序列右移一个位置。在每个位置上，模型都会尝试预测原始目标序列中的当前分析词元之后的词元。自回归解码
* 线性和softmax层：分别对输出向量进行线性和非线性变换。非线性变换将输出向量转为概率分布，与一组候选词相对应。

![transformer架构](./assets/transformer架构.jpg)



#  Agent概述

## Agent的技术框架结构与关键模块 

### 三层结构

智能体架构包含：感知模块、决策模块、执行模块以及反馈机制

### 上下文管理与记忆模块

1. 短期记忆：存储在内存缓存中，会在本次会话结束后情况
2. 长期记忆：利用数据库支持持久化存储，智能体在未来会话中查询历史数据

### 向量数据库语义检索中的作用

#### 设计与应用关键点

1. 高效存储与检索：支持大规模数据的高效存储与检索。常用向量数据库包括`FAISS`和`Milvus`，他们能快速计算向量之间的相似度，并支持分布式部署。
2. 数据更新与维护：可以定期更新数据
3. 查询优化与缓存：可以查询缓存和索引优化计算，通常优化包括构建向量索引和提前计算相似度
4. 与大语言模型的协同：与大语言模型结合。模型负责生成向量表示，数据库负责高效存储和检索。

### 常用框架和开发者平台

#### ReAct

多步推理框架，将决策过程与任务执行融合在一起。在多问题时，可以根据推理机制判断问题的优先级，决定任务的执行顺序。不仅能够根据预设的规则进行任务规划，还可以通过不断学习和反馈优化自身决策过程。

#### Hugging Face

1. 模型库的开放性和多样性
2. 模型微调与部署的便捷性
3. 集成工具与API支持
4. 社区与开源支持

#### LangChain

多步推理和复杂任务自动化的开源框架，专注于将语言模型与多种工具集成起来。将大语言模型作为基础组件，通过链式结构实现任务的分解与执行。支持API、数据库和外部知识库无缝集成，使智能体可以高效处理复杂任务流。

优势还体现在灵活的任务调度能力。可以根据任务的复杂程度和优先级，动态调整执行路径，确保关键任务得到及时处理。

# LangChain

## 组件与功能介绍

### 链式逻辑

> 设计思想：链式逻辑将复杂任务拆解为多个步骤，并按照特定顺序依次执行，提供系统灵活性和可扩展性

链条逻辑任务结构分为：

1. 顺序链：任务按固定顺序依次进行，每个步骤输出作为下一个步骤输入。
   
2. 分支链：根据条件判断执行不同路径的任务。

3. 循环链：处理重复性任务

任务链分解策略：

1. 单一职责原则
2. 逻辑耦合最小化
3. 任务优先级划分
4. 异步处理策略

### 数据流与上下文传递

LangChain支持上下文管理，通过在链条内存储和传递变量来保持任务状态。

### 集成LLM进行推理与生成

LangChain无缝集成了多个大语言模型，为智能体的推理和自然语言生成提供支持。

### 回调与实时监控功能

LangChain帮助开发者跟踪任务链的执行状态，出现异常时及时响应。回调机制可以在链条执行的每个步骤中记录关键数据，并基于这些数据优化。

同时还支持错误回滚与重试机制，可以根据预定义的规则回滚，或在一定时间内重试。

## 多步推理和任务自动化

### 决策链条与自动化触发

1. SequentialChain：顺序执行任务链
   ```python
   from langchain_community.chat_models import ChatOpenAI
   from langchain.prompts import PromptTemplate
   from langchain.chains import SequentialChain, LLMChain
   from langchain.callbacks import StdOutCallbackHandler
   
   # 初始化 GPT-4 模型
   llm = ChatOpenAI(model_name="gpt-4", temperature=0.7)
   # 创建提示模板
   template_1 = PromptTemplate(
       input_variables=["query"],
       template="请解析查询：{query}"
   )
   template_2 = PromptTemplate(
       input_variables=["parsed_result"],
       template="基于解析结果：{parsed_result}，生成响应。"
   )
   # 创建回调处理程序
   callback = StdOutCallbackHandler()
   # 将模板与 LLM 结合，创建 LLMChain
   chain_1 = LLMChain(llm=llm, prompt=template_1, output_key="parsed_result")
   chain_2 = LLMChain(llm=llm, prompt=template_2, output_key="final_response")
   # 创建顺序任务链
   sequential_chain = SequentialChain(
       chains=[chain_1, chain_2],  # 加入任务链
       input_variables=["query"],  # 初始输入变量
       output_variables=["final_response"],  # 最终输出变量
       callback=[callback]
       memory=True,  # 开启上下文记忆
       verbose=True
   )
   # 执行任务链并打印结果
   response = sequential_chain.run({"query": "如何训练机器学习模型？"})
   print(response)
   ```

2. RouterChain：路由任务链
   ```python
   from langchain.chains import RouterChain
   # 根据输入动态选择任务路径
   router_chain = RouterChain(
       conditions={"查询订单": order_query_chain, "查询物流": logistics_query_chain}
   )
   # 运行任务链
   router_chain.run({"query": "查询物流信息"})
   ```

3. WebhookChain：事件触发任务链
   ```python
   from langchain.chains import WebhookChain
   # 创建 Webhook 任务链
   webhook_chain = WebhookChain(webhook_url="https://example.com/webhook")
   # 定义任务逻辑
   def on_new_order(data):
       chain.run(data)
   # 注册事件回调
   webhook_chain.on_event(on_new_order)
   ```

4. BlockingScheduler：定时器
   ```python
   from apscheduler.schedulers.blocking import BlockingScheduler
   from langchain.chains import SequentialChain
   # 创建顺序任务链
   chain = SequentialChain(chains=[...])
   # 创建调度器并定义定时任务
   scheduler = BlockingScheduler()
   scheduler.add_job(lambda: chain.run({"query": "生成日报"}), 'cron', hour=8)
   # 启动调度器
   scheduler.start()
   ```

5. ConversationChain：对话任务链
   ```python
   from langchain.chains import ConversationChain
   from langchain.llms import OpenAI
   # 初始化语言模型
   llm = OpenAI(model_name="gpt-4")
   # 创建对话任务链
   conversation_chain = ConversationChain(llm=llm, verbose=True)
   # 用户输入触发任务链
   response = conversation_chain.predict(input="查询订单状态")
   print(response)
   ```

### 任务链的优化

1. 异步任务链：可以独立运行，不依赖其他模块输出。
   ```python
   from langchain.chains import AsyncSequentialChain
   # 异步任务链，市场数据与客户分析并行执行
   async_chain = AsyncSequentialChain(
       chains=[market_data_chain, client_analysis_chain],
       verbose=True
   )
   # 执行异步任务链
   await async_chain.run(input_data)
   ```

2. 缓存机制
   ```python
   from langchain.cache import InMemoryCache
   # 初始化缓存系统
   cache = InMemoryCache()
   # 使用缓存查询客户数据
   def get_client_preferences(client_id):
       if cache.get(client_id):
           return cache.get(client_id)
       preferences = query_client_preferences(client_id)  # 查询数据库
       cache.set(client_id, preferences)
       return preferences
   from langchain.chains import RouterChain
   # 根据客户类型动态选择任务路径
   router_chain = RouterChain(
       conditions={
           "短期投资": short_term_strategy_chain,
           "长期投资": long_term_strategy_chain,
       }
   )
   # 执行动态任务链
   router_chain.run({"client_type": "短期投资"})
   ```

3. 错误处理与自动重试
   ```python
   from langchain.chains import SequentialChain
   def safe_execute_module(module, retries=3):
       for attempt in range(retries):
           try:
               return module.run()
           except Exception as e:
               print(f"错误发生：{e}，重试次数：{attempt + 1}")
       raise Exception("任务执行失败")
   # 包装模块执行，确保发生错误时自动重试
   safe_execute_module(market_data_chain)
   ```

4. 实时监控与性能分析
   ```python
   from langchain.callbacks import StdOutCallbackHandler
   # 初始化回调处理程序
   callback = StdOutCallbackHandler()
   # 监控任务链执行
   chain = SequentialChain(
       chains=[market_data_chain, client_analysis_chain],
       callbacks=[callback],
       verbose=True
   )
   ```

## 集成外部数据源工具

> 利用python相关的工具库，可以集成到Agent中

### 数据库

1. 关系数据库的集成：可以连接关系数据库查询数据，作为数据的输入或输出。

2. 向量存储的集成：对于非结构化的数据，向量数据库可以利用向量间相似匹配快速检索相关信息。
   ```python
   from langchain.vectorstores import FAISS
   from langchain.embeddings import OpenAIEmbeddings
   # 初始化向量数据库
   vector_store = FAISS.load_local("path/to/index", OpenAIEmbeddings())
   # 根据查询内容检索相似文档
   results = vector_store.similarity_search("查询物流信息", k=5)
   ```

### API调用与外部系统集成

1. 支持RESTful API
2. GraphQL集成

### 文件和文档处理模块集成

1. PDF文件处理
   ```python
   from PyPDF2 import PdfReader
   # 读取 PDF 文件并提取文本
   def extract_text_from_pdf(file_path):
       reader = PdfReader(file_path)
       text = ""
       for page in reader.pages:
           text += page.extract_text()
       return text
   ```

2. Excel文件处理
   ```python
   import pandas as pd
   # 读取 Excel 文件并解析数据
   df = pd.read_excel("report.xlsx")
   summary = df.describe()
   ```

### 物联网与边缘设备的集成

1. 传感器数据集成
   ```python
   import paho.mqtt.client as mqtt
   # 初始化 MQTT 客户端并连接到 IoT 平台
   client = mqtt.Client()
   client.connect("broker.hivemq.com", 1883)
   # 订阅温度传感器数据
   def on_message(client, userdata, message):
       temperature = float(message.payload.decode())
       if temperature > 25:
           print("启动空调")
   client.on_message = on_message
   client.subscribe("home/temperature")
   client.loop_start()
   ```

## 构建记忆能力与对话

### 短期记忆与上下文管理

### 长期记忆设计

1. 将键值对通过json文件存储到本地，用于长期增删改查

2. 优化策略包括：上下文截断、重要信息标记和动态上下文管理
   ```python
   from langchain.memory import ConversationBufferMemory
   # 初始化上下文管理器，并限制上下文长度
   memory = ConversationBufferMemory(memory_key="chat_history", max_length=5)
   # 在对话链中使用上下文管理器
   conversation = ConversationChain(llm=llm, memory=memory)
   ```

# LlamaIndex

> 针对大规模数据进行快速查询和高效索引的框架

### 智能体知识库构建

非结构化数据处理：

1. 利用大语言模型解析文本并提取结构化信息，利用模版化的提示语，引导模型解析输出固定格式。

2. 非结构化数据存在**噪声**、**数据重复**、**格式不一致**，构建知识库前需要先进行数据清洗。可以利用`Pandas`等工具辅助清洗数据。

   
