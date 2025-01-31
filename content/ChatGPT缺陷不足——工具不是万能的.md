# ChatGPT 的缺陷

## 1. 引言

2022 年末 ChatGPT 的横空出世，在整个自然语言处理乃至人工智能领域都掀起了一 阵海啸。自席卷全球以来便引起各行各业空前的空前热度，数亿用户震惊于 ChatGPT 的强 大智能，感慨机器智能的飞速革新，研究背后的关键技术革新，然而这并不代表 ChatGPT 已然完美无瑕，从业者们则更多在思考当前 ChatGPT 亦或是预训练大模型存在哪些缺陷。 诚然 ChatGPT 的效果一度惊艳到众人，但仍未脱离深度预训练大模型的范式，更多是一 个工程上的突破性进展，因此其缺点也并不难发现。本章主要指出其两大大明显缺点：生 成文本包含事实错误，无法做到实时更新。至于其他一些能否做到类人思考等问题仍存 在争议，本文就不作赘述。

很多用户在使用中时常会碰到诸如事实错误这些问题，对于铺天盖地的炒作，很多 不明就里的外行发出了“就这？”的疑问。实际上，ChatGPT 乃至正在推出的 GPT-4 以及 未来的 GPT-5 等等，是近几十年人工智能研究范式的集大成者；当我们了解了神经网络 的发展历程，了解了大模型的百花齐放，了解了从 GPT，GPT-2，BERT，GPT-3 的日新 月异及其背后的大公司的争相角逐，再看看 ChatGPT 的飞跃式进步，就会觉得这项技术 是人工智能发展史上无可非议的里程碑式成就，这种宏观意义已经远远无需拘泥于是否 答对某个问题了。

本章介绍 ChatGPT 的一些缺点，并非是为了“挑刺”或者对 ChatGPT 被捧得神乎其 神的能力进行抬杠，而是为了让读者对这项技术有更立体的认识，不要被媒体一些流量 标题所“迷惑”，客观认识当前最先进人工智能距离人类智能的差距；同时也是给想要从 事自然语言处理研究的同学指出一些方向，让大家明白，革命尚未成功，同志仍需努力。

## 2. 事实性错误

### 2.1 问题示例

作为当前人工智能产品的巅峰代表，ChatGPT 并不是永远都能那么“睿智”；尽管在 一些高难度问题上展现了超越人类的水准，然而对于一些老幼皆知的简单问题，ChatGPT 也往往会给出一些离谱至极的回答，一本正经的胡说八道，让人汗颜，也难怪大家会发 出“就这”之类的感慨。如下图所示

![avatar](images/ChatGPT-Flaw-chatgpt1.png)

![avatar](images/ChatGPT-Flaw-chatgpt2.png)

这种事实性错误的存在无疑增加了应用落地的风险，尤其对医学，金融等非闲聊场景，轻 则造成经济损失，重则威胁生命安全，因此消除对话模型中的事实错误成为了工业界和 学术界的共同需求。

（扩展：在 NLP 学术界，这种事实性错误一般统称为“幻觉”(Hallucination)，顾名 思义，该术语最早用于图像合成等领域，直到后来描述诸如图像检测时检测到虚假或错 误目标等现象，才沿用至自然语言生成 (NLG) 任务，指模型生成自然流畅，语法正确但 实际上毫无意义且包含虚假信息即事实错误的文本，以假乱真，就像人类产生的幻觉一 样。）

### 2.2 原因分析

对 ChatGPT 之类的语言模型本身而言，在海量的文本数据上训练后，主要学到的知 识包含语言学知识和事实性知识（或称为世界知识）两类，语言学知识是为了能生成语 法正确，自然流畅的文本，大部分经过处理的训练数据都是严格文法正确的，对于大模 型来说学习语言知识并非难事，而事实性知识则主要为实体之间的关联，相对而言复杂 的多，即使对人类而言，也无法学习全部的事实知识。

语言模型中的先验知识都来自于训练语料，用于训练语言模型的大数据语料库在收 集时难免会包含一些错误的信息，这些错误知识都会被学习，存储在模型参数中，相关 研究表明模型生成文本时会优先考虑自身参数化的知识，所以更倾向生成错误内容，而 具体的生成过程仍是一个黑盒模型，很难逐个分析错误来源，也就造成生成任务中大量 事实错误。

相较其他自然语言生成任务，构建 ChatGPT 这种对话模型需要根据用户话语和对话 历史生成流畅连贯，且满足用户对话需求的合理回复。对话模型可以简单用以下因果图 来表示

![avatar](images/ChatGPT-Flaw-causal.png)

生成的回复 𝑌 由对话上下文 𝑋 和语言模型里的先验知识 𝐾 共同决定。在对话模型研究 中，描述这些事实错误有个更通用的术语，“不一致”，大致可分为两种：第一种事实不 一致就是生成回复 𝑌 与世界知识 𝐾 相悖；另一种是对话历史不一致，一般来自于历史信 息 𝑋 的遗忘，与已生成回复相矛盾，以及在人设对话中人设信息会发生变化的现象，在 多轮对话中，这种问题很常见。

### 2.3 解决方法

根据前文分析，针对两种不一致问题需要找到相对应的方法。关于上下文不一致，由 于当前所用的大模型能够接受很长的输入，这个问题造成的影响不大；而另一种事实不 一致则相对很难解决，造成事实错误的首要因素是训练数据，那么构造干净的数据集，进 行去噪显然是一条可行的方法。由于预训练数据多为网上收集的句子，一般都需要提前 过滤，修改语法，指代不明或事实错误，确保语言模型能学习到事实准确的知识，另外也 可以用 Wikipedia 这样的知识库或其他三元组表示的知识来对语言模型的进行知识增强， 这些数据都是公认的包含世界知识的准确数据，对于降低干扰有很大帮助。

数据方法涉及到人工构造，也就意味着成本较高，所以学术上更加关注使用其他方 法，对模型架构、训练或解码推断进行优化，近年来相关研究也是层出不穷，在知识对 话中，模型幻觉最大来源是外部知识选择不正确，因此用更强的检索模型搜索知识，返 回更加有用的知识，也是消除对话回复幻觉的有效途径之一。此前发布的 ChatGPT 并不 具备检索能力，其模型内部的隐式知识已然非常强大，一旦可以进行检索，结合网络上 海量数据，就可以做到实时学习，并更新模型内部过期的知识，这对模型效果的进一步 提升也是相当可观的。

当前对话模型更多关注在开放域场景，合理的回复往往不唯一，这意味着在训练阶 段很难制作标签，同时在推理时模型也比较容易“放飞自我”，生成千奇百怪的回复，其 中难免调用一些错误的知识。针对这种“一对多”的场景，很多研究致力于探索对话模 型的可控生成，通过添加一些控制因素，使生成文本满足一定的约束。提示学习（Prompt Learning）本身就是一种在输入上加入可控因素从而引导正确的生成，这已经成了最新流 行的范式；另外在解码阶段，也可以适当调整策略对生成的多条候选回复重新排序，尽 可能选择出包含目标词汇的回复控制生成内容。


## 3. 实时更新

### 3.1 问题示例

关于 ChatGPT 时效性的问题，也在使用中司空见惯了，去年发布的 ChatGPT 只更 新至 2021 年 9 月之前的信息，虽然 ChatGPT 加了检测机制对回答不了的问题直接“摊 牌”（图三），但在人类“诱导”下，还是会忍不住一本正经的胡说八道（图四），这就涉 及到时效性的问题了。

![avatar](images/ChatGPT-Flaw-chatgpt3.png)

![avatar](images/ChatGPT-Flaw-chatgpt.png)


### 3.2 原因分析

正如前文所分析，语言模型的信息都来自于训练数据，经过训练后这些知识会以参 数化的形式存储在模型中，在往后的使用中都是基于已学习的知识来交互，因此模型本身并不会学习到新知识，在某一轮对话中通过给出适当的实时提示也许会展示出拥有学 习新知识的能力，一旦重新开启对话将历史遗忘，ChatGPT 立即会返回最本真的状态。正 如 ChatGPT 自身所言

![avatar](images/ChatGPT-Flaw-chatgpt4.png)

那么对 ChatGPT 进行实时参数更新呢？这就涉及到另一个计算成本的问题了。深度 学习目前朝着大模型大数据的方向发展已成必然趋势，甚至大模型涌现能力还会激发研 究者们继续“疯狂氪金”堆参数；如今各大公司纷纷推出自己动辄便数千亿级参数量的 模型，所用数据更是无法统计，尽管语言模型并非监督训练，但数据过滤，人工反馈等 阶段还是需要高昂的人力成本；这样超大模型加海量数据的组合，每次训练都需要耗费 数百万美元，在数千台 GPU 来完成，即使后续更新时并不需要从头训练，但花费依旧不 菲，并不是一个可持续性的策略，没点财力的公司，是根本养不起的。

### 3.3 解决方法

针对 ChatGPT 这种超大模型的实时更新问题。首先最直接的方法就是降低微调更新数据时的成本，由于预训练过程中已经学到大 量语言学知识和其他先验世界知识，所以我们可以使用一些参数高效的方法只更新部分 参数来学习新知识，此前研究表明，文本的语言学知识多存储在模型的低层网络，所以 在微调更新时可以冻结中低层的模型参数，从而加速学习。

ChatGPT 不能实时更新，是因为当前的深度学习范式在结束训练后就是一个静态的 模型了，而人类则是终生都在动态学习，所以我们也希望能赋予它“活到老，学到老”的 觉悟。那么我们是否可以让 ChatGPT 学习使用各种 API 的能力，自行处理数据然后自我 迭代更新，实现闭环式学习达到时效性的目的呢？微软所推出的 new bing 搜索引擎，以 对话形式精准查询用户的需求，实现了对话模型与海量网络信息的联动；ChatGPT 并不 是搜索引擎，但可以和搜索引擎结合对查询做优化，或许会颠覆整个互联网的搜索模式， 而 ChatGPT 本身也就具备了访问这些最新数据的能力，这也许是未来大模型的新赛道。

## 4. 总结

21 世纪一定是属于人工智能的时代，如果说深度学习引起学术界 AI 研究的热度， ChatGPT 的问世则无疑推向第一个小高潮，但简单了解过其原理后就会发现，这项技术 并没有想象中人类智慧那样复杂，更遑论毁灭人类的能力了，我们应当理性看待这一技 术，不需要过度神化吹捧，也没必要过分苛责，在真正实现通用人工智能的路上仍然任 重而道远。
