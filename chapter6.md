# 第6章：AI协作与生成式创作
*与机器共同创作的艺术*

> "AI不是要取代作家，而是要解放想象力的边界。" — Robin Sloan

在传统创作中，作者是唯一的造物主。而在AI时代，创作变成了一场对话——作者提供愿景和约束，AI带来意外和可能性。本章将探讨如何与AI系统协作，创造出既保持创作者意图、又充满生成式惊喜的非传统叙事作品。

你将学习提示工程的核心技巧，掌握程序化内容生成的设计模式，并通过实际案例理解AI如何扩展叙事的可能性空间。无论是创建永不重复的故事分支，还是生成响应读者输入的动态内容，AI都将成为你最有力的创作伙伴。

## 6.1 提示工程：引导AI叙事

### 6.1.1 理解大语言模型的叙事能力

大语言模型（LLM）的核心是一个概率预测器，但当规模足够大时，它展现出了令人惊讶的创造性涌现。理解这种能力的本质，是有效引导AI叙事的基础。

#### Token预测与创造性涌现

LLM通过预测下一个token来生成文本。这个看似简单的机制，在数万亿参数的规模下，产生了复杂的叙事能力：

```python
# 温度参数对生成的影响示例
prompt = "在迷雾笼罩的城市里，侦探发现了"

# 低温度（0.3）：更可预测，更连贯
# 可能输出："一具尸体"、"重要线索"、"血迹"

# 高温度（1.2）：更有创意，可能失控
# 可能输出："会说话的猫"、"时间裂缝"、"自己的墓碑"
```

**深入理解温度参数的数学本质**：

温度参数T直接影响softmax函数的概率分布：
```
P(token_i) = exp(logit_i/T) / Σ exp(logit_j/T)
```

- T → 0：分布变得尖锐，最高概率token几乎总被选中
- T = 1：原始概率分布
- T → ∞：分布变得平坦，所有token概率趋同

**采样策略对叙事的影响**：

```python
# Top-k采样：限制候选token数量
def top_k_sampling(logits, k=50):
    """只从概率最高的k个token中采样"""
    # 适合：需要连贯性的主线情节
    # 风险：可能错过创意选项
    
# Top-p (nucleus)采样：动态候选集
def nucleus_sampling(logits, p=0.95):
    """选择累积概率达到p的最小token集"""
    # 适合：平衡创意与连贯性
    # 优势：自适应不同上下文的确定性
    
# 混合策略示例
def adaptive_sampling(context, logits):
    if context.is_dialogue:
        return nucleus_sampling(logits, p=0.9)
    elif context.is_action:
        return top_k_sampling(logits, k=30)
    elif context.is_description:
        return temperature_sampling(logits, T=1.1)
```

**涌现能力的层次**：

1. **语法涌现**（~1B参数）：基本的语法正确性
2. **语义涌现**（~10B参数）：概念理解和关联
3. **逻辑涌现**（~100B参数）：因果推理和情节连贯性
4. **创造涌现**（~1T参数）：新颖概念组合和隐喻生成

关键洞察：
- **概率分布塑造叙事风格**：调整采样参数可以在"安全但无聊"和"有趣但混乱"之间找到平衡
- **上下文是一切**：模型通过上下文理解角色、场景和叙事逻辑
- **涌现不等于理解**：AI可以生成连贯的情节，但并不"理解"故事的意义
- **规模定律的叙事含义**：更大的模型不仅是"更好"，而是解锁了质的飞跃

#### 上下文窗口与长程依赖

现代LLM的上下文窗口已经扩展到10万+tokens，但有效管理这个窗口仍是挑战：

```yaml
上下文层次结构：
  核心设定（始终保留）：
    - 世界观基础规则
    - 主要角色定义
    - 当前章节目标
  
  动态上下文（按需加载）：
    - 最近对话历史
    - 相关场景描述
    - 临时角色信息
  
  可压缩信息（摘要形式）：
    - 早期情节发展
    - 次要角色互动
    - 环境细节
```

**注意力机制的叙事特性**：

```python
class NarrativeAttentionPatterns:
    """叙事中的注意力模式分析"""
    
    def analyze_attention_decay(self, distance):
        """注意力随距离衰减的模式"""
        # 研究发现：叙事元素的注意力衰减遵循特定模式
        
        if distance < 100:  # 近期上下文
            return 1.0  # 完全注意力
        elif distance < 1000:  # 中期记忆
            return 0.8 * math.exp(-distance/500)  # 指数衰减
        else:  # 长期记忆
            return 0.2 * (1000/distance)  # 幂律衰减
    
    def critical_memory_points(self):
        """识别必须保留的叙事锚点"""
        return {
            "character_introduction": 0.95,  # 角色首次出现
            "plot_twist": 0.90,             # 情节转折点
            "worldbuilding_rule": 0.85,     # 世界设定规则
            "unresolved_conflict": 0.80,    # 未解决的冲突
            "foreshadowing": 0.75           # 伏笔元素
        }
```

**长文本的分块策略**：

```python
def chunk_narrative_intelligently(text, max_chunk_size=2000):
    """智能分割长篇叙事，保持语义完整性"""
    
    chunks = []
    current_chunk = []
    current_size = 0
    
    # 场景边界检测
    scene_markers = [
        "\n\n",           # 段落分隔
        "* * *",          # 场景分隔符
        "第.*章",         # 章节标记
        "与此同时",       # 时空转换
        "多年后",         # 时间跳跃
    ]
    
    # 保持对话完整性
    in_dialogue = False
    dialogue_speaker = None
    
    for paragraph in text.split('\n\n'):
        # 检测对话状态
        if '"' in paragraph or '"' in paragraph:
            in_dialogue = True
            # 提取说话人
            speaker = extract_speaker(paragraph)
            if speaker != dialogue_speaker:
                # 说话人变化，可以分割
                if current_size > max_chunk_size * 0.8:
                    chunks.append(merge_chunk(current_chunk))
                    current_chunk = []
                    current_size = 0
        
        current_chunk.append(paragraph)
        current_size += len(paragraph)
        
        # 智能分割决策
        if should_split(current_size, max_chunk_size, paragraph, scene_markers):
            chunks.append(merge_chunk(current_chunk))
            current_chunk = []
            current_size = 0
    
    return chunks
```

**记忆压缩与检索增强生成(RAG)**：

```python
class NarrativeMemoryBank:
    """叙事记忆库，支持压缩存储和语义检索"""
    
    def __init__(self):
        self.memories = {}
        self.embeddings = {}
        self.compression_ratio = 0.3
    
    def store_narrative_event(self, event, importance_score):
        """存储叙事事件，根据重要性决定压缩程度"""
        
        if importance_score > 0.8:
            # 高重要性：完整保存
            compressed = event
            self.memories[event.id] = {
                'full_text': event.text,
                'summary': None,
                'importance': importance_score,
                'timestamp': event.timestamp
            }
        else:
            # 低重要性：压缩存储
            summary = self.compress_event(event)
            self.memories[event.id] = {
                'full_text': None,
                'summary': summary,
                'importance': importance_score,
                'timestamp': event.timestamp
            }
        
        # 计算并存储语义嵌入
        self.embeddings[event.id] = self.compute_embedding(event)
    
    def retrieve_relevant_context(self, query, top_k=5):
        """基于语义相似度检索相关记忆"""
        query_embedding = self.compute_embedding(query)
        
        similarities = []
        for memory_id, memory_embedding in self.embeddings.items():
            similarity = cosine_similarity(query_embedding, memory_embedding)
            similarities.append((memory_id, similarity))
        
        # 返回最相关的记忆
        top_memories = sorted(similarities, key=lambda x: x[1], reverse=True)[:top_k]
        
        return [self.expand_memory(memory_id) for memory_id, _ in top_memories]
```

#### 温度参数与创造性控制

温度不仅影响随机性，更是叙事节奏的调节器：

```python
def adaptive_temperature(narrative_context):
    """根据叙事需求动态调整温度"""
    if narrative_context.is_action_scene:
        return 0.7  # 动作场景需要连贯性
    elif narrative_context.is_dialogue:
        return 0.9  # 对话可以更自然多样
    elif narrative_context.is_description:
        return 1.1  # 描述可以更有诗意
    elif narrative_context.is_plot_critical:
        return 0.5  # 关键情节点需要精确控制
```

**高级温度控制策略**：

```python
class AdvancedTemperatureController:
    """基于多维度因素的温度控制系统"""
    
    def __init__(self):
        self.base_temp = 0.8
        self.history_window = 100  # 考虑最近100个生成
        self.repetition_penalty = 0.1
        
    def calculate_dynamic_temperature(self, context):
        """多因素动态温度计算"""
        
        # 基础温度
        temp = self.base_temp
        
        # 1. 重复度惩罚
        repetition_score = self.measure_repetition(context.recent_outputs)
        temp += repetition_score * self.repetition_penalty
        
        # 2. 情绪强度调整
        emotional_intensity = context.current_emotion_level
        if emotional_intensity > 0.7:
            temp += 0.2  # 高情绪时增加变化性
        
        # 3. 叙事张力曲线
        tension_level = context.narrative_tension
        temp_adjustment = self.tension_to_temp_curve(tension_level)
        temp *= temp_adjustment
        
        # 4. 读者参与度响应
        if context.reader_engagement < 0.5:
            temp += 0.15  # 低参与度时增加惊喜元素
        
        # 5. 类型特定调整
        genre_modifier = {
            'mystery': 0.8,      # 推理需要逻辑
            'poetry': 1.3,       # 诗歌需要创意
            'technical': 0.6,    # 技术文档需要准确
            'surreal': 1.5       # 超现实可以疯狂
        }.get(context.genre, 1.0)
        
        return max(0.1, min(2.0, temp * genre_modifier))
    
    def tension_to_temp_curve(self, tension):
        """叙事张力到温度的映射曲线"""
        # 使用S型曲线，在高潮时稍微降低温度保证连贯性
        import math
        return 1.2 - 0.4 / (1 + math.exp(-10 * (tension - 0.7)))
```

**创造性与连贯性的平衡艺术**：

```python
class CreativityCoherenceBalancer:
    """平衡创造性和连贯性的高级系统"""
    
    def __init__(self):
        self.coherence_tracker = CoherenceTracker()
        self.creativity_evaluator = CreativityEvaluator()
        
    def generate_with_balance(self, prompt, target_creativity=0.7):
        """生成满足创造性目标的连贯文本"""
        
        candidates = []
        temps = [0.5, 0.7, 0.9, 1.1, 1.3]
        
        for temp in temps:
            # 生成候选
            output = self.generate(prompt, temperature=temp)
            
            # 评估指标
            coherence = self.coherence_tracker.score(output, prompt)
            creativity = self.creativity_evaluator.score(output)
            
            # 计算综合分数
            balance_score = self.calculate_balance_score(
                coherence, creativity, target_creativity
            )
            
            candidates.append({
                'text': output,
                'temp': temp,
                'coherence': coherence,
                'creativity': creativity,
                'balance': balance_score
            })
        
        # 选择最佳候选
        best = max(candidates, key=lambda x: x['balance'])
        
        # 学习最佳温度模式
        self.learn_optimal_pattern(prompt, best)
        
        return best['text']
    
    def calculate_balance_score(self, coherence, creativity, target_creativity):
        """计算平衡分数，考虑目标创造性水平"""
        
        # 连贯性是基础要求（最低0.6）
        if coherence < 0.6:
            return coherence * 0.5
        
        # 创造性接近目标值时得分最高
        creativity_distance = abs(creativity - target_creativity)
        creativity_score = 1 - creativity_distance
        
        # 综合评分
        return coherence * 0.4 + creativity_score * 0.6
```

**温度调度器：长篇叙事的节奏控制**：

```python
class NarrativeTemperatureScheduler:
    """根据故事进展调度温度参数"""
    
    def __init__(self, story_structure):
        self.structure = story_structure
        self.current_position = 0
        
    def get_scheduled_temperature(self):
        """根据三幕结构返回建议温度"""
        
        position_ratio = self.current_position / self.structure.total_length
        
        if position_ratio < 0.25:  # 第一幕：建立
            # 开篇需要吸引力，但不能太疯狂
            return 0.8 + 0.1 * math.sin(position_ratio * 4 * math.pi)
            
        elif position_ratio < 0.5:  # 第二幕前半：发展
            # 逐渐增加复杂性和意外
            return 0.85 + 0.15 * (position_ratio - 0.25) * 4
            
        elif position_ratio < 0.75:  # 第二幕后半：冲突
            # 高潮前的紧张积累
            return 1.0 - 0.2 * math.cos((position_ratio - 0.5) * 4 * math.pi)
            
        elif position_ratio < 0.9:  # 第三幕：高潮
            # 关键时刻需要控制
            return 0.7
            
        else:  # 结尾
            # 收束时降低随机性
            return 0.6 - 0.1 * (position_ratio - 0.9) * 10
```

### 6.1.2 提示设计模式

有效的提示设计是一门艺术，也是一门科学。以下是经过实践验证的核心模式：

#### 角色定义与世界设定

**模式1：角色卡片系统**

```markdown
## 角色定义卡片

**名称**：艾琳娜·星语者
**核心特征**：
- 说话总是用天文学比喻
- 相信命运写在星星里
- 害怕封闭空间（童年创伤）

**语言风格**：
- 示例1："你的谎言像超新星一样明显——短暂、剧烈、注定毁灭。"
- 示例2："我们之间的距离不是光年能衡量的。"

**行为模式**：
- 紧张时会数星座名称
- 重要决定前必须观察夜空
- 从不直接说"是"或"不是"
```

**模式2：世界规则引擎**

```python
world_rules = {
    "physics": {
        "gravity": "反向作用于情绪激动者",
        "time": "在记忆密集区域会变慢",
        "light": "可以储存在特殊容器中"
    },
    "society": {
        "currency": "交易使用'记忆片段'",
        "government": "由AI议会和人类长老共治",
        "taboo": "提及'地面世界'会被放逐"
    },
    "magic_system": {
        "source": "情绪能量转化",
        "limitation": "每次使用会遗忘一个名字",
        "manifestation": "通过专注的手势和吟唱"
    }
}
```

#### 少样本学习在叙事中的应用

少样本学习让AI快速掌握特定的叙事风格：

```markdown
## 叙事风格示例

### 示例1（忧郁诗意）：
"雨水沿着玻璃流下，像这座城市的眼泪。每一滴都带着一个未完成的故事，汇入下水道的黑暗交响曲。"

### 示例2（忧郁诗意）：
"她的笑容是一朵在废墟中绽放的花，美丽得让人心碎，因为你知道它注定凋零。"

### 示例3（忧郁诗意）：
"时钟的指针像执着的恋人，永远追逐却永不相遇，在表盘上画着命运的圆圈。"

### 现在继续这种风格：
"他打开门，看到..."
```

**高级少样本学习技巧**：

```python
class AdvancedFewShotLearning:
    """用于叙事风格学习的高级少样本系统"""
    
    def extract_style_features(self, examples):
        """从示例中提取风格特征"""
        features = {
            'vocabulary': self.analyze_word_choice(examples),
            'sentence_structure': self.analyze_syntax(examples),
            'imagery_patterns': self.extract_metaphors(examples),
            'emotional_tone': self.measure_sentiment(examples),
            'rhythm': self.analyze_prosody(examples)
        }
        return features
    
    def generate_style_prompt(self, style_name, examples, task):
        """生成包含风格指导的提示"""
        
        # 提取风格DNA
        style_dna = self.extract_style_features(examples)
        
        prompt = f"""
        ## 风格指南：{style_name}
        
        词汇特征：{style_dna['vocabulary']['key_words']}
        句式偏好：{style_dna['sentence_structure']['patterns']}
        意象类型：{style_dna['imagery_patterns']['dominant_themes']}
        情感基调：{style_dna['emotional_tone']['spectrum']}
        节奏模式：{style_dna['rhythm']['cadence']}
        
        ## 示例展示
        {self.format_examples(examples)}
        
        ## 任务
        {task}
        
        请严格遵循以上风格特征...
        """
        
        return prompt
```

**对比学习增强风格区分**：

```python
def contrastive_style_learning(target_style, contrast_styles):
    """通过对比学习强化风格特征"""
    
    prompt = f"""
    ## 目标风格：{target_style['name']}
    
    ### 正例（应该这样写）：
    {format_examples(target_style['examples'])}
    
    ### 反例（避免这样写）：
    """
    
    for style in contrast_styles:
        prompt += f"""
    
    ❌ {style['name']}风格：
    {style['example']}
    原因：{style['why_different']}
    """
    
    prompt += """
    
    ### 风格检查清单：
    ✓ 是否使用了目标风格的核心意象？
    ✓ 句式节奏是否匹配？
    ✓ 情感色彩是否一致？
    ✗ 是否误用了其他风格的特征？
    
    现在请用目标风格写作...
    """
    
    return prompt
```

**渐进式风格迁移**：

```python
class ProgressiveStyleTransfer:
    """逐步引导AI掌握复杂风格"""
    
    def __init__(self):
        self.difficulty_levels = ['basic', 'intermediate', 'advanced', 'master']
        
    def create_learning_sequence(self, target_style):
        """创建渐进学习序列"""
        
        sequence = []
        
        # Level 1: 基础词汇和句式
        sequence.append({
            'level': 'basic',
            'focus': '模仿基本词汇选择和句子长度',
            'examples': self.filter_simple_examples(target_style),
            'task': '写一个简单描述'
        })
        
        # Level 2: 修辞手法
        sequence.append({
            'level': 'intermediate', 
            'focus': '学习比喻和意象运用',
            'examples': self.filter_metaphor_rich_examples(target_style),
            'task': '创作包含核心意象的段落'
        })
        
        # Level 3: 情感层次
        sequence.append({
            'level': 'advanced',
            'focus': '掌握情感转折和层次',
            'examples': self.filter_emotional_complexity_examples(target_style),
            'task': '写一个情感渐变的场景'
        })
        
        # Level 4: 完整融合
        sequence.append({
            'level': 'master',
            'focus': '自如运用所有风格元素',
            'examples': self.select_best_examples(target_style),
            'task': '创作展现风格精髓的完整片段'
        })
        
        return sequence
```

#### 链式思考构建复杂情节

链式思考（Chain-of-Thought）不仅用于推理，也是构建多层次情节的利器：

```markdown
## 情节发展链

**当前状况**：主角发现了一封神秘信件

**推理链**：
1. 信件内容暗示了什么？
   → 提到了"月圆之夜"和"老地方"
   
2. 这对主角意味着什么？
   → 勾起了被压抑的记忆，关于失踪的妹妹
   
3. 主角会如何反应？
   → 内心冲突：理智告诉他这是陷阱，情感驱使他必须去
   
4. 这如何推动情节？
   → 决定赴约，但会做准备，可能通知可信任的朋友
   
5. 潜在的转折点？
   → "老地方"可能已经改变，或者写信人并非他想象的人

**生成下一段**：基于以上推理...
```

**多维度链式推理系统**：

```python
class MultidimensionalChainOfThought:
    """多维度链式思考系统，构建立体情节"""
    
    def __init__(self):
        self.dimensions = {
            'plot': PlotChain(),           # 情节逻辑链
            'character': CharacterChain(),   # 角色心理链
            'world': WorldChain(),          # 世界规则链
            'theme': ThemeChain(),          # 主题深化链
            'reader': ReaderChain()         # 读者体验链
        }
    
    def generate_complex_development(self, current_state):
        """生成多维度协调的情节发展"""
        
        # 1. 各维度独立推理
        chains = {}
        for dim_name, dim_chain in self.dimensions.items():
            chains[dim_name] = dim_chain.reason(current_state)
        
        # 2. 检测冲突和协同
        conflicts = self.detect_conflicts(chains)
        synergies = self.find_synergies(chains)
        
        # 3. 解决冲突，强化协同
        resolved_chains = self.resolve_and_enhance(chains, conflicts, synergies)
        
        # 4. 生成综合推理路径
        return self.synthesize_chains(resolved_chains)
    
    def detect_conflicts(self, chains):
        """检测不同维度间的逻辑冲突"""
        conflicts = []
        
        # 示例：角色动机vs世界规则
        if chains['character']['action'] == 'use_magic' and \
           chains['world']['magic_availability'] == 'forbidden':
            conflicts.append({
                'type': 'character_world_conflict',
                'description': '角色想使用被禁止的魔法',
                'severity': 'high',
                'resolution_options': [
                    '角色违反规则（推动冲突）',
                    '角色寻找替代方案（展示智慧）',
                    '揭示规则的例外（世界观深化）'
                ]
            })
        
        return conflicts
```

**情节分支的概率树**：

```python
class PlotProbabilityTree:
    """基于概率的情节分支生成器"""
    
    def __init__(self, story_context):
        self.context = story_context
        self.tree = {}
        
    def generate_branches(self, decision_point, depth=3):
        """生成决策点的概率分支树"""
        
        if depth == 0:
            return None
            
        branches = []
        
        # 生成可能的选择
        choices = self.generate_plausible_choices(decision_point)
        
        for choice in choices:
            # 计算选择的合理性概率
            probability = self.calculate_choice_probability(choice)
            
            # 推演选择的后果
            consequences = self.simulate_consequences(choice)
            
            # 递归生成子分支
            sub_branches = self.generate_branches(consequences, depth-1)
            
            branches.append({
                'choice': choice,
                'probability': probability,
                'consequences': consequences,
                'sub_branches': sub_branches,
                'narrative_value': self.evaluate_narrative_value(choice, consequences)
            })
        
        return branches
    
    def calculate_choice_probability(self, choice):
        """基于角色性格、情境压力等计算选择概率"""
        
        factors = {
            'character_alignment': self.character_choice_fit(choice),
            'situational_pressure': self.context_pressure_factor(choice),
            'narrative_momentum': self.story_flow_factor(choice),
            'reader_expectation': self.expectation_subversion_value(choice)
        }
        
        # 加权计算综合概率
        weights = {'character': 0.4, 'situation': 0.3, 'narrative': 0.2, 'reader': 0.1}
        
        return sum(factors[k] * weights[k.split('_')[0]] for k in factors)
```

**深度情节推理模板**：

```python
def deep_plot_reasoning_template(situation):
    """深度情节推理的结构化模板"""
    
    template = f"""
    ## 情境分析
    
    ### 表层情况
    {situation['surface_description']}
    
    ### 深层含义
    - 象征层面：{analyze_symbolism(situation)}
    - 心理层面：{analyze_psychology(situation)}
    - 社会层面：{analyze_social_context(situation)}
    
    ## 因果推演
    
    ### 直接原因
    {trace_immediate_causes(situation)}
    
    ### 根本原因
    {trace_root_causes(situation)}
    
    ### 潜在后果
    - 短期（下一场景）：{predict_short_term(situation)}
    - 中期（本章结束）：{predict_medium_term(situation)}
    - 长期（故事主线）：{predict_long_term(situation)}
    
    ## 叙事机会
    
    ### 角色发展
    - 可以展现的性格特质：{character_development_opportunities(situation)}
    - 关系动态的变化：{relationship_dynamics(situation)}
    
    ### 主题深化
    - 可以探讨的主题：{thematic_opportunities(situation)}
    - 与核心主题的联系：{connect_to_central_theme(situation)}
    
    ### 读者参与
    - 悬念设置点：{suspense_points(situation)}
    - 情感共鸣点：{emotional_resonance_points(situation)}
    
    ## 最优路径推荐
    
    基于以上分析，推荐的情节发展是：
    {recommend_optimal_path(situation)}
    
    理由：
    1. {reason_1}
    2. {reason_2}
    3. {reason_3}
    """
    
    return template
```

### 6.1.3 交互式提示系统

静态提示只是开始，真正的力量来自动态、响应式的提示系统：

#### 动态提示生成

```python
class DynamicPromptSystem:
    def __init__(self):
        self.story_state = {}
        self.reader_profile = {}
        self.narrative_arc = {}
    
    def generate_prompt(self, reader_input):
        """根据故事状态和读者输入生成提示"""
        
        # 分析读者输入的意图
        intent = self.analyze_intent(reader_input)
        
        # 检查故事一致性
        consistency_check = self.verify_consistency(intent)
        
        # 构建分层提示
        prompt = f"""
        ## 故事状态
        {self.format_story_state()}
        
        ## 读者行动
        {reader_input}
        
        ## 叙事约束
        - 保持人物性格一致性
        - 遵循已建立的世界规则
        - 当前章节主题：{self.narrative_arc['current_theme']}
        
        ## 生成指导
        - 长度：{self.calculate_response_length()}
        - 情绪基调：{self.narrative_arc['emotional_tone']}
        - 包含元素：{self.get_required_elements()}
        
        请继续故事...
        """
        
        return prompt
```

#### 读者输入的整合

将读者输入无缝整合到叙事中，需要智能的解析和适配：

```python
def integrate_reader_action(action, context):
    """智能整合读者动作到故事中"""
    
    # 解析动作类型
    action_type = classify_action(action)
    
    if action_type == "dialogue":
        # 转换为角色对话
        return f'你说道："{action}"'
    
    elif action_type == "movement":
        # 添加环境描述
        location = context.current_location
        return f"你{action}，{describe_environment_reaction(location)}"
    
    elif action_type == "investigation":
        # 触发发现机制
        discovery = generate_discovery(action, context)
        return f"你仔细{action}，{discovery}"
    
    elif action_type == "combat":
        # 进入动作序列
        return initiate_combat_sequence(action, context)
    
    else:
        # 优雅处理意外输入
        return adapt_unexpected_input(action, context)
```

#### 上下文管理策略

有效的上下文管理是长篇交互叙事的关键：

```python
class ContextManager:
    def __init__(self, max_tokens=8000):
        self.max_tokens = max_tokens
        self.context_layers = {
            'core': [],      # 永不删除
            'recent': [],    # FIFO队列
            'relevant': [],  # 动态检索
            'summary': []    # 压缩的历史
        }
    
    def optimize_context(self, new_input):
        """优化上下文以适应token限制"""
        
        # 1. 计算当前使用量
        current_tokens = self.count_tokens()
        
        # 2. 如果超出限制，执行压缩
        if current_tokens > self.max_tokens * 0.8:
            # 摘要旧对话
            self.summarize_old_conversations()
            
            # 移除低相关性内容
            self.prune_irrelevant_content()
            
            # 合并相似信息
            self.merge_similar_information()
        
        # 3. 智能检索相关历史
        relevant_history = self.retrieve_relevant_context(new_input)
        
        # 4. 构建优化后的上下文
        return self.build_optimized_context(relevant_history)
    
    def summarize_old_conversations(self):
        """使用AI摘要旧对话"""
        old_convos = self.context_layers['recent'][:10]
        summary_prompt = f"""
        请将以下对话摘要为关键信息点：
        {old_convos}
        
        保留：角色发展、重要事件、未解决的线索
        省略：重复信息、环境细节、已解决的问题
        """
        # 调用AI生成摘要...
        summary = self.call_ai_for_summary(summary_prompt)
        self.context_layers['summary'].append(summary)
        
        # 清理已摘要的内容
        self.context_layers['recent'] = self.context_layers['recent'][10:]

def adaptive_prompt_strategies(narrative_phase):
    """根据叙事阶段调整提示策略"""
    strategies = {
        "exposition": {
            "focus": "世界构建和角色介绍",
            "techniques": ["感官细节", "环境描写", "背景暗示"],
            "avoid": ["过快的节奏", "复杂冲突", "深层秘密揭露"]
        },
        "rising_action": {
            "focus": "张力积累和线索铺设",
            "techniques": ["悬念制造", "角色冲突", "障碍设置"],
            "avoid": ["过早解决", "偏离主线", "节奏拖沓"]
        },
        "climax": {
            "focus": "冲突爆发和真相揭示",
            "techniques": ["快节奏", "高风险选择", "情感爆发"],
            "avoid": ["新线索引入", "冗长描述", "突兀转折"]
        },
        "resolution": {
            "focus": "线索收束和情感共鸣",
            "techniques": ["回应伏笔", "角色成长", "主题升华"],
            "avoid": ["新冲突", "开放过多", "仓促结束"]
        }
    }
    return strategies.get(narrative_phase, strategies["exposition"])
```

#### 多模态提示融合

将文本提示与其他模态信息结合，创造更丰富的叙事体验：

```python
class MultiModalPromptFusion:
    def __init__(self):
        self.modalities = {
            'text': {'weight': 0.5},
            'emotion': {'weight': 0.2},
            'visual': {'weight': 0.2},
            'audio': {'weight': 0.1}
        }
    
    def create_fusion_prompt(self, inputs):
        """融合多种输入模态生成统一提示"""
        
        # 文本层：核心叙事内容
        text_prompt = inputs.get('text', '')
        
        # 情感层：引导叙事基调
        emotion_layer = self.encode_emotion(inputs.get('emotion', 'neutral'))
        
        # 视觉层：场景和氛围描述
        visual_cues = self.process_visual_input(inputs.get('visual', {}))
        
        # 音频层：节奏和张力暗示
        audio_hints = self.extract_audio_patterns(inputs.get('audio', {}))
        
        # 智能融合
        fusion_prompt = f"""
        ## 多维度叙事指引
        
        ### 核心叙述
        {text_prompt}
        
        ### 情感基调
        当前情感状态：{emotion_layer['current']}
        目标情感弧线：{emotion_layer['target']}
        情感转换速度：{emotion_layer['transition_speed']}
        
        ### 视觉氛围
        场景主色调：{visual_cues['dominant_colors']}
        光线条件：{visual_cues['lighting']}
        空间感受：{visual_cues['spatial_feeling']}
        关键视觉元素：{', '.join(visual_cues['key_elements'])}
        
        ### 叙事节奏
        基础节拍：{audio_hints['tempo']}
        张力曲线：{audio_hints['tension_curve']}
        停顿点：{audio_hints['pause_points']}
        
        请根据以上多维度信息，生成符合整体氛围的叙事内容...
        """
        
        return fusion_prompt
    
    def encode_emotion(self, emotion_input):
        """将情感输入编码为叙事指导"""
        emotion_map = {
            'joy': {'energy': 'high', 'tone': 'bright', 'pace': 'lively'},
            'sadness': {'energy': 'low', 'tone': 'muted', 'pace': 'slow'},
            'fear': {'energy': 'tense', 'tone': 'sharp', 'pace': 'irregular'},
            'anger': {'energy': 'explosive', 'tone': 'harsh', 'pace': 'rapid'},
            'mystery': {'energy': 'controlled', 'tone': 'ambiguous', 'pace': 'measured'}
        }
        
        if isinstance(emotion_input, str):
            return emotion_map.get(emotion_input, emotion_map['mystery'])
        elif isinstance(emotion_input, dict):
            # 支持复合情感
            return self.blend_emotions(emotion_input)
```

#### 实时反馈循环

建立读者反馈与AI生成之间的动态循环：

```python
class RealtimeFeedbackLoop:
    def __init__(self):
        self.feedback_history = []
        self.adjustment_threshold = 0.7
        self.learning_rate = 0.1
        
    def process_reader_feedback(self, generated_content, reader_response):
        """处理读者反馈并调整生成策略"""
        
        # 分析读者反应
        feedback_analysis = {
            'engagement_level': self.measure_engagement(reader_response),
            'emotional_response': self.detect_emotion(reader_response),
            'comprehension': self.assess_comprehension(reader_response),
            'satisfaction': self.gauge_satisfaction(reader_response)
        }
        
        # 识别问题区域
        issues = self.identify_issues(feedback_analysis)
        
        # 生成改进建议
        improvements = self.generate_improvements(issues)
        
        # 更新提示策略
        updated_strategy = self.update_prompt_strategy(improvements)
        
        return updated_strategy
    
    def measure_engagement(self, response):
        """测量读者参与度"""
        indicators = {
            'response_length': len(response.get('text', '')),
            'response_time': response.get('time_taken', 0),
            'action_complexity': self.analyze_action_complexity(response),
            'question_count': response.get('questions_asked', 0)
        }
        
        # 归一化并加权计算
        engagement_score = (
            indicators['response_length'] / 100 * 0.3 +
            min(indicators['response_time'] / 60, 1) * 0.2 +
            indicators['action_complexity'] * 0.3 +
            indicators['question_count'] / 3 * 0.2
        )
        
        return min(engagement_score, 1.0)
    
    def adaptive_difficulty_adjustment(self, reader_profile):
        """根据读者能力动态调整叙事复杂度"""
        
        complexity_factors = {
            'vocabulary': {
                'current': reader_profile.get('vocab_level', 0.5),
                'target': self.calculate_optimal_vocab_level(reader_profile),
                'adjustment_rate': 0.05
            },
            'plot_complexity': {
                'current': reader_profile.get('plot_tracking', 0.5),
                'target': self.calculate_optimal_plot_complexity(reader_profile),
                'adjustment_rate': 0.03
            },
            'symbolic_depth': {
                'current': reader_profile.get('symbolism_appreciation', 0.3),
                'target': self.calculate_optimal_symbolism(reader_profile),
                'adjustment_rate': 0.02
            }
        }
        
        # 渐进式调整
        for factor, params in complexity_factors.items():
            if abs(params['current'] - params['target']) > 0.1:
                adjustment = (params['target'] - params['current']) * params['adjustment_rate']
                params['current'] += adjustment
                
        return complexity_factors
```

#### 协作式世界构建

让AI和读者共同构建故事世界：

```python
class CollaborativeWorldBuilding:
    def __init__(self):
        self.world_state = {}
        self.contribution_history = []
        self.consistency_checker = ConsistencyEngine()
        
    def integrate_reader_contribution(self, contribution, context):
        """整合读者贡献到世界设定中"""
        
        # 验证贡献的一致性
        validation = self.consistency_checker.validate(contribution, self.world_state)
        
        if validation['is_consistent']:
            # 直接整合
            self.world_state.update(contribution)
            return self.generate_acknowledgment(contribution)
            
        elif validation['can_be_adapted']:
            # 适配后整合
            adapted = self.adapt_contribution(contribution, validation['conflicts'])
            self.world_state.update(adapted)
            return self.generate_adapted_acknowledgment(contribution, adapted)
            
        else:
            # 创造性地处理冲突
            resolution = self.creative_conflict_resolution(
                contribution, 
                validation['conflicts']
            )
            return resolution
    
    def generate_world_building_prompt(self, focus_area):
        """生成引导读者参与世界构建的提示"""
        
        prompts = {
            'geography': """
            你发现了一张古老地图的碎片。上面标注着一个从未被记录的地区。
            这个地方有什么独特之处？它如何连接到已知世界？
            
            已知世界要素：
            {known_geography}
            
            请描述你的发现...
            """,
            
            'culture': """
            你遇到了一个神秘部落的使者。他们的习俗似乎很独特。
            他们如何问候？有什么禁忌？他们崇拜什么？
            
            已知文化体系：
            {known_cultures}
            
            请描述这个新文化...
            """,
            
            'magic_system': """
            你偶然发现了一种新的魔法形式。它的原理是什么？
            有什么限制？需要什么代价？
            
            已知魔法规则：
            {known_magic}
            
            请解释这种新魔法...
            """,
            
            'history': """
            在古代遗迹中，你发现了一段被遗忘的历史。
            这段历史如何改变了对当前世界的理解？
            
            已知历史事件：
            {known_history}
            
            请讲述这段历史...
            """
        }
        
        chosen_prompt = prompts.get(focus_area, prompts['geography'])
        return chosen_prompt.format(**self.get_relevant_world_info(focus_area))
```

#### 智能错误恢复

当AI生成出现问题时的优雅恢复机制：

```python
class IntelligentErrorRecovery:
    def __init__(self):
        self.error_patterns = {}
        self.recovery_strategies = {}
        self.learning_enabled = True
        
    def detect_and_recover(self, generated_content, context):
        """检测生成内容中的错误并恢复"""
        
        errors = self.detect_errors(generated_content, context)
        
        if not errors:
            return generated_content
            
        # 按严重程度排序
        errors.sort(key=lambda x: x['severity'], reverse=True)
        
        # 应用恢复策略
        recovered_content = generated_content
        for error in errors:
            strategy = self.select_recovery_strategy(error)
            recovered_content = self.apply_recovery(
                recovered_content, 
                error, 
                strategy,
                context
            )
            
        # 验证恢复效果
        if self.validate_recovery(recovered_content, context):
            return recovered_content
        else:
            # 降级到安全模式
            return self.safe_mode_generation(context)
    
    def detect_errors(self, content, context):
        """检测各类叙事错误"""
        
        errors = []
        
        # 1. 角色一致性错误
        character_errors = self.check_character_consistency(content, context)
        errors.extend(character_errors)
        
        # 2. 世界规则违反
        world_errors = self.check_world_rules(content, context)
        errors.extend(world_errors)
        
        # 3. 时间线混乱
        timeline_errors = self.check_timeline_consistency(content, context)
        errors.extend(timeline_errors)
        
        # 4. 逻辑矛盾
        logic_errors = self.check_logical_consistency(content, context)
        errors.extend(logic_errors)
        
        # 5. 风格突变
        style_errors = self.check_style_consistency(content, context)
        errors.extend(style_errors)
        
        return errors
    
    def recovery_strategies(self):
        """定义各类错误的恢复策略"""
        return {
            'character_inconsistency': {
                'mild': self.soft_character_correction,
                'moderate': self.character_reinterpretation,
                'severe': self.character_reset
            },
            'world_rule_violation': {
                'mild': self.rule_bending_explanation,
                'moderate': self.exception_justification,
                'severe': self.reality_shift
            },
            'timeline_confusion': {
                'mild': self.temporal_clarification,
                'moderate': self.flashback_reframe,
                'severe': self.timeline_reset
            }
        }
```

## 6.2 程序化内容生成：规则与随机性

程序化生成为非传统叙事提供了无限的可能性。通过精心设计的规则系统和控制随机性，我们可以创造既有结构又充满惊喜的叙事体验。

### 6.2.1 生成式语法与模板系统

#### Context-Free Grammar在叙事中的应用

上下文无关文法（CFG）是生成结构化内容的强大工具：

```python
class NarrativeGrammar:
    def __init__(self):
        self.rules = {
            "<story>": ["<opening> <conflict> <resolution>"],
            
            "<opening>": [
                "<time> <protagonist> <location> <mood>",
                "<mood> <time> <protagonist> <action>",
                "It was <time> when <protagonist> <discovered>"
            ],
            
            "<protagonist>": [
                "the last <occupation> in <place>",
                "<adjective> <name>",
                "someone who <unique_trait>"
            ],
            
            "<conflict>": [
                "but then <antagonist> <villainous_action>",
                "when suddenly <disaster> <consequence>",
                "until the day <twist> changed everything"
            ],
            
            "<resolution>": [
                "In the end, <lesson> <outcome>",
                "<sacrifice> led to <transformation>",
                "And so <protagonist> learned <wisdom>"
            ]
        }
        
        self.terminals = {
            "<time>": ["in the year 2157", "during the last winter", 
                      "when the stars aligned"],
            "<occupation>": ["dream merchant", "memory thief", 
                           "gravity dancer"],
            "<adjective>": ["forgotten", "ethereal", "clockwork"],
            "<name>": ["Zara", "Echo", "Meridian"],
            # ... 更多终端符号
        }
    
    def generate(self, symbol="<story>"):
        """递归生成故事"""
        if symbol in self.terminals:
            return random.choice(self.terminals[symbol])
        
        if symbol in self.rules:
            rule = random.choice(self.rules[symbol])
            # 递归替换所有非终端符号
            for non_terminal in self.find_non_terminals(rule):
                replacement = self.generate(non_terminal)
                rule = rule.replace(non_terminal, replacement, 1)
            return rule
        
        return symbol
```

#### 模板变量与条件逻辑

高级模板系统支持条件逻辑和状态相关的生成：

```python
class SmartTemplate:
    def __init__(self):
        self.templates = {
            "room_description": """
            你进入了{room_name}。
            {if lighting == 'dark'}
                黑暗笼罩着一切，你几乎看不清周围。
                {if has_item('torch')}
                    你点燃了火把，微弱的光芒揭示了房间的轮廓。
                {else}
                    你必须在黑暗中摸索前进。
                {/if}
            {elif lighting == 'dim'}
                微弱的{light_source}提供了朦胧的照明。
            {else}
                明亮的光线让每个细节都清晰可见。
            {/if}
            
            {if room_state.has_monster}
                {monster_description}
            {/if}
            
            {for item in visible_items}
                你注意到{item.description}。
            {/for}
            """
        }
    
    def render(self, template_name, context):
        """渲染模板with context"""
        template = self.templates[template_name]
        return self.process_template(template, context)
```

#### 嵌套生成与递归结构

递归生成创造分形般的叙事结构：

```python
def generate_nested_story(depth=0, max_depth=3):
    """生成嵌套故事结构"""
    
    if depth >= max_depth:
        # 基础情况：简单故事片段
        return generate_simple_fragment()
    
    story_types = [
        "linear",      # 线性叙事
        "circular",    # 循环结构
        "branching",   # 分支故事
        "nested"       # 故事中的故事
    ]
    
    chosen_type = weighted_choice(story_types)
    
    if chosen_type == "nested":
        # 递归生成故事中的故事
        frame_story = generate_frame_narrative()
        inner_story = generate_nested_story(depth + 1, max_depth)
        
        return f"""
        {frame_story['opening']}
        
        "{inner_story}"
        
        {frame_story['closing']}
        """
    
    elif chosen_type == "branching":
        # 生成分支结构
        trunk = generate_story_trunk()
        branches = [
            generate_nested_story(depth + 1, max_depth)
            for _ in range(random.randint(2, 4))
        ]
        
        return create_branching_narrative(trunk, branches)
```

### 6.2.2 概率模型与叙事多样性

#### 马尔可夫链生成句子

马尔可夫链提供了简单但有效的文本生成方法：

```python
class NarrativeMarkovChain:
    def __init__(self, order=2):
        self.order = order
        self.transitions = defaultdict(Counter)
        
    def train(self, corpus):
        """从语料库学习转移概率"""
        for sentence in corpus:
            words = sentence.split()
            for i in range(len(words) - self.order):
                state = tuple(words[i:i+self.order])
                next_word = words[i+self.order]
                self.transitions[state][next_word] += 1
    
    def generate(self, start_state, length=100):
        """生成文本"""
        if isinstance(start_state, str):
            current = tuple(start_state.split())
        else:
            current = start_state
            
        result = list(current)
        
        for _ in range(length):
            if current not in self.transitions:
                # 找相似状态或随机重启
                current = self.find_similar_state(current)
                
            next_word = self.weighted_choice(
                self.transitions[current]
            )
            result.append(next_word)
            
            # 更新状态
            current = current[1:] + (next_word,)
            
        return ' '.join(result)
    
    def blend_styles(self, style_weights):
        """混合多种写作风格"""
        blended_transitions = defaultdict(Counter)
        
        for style, weight in style_weights.items():
            style_chain = self.style_chains[style]
            for state, choices in style_chain.transitions.items():
                for word, count in choices.items():
                    blended_transitions[state][word] += count * weight
                    
        return blended_transitions
```

#### 加权随机与叙事倾向

通过加权系统引导故事向特定方向发展：

```python
class NarrativeTendency:
    def __init__(self):
        self.tendencies = {
            "mood": {
                "dark": 0.5,
                "hopeful": 0.3,
                "mysterious": 0.2
            },
            "pacing": {
                "fast": 0.4,
                "steady": 0.4,
                "slow": 0.2
            },
            "focus": {
                "action": 0.3,
                "character": 0.4,
                "atmosphere": 0.3
            }
        }
        
    def adjust_weights(self, story_progress, reader_choices):
        """根据故事进展和读者选择动态调整权重"""
        
        # 故事后期增加快节奏倾向
        if story_progress > 0.7:
            self.tendencies["pacing"]["fast"] *= 1.5
            self.normalize_weights("pacing")
            
        # 读者选择影响后续倾向
        if "investigate" in reader_choices[-3:]:
            self.tendencies["focus"]["atmosphere"] *= 1.3
            self.tendencies["mood"]["mysterious"] *= 1.2
            
        # 保持叙事连贯性
        self.apply_momentum()
        
    def select_narrative_element(self, category):
        """根据当前权重选择叙事元素"""
        weights = self.tendencies[category]
        return self.weighted_random_choice(weights)
    
    def apply_momentum(self):
        """应用叙事动量，避免风格突变"""
        for category in self.tendencies:
            weights = self.tendencies[category]
            # 强化当前主导风格
            max_key = max(weights, key=weights.get)
            weights[max_key] *= 1.1
            self.normalize_weights(category)
```

#### 混沌系统与蝴蝶效应

在叙事中引入混沌理论，创造深度因果关系：

```python
class ChaoticNarrative:
    def __init__(self):
        self.world_state = {
            "weather": 0.5,
            "political_tension": 0.3,
            "economic_health": 0.7,
            "social_harmony": 0.6,
            "magical_stability": 0.8
        }
        self.sensitivity = 0.1  # 对初始条件的敏感度
        
    def butterfly_effect(self, action, magnitude=0.01):
        """小动作引发大变化"""
        
        # 直接影响
        direct_impact = self.calculate_direct_impact(action)
        
        # 连锁反应
        ripples = []
        current_impact = direct_impact
        
        for i in range(5):  # 5层连锁反应
            next_impacts = {}
            
            for factor, change in current_impact.items():
                # 非线性放大
                amplification = self.nonlinear_transform(
                    change, 
                    self.world_state[factor]
                )
                
                # 影响相关因素
                related = self.get_related_factors(factor)
                for related_factor in related:
                    influence = amplification * self.get_correlation(
                        factor, 
                        related_factor
                    )
                    next_impacts[related_factor] = influence
                    
            ripples.append(next_impacts)
            current_impact = next_impacts
            
        return self.narrative_consequences(ripples)
    
    def nonlinear_transform(self, change, current_state):
        """非线性变换模拟混沌行为"""
        # 在临界点附近变化更剧烈
        if 0.4 < current_state < 0.6:
            return change * 3.7 * current_state * (1 - current_state)
        return change * 1.5
    
    def narrative_consequences(self, ripples):
        """将数值变化转化为叙事事件"""
        events = []
        
        for layer, impacts in enumerate(ripples):
            delay = layer * 3  # 天数
            
            for factor, magnitude in impacts.items():
                if abs(magnitude) > 0.15:
                    event = self.generate_event(
                        factor, 
                        magnitude, 
                        delay
                    )
                    events.append(event)
                    
        return self.weave_events_into_narrative(events)
```

### 6.2.3 混合方法：AI+规则

最强大的生成系统结合了AI的创造力和规则的可控性：

#### 规则约束下的AI生成

```python
class ConstrainedAIGenerator:
    def __init__(self, ai_model):
        self.ai_model = ai_model
        self.constraints = {
            "plot": PlotConstraintEngine(),
            "character": CharacterConsistencyChecker(),
            "world": WorldRuleValidator()
        }
        
    def generate_with_constraints(self, prompt, context):
        """在约束下生成内容"""
        
        # 第一步：AI自由生成
        raw_generation = self.ai_model.generate(prompt)
        
        # 第二步：验证约束
        violations = self.check_all_constraints(
            raw_generation, 
            context
        )
        
        if not violations:
            return raw_generation
            
        # 第三步：修复违规
        repair_prompt = self.create_repair_prompt(
            raw_generation, 
            violations
        )
        
        repaired = self.ai_model.generate(repair_prompt)
        
        # 第四步：二次验证
        if self.check_all_constraints(repaired, context):
            return repaired
        else:
            # 降级到规则生成
            return self.rule_based_fallback(context)
    
    def create_repair_prompt(self, text, violations):
        """创建修复提示"""
        return f"""
        原始生成：{text}
        
        检测到以下问题：
        {self.format_violations(violations)}
        
        请修改内容以符合所有约束，保持原始创意的同时确保：
        1. 不违反已建立的规则
        2. 保持角色性格一致
        3. 符合情节逻辑
        
        修改后的版本：
        """
```

#### AI辅助的规则优化

让AI帮助优化和扩展规则系统：

```python
class RuleEvolutionSystem:
    def __init__(self):
        self.rules = {}
        self.performance_history = []
        
    def evolve_rules(self, feedback_data):
        """基于反馈进化规则"""
        
        # 分析哪些规则产生了好的结果
        successful_patterns = self.analyze_success_patterns(
            feedback_data
        )
        
        # 使用AI提议新规则
        evolution_prompt = f"""
        当前规则系统：
        {self.format_rules()}
        
        成功的模式：
        {successful_patterns}
        
        失败的案例：
        {self.get_failure_cases(feedback_data)}
        
        请提议3-5个新规则或规则修改，以提高生成质量。
        每个规则应该：
        1. 具体且可执行
        2. 不与现有规则冲突
        3. 解决识别出的问题
        """
        
        proposed_rules = self.ai_model.generate(evolution_prompt)
        
        # 测试新规则
        test_results = self.test_rules(proposed_rules)
        
        # 采纳表现良好的规则
        self.adopt_successful_rules(test_results)
```

#### 人机协作工作流

设计高效的人机协作流程：

```python
class CollaborativeWorkflow:
    def __init__(self):
        self.stages = {
            "ideation": HumanLead_AIAssist(),
            "expansion": AILead_HumanGuide(),
            "refinement": HumanAIIteration(),
            "validation": HumanFinalApproval()
        }
        
    def collaborative_generation(self, initial_concept):
        """完整的协作生成流程"""
        
        # 阶段1：人类主导的创意阶段
        human_idea = self.get_human_input(
            "描述你的核心创意："
        )
        
        ai_variations = self.generate_variations(human_idea)
        
        selected_concept = self.human_select_best(
            ai_variations,
            "选择最吸引你的变体："
        )
        
        # 阶段2：AI主导的扩展阶段
        expanded_content = self.ai_expand(selected_concept)
        
        human_feedback = self.get_human_feedback(
            expanded_content,
            "哪些部分需要调整？"
        )
        
        # 阶段3：迭代优化
        refined_content = expanded_content
        
        for i in range(3):  # 最多3轮迭代
            ai_revision = self.ai_revise(
                refined_content, 
                human_feedback
            )
            
            if self.human_approves(ai_revision):
                refined_content = ai_revision
                break
                
            human_feedback = self.get_specific_feedback(
                ai_revision
            )
            
        # 阶段4：最终人类润色
        final_content = self.human_final_edit(refined_content)
        
        return final_content
    
    def ai_expand(self, concept):
        """AI扩展概念"""
        expansion_prompt = f"""
        核心概念：{concept}
        
        请扩展这个概念，包括：
        1. 3个主要情节点
        2. 2个次要角色
        3. 1个意外转折
        4. 情感弧线
        5. 主题深化
        
        保持原始概念的精髓，但大胆创新细节。
        """
        
        return self.ai_model.generate(expansion_prompt)
```

#### 语义验证与逻辑推理

确保生成内容的语义连贯和逻辑一致：

```python
class SemanticValidator:
    def __init__(self):
        self.knowledge_base = KnowledgeGraph()
        self.inference_engine = LogicalReasoner()
        
    def validate_semantic_consistency(self, generated_text, context):
        """验证生成文本的语义一致性"""
        
        # 提取实体和关系
        entities = self.extract_entities(generated_text)
        relations = self.extract_relations(generated_text)
        
        # 检查事实一致性
        fact_violations = []
        for entity in entities:
            known_facts = self.knowledge_base.get_facts(entity)
            stated_facts = self.extract_entity_properties(
                entity, 
                generated_text
            )
            
            conflicts = self.find_conflicts(known_facts, stated_facts)
            if conflicts:
                fact_violations.extend(conflicts)
                
        # 检查逻辑一致性
        logical_violations = self.inference_engine.check_consistency(
            relations,
            self.knowledge_base
        )
        
        return {
            'is_valid': len(fact_violations) == 0 and len(logical_violations) == 0,
            'fact_violations': fact_violations,
            'logical_violations': logical_violations
        }
    
    def semantic_repair(self, text, violations):
        """修复语义违规"""
        repair_strategies = {
            'contradiction': self.resolve_contradiction,
            'missing_context': self.add_context,
            'broken_causality': self.repair_causality,
            'inconsistent_timeline': self.fix_timeline
        }
        
        repaired_text = text
        for violation in violations:
            strategy = repair_strategies.get(
                violation['type'], 
                self.generic_repair
            )
            repaired_text = strategy(repaired_text, violation)
            
        return repaired_text
```

#### 动态规则生成

让系统自动学习和生成新的叙事规则：

```python
class DynamicRuleGenerator:
    def __init__(self):
        self.pattern_analyzer = PatternAnalyzer()
        self.rule_synthesizer = RuleSynthesizer()
        self.effectiveness_tracker = EffectivenessTracker()
        
    def learn_from_successful_narratives(self, narrative_corpus, ratings):
        """从成功的叙事中学习模式"""
        
        # 识别高评分叙事的共同模式
        high_rated = [
            n for n, r in zip(narrative_corpus, ratings) 
            if r > 4.0
        ]
        
        patterns = self.pattern_analyzer.extract_patterns(high_rated)
        
        # 将模式转化为可执行规则
        new_rules = []
        for pattern in patterns:
            rule = self.pattern_to_rule(pattern)
            if self.validate_rule(rule):
                new_rules.append(rule)
                
        return new_rules
    
    def pattern_to_rule(self, pattern):
        """将识别的模式转换为生成规则"""
        
        rule_template = {
            'name': f"rule_{pattern['id']}",
            'condition': self.synthesize_condition(pattern),
            'action': self.synthesize_action(pattern),
            'weight': self.calculate_initial_weight(pattern),
            'metadata': {
                'source_pattern': pattern,
                'creation_time': datetime.now(),
                'confidence': pattern['confidence']
            }
        }
        
        return Rule(rule_template)
    
    def meta_rule_generation(self):
        """生成控制其他规则的元规则"""
        
        meta_rules = {
            'conflict_resolution': """
            当多个规则冲突时：
            1. 优先级：情节关键 > 角色一致性 > 世界设定
            2. 新近性：最近建立的事实优先
            3. 影响范围：局部规则优于全局规则
            """,
            
            'rule_activation': """
            规则激活条件：
            1. 上下文相关性 > 0.7
            2. 与当前叙事阶段匹配
            3. 不违反已激活的约束
            """,
            
            'adaptive_weighting': """
            动态调整规则权重：
            - 成功应用：权重 * 1.1
            - 产生冲突：权重 * 0.9
            - 读者喜欢：权重 * 1.2
            - 读者困惑：权重 * 0.8
            """
        }
        
        return meta_rules
```

#### 多智能体协作系统

使用多个专门化的AI智能体协同创作：

```python
class MultiAgentNarrativeSystem:
    def __init__(self):
        self.agents = {
            'plot_architect': PlotAgent(),
            'character_psychologist': CharacterAgent(),
            'world_builder': WorldAgent(),
            'dialogue_master': DialogueAgent(),
            'style_editor': StyleAgent(),
            'continuity_checker': ContinuityAgent()
        }
        self.coordinator = NarrativeCoordinator()
        
    def collaborative_generation(self, prompt, context):
        """多智能体协作生成"""
        
        # 阶段1：独立生成
        proposals = {}
        for agent_name, agent in self.agents.items():
            if agent.is_relevant(prompt, context):
                proposal = agent.generate_proposal(prompt, context)
                proposals[agent_name] = proposal
                
        # 阶段2：协商与整合
        negotiation_rounds = 0
        consensus = False
        
        while not consensus and negotiation_rounds < 5:
            # 交换提议
            for agent_name, agent in self.agents.items():
                other_proposals = {
                    k: v for k, v in proposals.items() 
                    if k != agent_name
                }
                
                # 智能体评估其他提议
                feedback = agent.evaluate_proposals(other_proposals)
                
                # 根据反馈调整自己的提议
                if agent.should_adjust(feedback):
                    proposals[agent_name] = agent.adjust_proposal(
                        proposals[agent_name],
                        feedback
                    )
                    
            # 检查是否达成共识
            consensus = self.check_consensus(proposals)
            negotiation_rounds += 1
            
        # 阶段3：最终整合
        if consensus:
            final_narrative = self.coordinator.integrate_proposals(
                proposals
            )
        else:
            # 协调者强制整合
            final_narrative = self.coordinator.mediate_conflicts(
                proposals
            )
            
        # 阶段4：质量保证
        final_narrative = self.quality_assurance(final_narrative)
        
        return final_narrative
    
    def quality_assurance(self, narrative):
        """多智能体质量检查"""
        
        issues = []
        
        # 每个智能体从自己的专业角度检查
        for agent_name, agent in self.agents.items():
            agent_issues = agent.quality_check(narrative)
            if agent_issues:
                issues.extend(agent_issues)
                
        # 如果有问题，启动修复流程
        if issues:
            return self.collaborative_repair(narrative, issues)
            
        return narrative
```

#### 实时学习与适应

系统在运行时不断学习和改进：

```python
class RealtimeLearningSystem:
    def __init__(self):
        self.experience_buffer = ExperienceReplay(capacity=10000)
        self.model_updater = OnlineModelUpdater()
        self.performance_monitor = PerformanceMonitor()
        
    def learn_from_interaction(self, interaction):
        """从每次交互中学习"""
        
        # 记录交互
        experience = {
            'input': interaction['user_input'],
            'output': interaction['ai_output'],
            'feedback': interaction.get('user_feedback'),
            'context': interaction['context'],
            'timestamp': datetime.now()
        }
        
        self.experience_buffer.add(experience)
        
        # 实时分析
        if interaction.get('user_feedback'):
            self.process_immediate_feedback(experience)
            
        # 批量学习
        if self.experience_buffer.size() % 100 == 0:
            self.batch_learning()
            
    def process_immediate_feedback(self, experience):
        """处理即时反馈"""
        
        feedback_type = self.classify_feedback(experience['feedback'])
        
        if feedback_type == 'negative':
            # 立即分析问题
            issue_analysis = self.analyze_negative_feedback(experience)
            
            # 更新生成策略
            self.update_generation_strategy(issue_analysis)
            
            # 记录到问题模式库
            self.record_problematic_pattern(issue_analysis)
            
        elif feedback_type == 'positive':
            # 强化成功模式
            self.reinforce_successful_pattern(experience)
            
    def adaptive_generation_parameters(self, context):
        """根据学习动态调整生成参数"""
        
        # 获取相似上下文的历史表现
        similar_experiences = self.experience_buffer.get_similar(
            context, 
            k=10
        )
        
        # 分析最佳参数
        optimal_params = self.analyze_optimal_parameters(
            similar_experiences
        )
        
        # 考虑全局趋势
        global_trends = self.performance_monitor.get_trends()
        
        # 综合调整
        adjusted_params = self.balance_parameters(
            optimal_params,
            global_trends,
            context
        )
        
        return adjusted_params
    
    def continuous_improvement_loop(self):
        """持续改进循环"""
        
        while True:
            # 收集一定时间窗口的数据
            recent_data = self.experience_buffer.get_recent(hours=24)
            
            # 识别改进机会
            improvement_opportunities = self.identify_improvements(
                recent_data
            )
            
            # 生成改进假设
            hypotheses = self.generate_hypotheses(
                improvement_opportunities
            )
            
            # A/B测试验证
            for hypothesis in hypotheses:
                test_result = self.run_ab_test(hypothesis)
                
                if test_result['significant'] and test_result['positive']:
                    self.implement_improvement(hypothesis)
                    
            # 休眠直到下一个改进周期
            time.sleep(3600)  # 每小时一次
```

## 6.3 案例研究：《AI Dungeon》与《无限故事》

通过深入分析成功的AI驱动叙事项目，我们可以学习实践中的经验教训。

### 6.3.1 AI Dungeon深度解析

AI Dungeon开创了AI驱动的交互式叙事，让我们解构其核心机制：

#### GPT在游戏叙事中的应用

```python
class AIDungeonCore:
    def __init__(self):
        self.model = GPTModel()
        self.context_manager = ContextManager()
        self.safety_filter = SafetyFilter()
        
    def process_player_action(self, action):
        """处理玩家输入的核心流程"""
        
        # 1. 输入预处理
        normalized_action = self.normalize_input(action)
        
        # 2. 上下文构建
        context = self.build_context(
            normalized_action,
            include_history=True,
            include_world_info=True
        )
        
        # 3. 生成响应
        raw_response = self.model.generate(
            context,
            temperature=0.8,
            max_tokens=150
        )
        
        # 4. 后处理
        filtered_response = self.safety_filter.apply(raw_response)
        formatted_response = self.format_response(filtered_response)
        
        # 5. 状态更新
        self.update_game_state(action, formatted_response)
        
        return formatted_response
    
    def build_context(self, action, **kwargs):
        """构建提示上下文"""
        context_parts = []
        
        # 基础设定
        context_parts.append(self.get_scenario_prompt())
        
        # 历史记录
        if kwargs.get('include_history'):
            context_parts.append(
                self.context_manager.get_recent_history()
            )
            
        # 世界信息
        if kwargs.get('include_world_info'):
            relevant_info = self.retrieve_world_info(action)
            context_parts.append(relevant_info)
            
        # 玩家动作
        context_parts.append(f"\n> {action}\n")
        
        return "\n".join(context_parts)
```

#### 记忆系统与世界持久性

AI Dungeon的记忆系统是其成功的关键：

```python
class MemorySystem:
    def __init__(self):
        self.short_term = deque(maxlen=20)  # 最近20条交互
        self.long_term = {}  # 关键信息存储
        self.world_facts = WorldKnowledgeBase()
        
    def process_interaction(self, action, response):
        """处理一次交互，提取和存储信息"""
        
        # 短期记忆
        self.short_term.append({
            "action": action,
            "response": response,
            "timestamp": time.time()
        })
        
        # 信息提取
        extracted_info = self.extract_information(action, response)
        
        # 更新长期记忆
        for info_type, content in extracted_info.items():
            if info_type == "character":
                self.update_character_info(content)
            elif info_type == "location":
                self.update_location_info(content)
            elif info_type == "item":
                self.update_inventory(content)
            elif info_type == "plot_point":
                self.record_plot_point(content)
                
    def extract_information(self, action, response):
        """使用NLP提取关键信息"""
        
        # 实体识别
        entities = self.ner_model.extract_entities(
            action + " " + response
        )
        
        # 关系抽取
        relationships = self.relation_extractor.extract(
            entities, 
            response
        )
        
        # 状态变化检测
        state_changes = self.detect_state_changes(
            action, 
            response
        )
        
        return {
            "entities": entities,
            "relationships": relationships,
            "state_changes": state_changes
        }
    
    def retrieve_relevant_memory(self, current_context):
        """智能检索相关记忆"""
        
        relevance_scores = {}
        
        # 计算相关性分数
        for memory_id, memory in self.long_term.items():
            score = self.calculate_relevance(
                memory, 
                current_context
            )
            relevance_scores[memory_id] = score
            
        # 返回最相关的记忆
        top_memories = sorted(
            relevance_scores.items(), 
            key=lambda x: x[1], 
            reverse=True
        )[:5]
        
        return [
            self.long_term[mem_id] 
            for mem_id, _ in top_memories
        ]
```

#### 玩家行为的处理策略

处理开放式玩家输入的挑战和解决方案：

```python
class PlayerActionHandler:
    def __init__(self):
        self.action_patterns = self.load_action_patterns()
        self.fallback_strategies = FallbackStrategies()
        
    def interpret_action(self, raw_input):
        """解释玩家意图"""
        
        # 1. 直接模式匹配
        for pattern, action_type in self.action_patterns.items():
            if re.match(pattern, raw_input, re.IGNORECASE):
                return self.handle_known_action(
                    action_type, 
                    raw_input
                )
                
        # 2. 语义理解
        intent = self.semantic_analyzer.analyze_intent(raw_input)
        
        if intent.confidence > 0.7:
            return self.handle_semantic_action(intent)
            
        # 3. 上下文推理
        contextual_interpretation = self.context_aware_interpret(
            raw_input
        )
        
        if contextual_interpretation:
            return contextual_interpretation
            
        # 4. 优雅降级
        return self.fallback_strategies.handle_unclear_input(
            raw_input
        )
    
    def handle_edge_cases(self, action):
        """处理边缘情况"""
        
        edge_cases = {
            "meta_gaming": self.handle_meta_gaming,
            "inappropriate": self.handle_inappropriate_content,
            "repetitive": self.handle_repetitive_actions,
            "contradictory": self.handle_contradictions,
            "exploit": self.handle_exploit_attempts
        }
        
        for case_type, handler in edge_cases.items():
            if self.detect_edge_case(action, case_type):
                return handler(action)
                
        return None
        
    def handle_contradictions(self, action):
        """处理与已建立事实矛盾的动作"""
        
        contradiction = self.detect_contradiction(action)
        
        if contradiction:
            return f"""
            你试图{action}，但是{contradiction.explanation}。
            {self.suggest_alternative(action, contradiction)}
            """
```

### 6.3.2 无限故事的设计哲学

"无限故事"项目展示了生成式分支管理的艺术：

#### 生成式分支管理

```python
class InfiniteStoryBranchManager:
    def __init__(self):
        self.story_graph = nx.DiGraph()
        self.branch_generator = BranchGenerator()
        self.coherence_checker = CoherenceChecker()
        
    def generate_branches(self, node_id, num_branches=3):
        """为给定节点生成分支"""
        
        current_node = self.story_graph.nodes[node_id]
        context = self.build_branch_context(node_id)
        
        # 生成多样化的分支
        branch_seeds = self.generate_diverse_seeds(
            context, 
            num_branches
        )
        
        branches = []
        for seed in branch_seeds:
            # 确保每个分支都有独特的发展方向
            branch_prompt = f"""
            当前情况：{current_node['content']}
            
            分支方向：{seed['direction']}
            情绪基调：{seed['tone']}
            
            生成一个引人入胜的选择选项，将故事引向：
            {seed['narrative_goal']}
            
            选项文本（15-25字）：
            """
            
            branch_text = self.generate_branch_text(branch_prompt)
            
            # 预生成分支内容以确保质量
            branch_content = self.preview_branch_outcome(
                context, 
                branch_text
            )
            
            if self.coherence_checker.is_coherent(
                branch_content, 
                context
            ):
                branches.append({
                    "text": branch_text,
                    "preview": branch_content[:100],
                    "metadata": seed
                })
                
        return self.rank_branches(branches)
    
    def generate_diverse_seeds(self, context, num):
        """生成多样化的分支种子"""
        
        diversity_dimensions = [
            "action_vs_reflection",
            "conflict_vs_harmony", 
            "revelation_vs_mystery",
            "individual_vs_collective",
            "order_vs_chaos"
        ]
        
        seeds = []
        used_combinations = set()
        
        while len(seeds) < num:
            # 在多个维度上采样以确保多样性
            combination = tuple(
                random.choice([-1, 0, 1]) 
                for _ in diversity_dimensions
            )
            
            if combination not in used_combinations:
                used_combinations.add(combination)
                
                seed = self.combination_to_seed(
                    combination, 
                    context
                )
                seeds.append(seed)
                
        return seeds
```

#### 情节连贯性保证

保持分支叙事的连贯性是最大的挑战：

```python
class CoherenceEngine:
    def __init__(self):
        self.fact_tracker = FactTracker()
        self.character_arcs = CharacterArcManager()
        self.theme_consistency = ThemeConsistencyChecker()
        
    def ensure_coherence(self, new_content, story_history):
        """确保新内容与历史保持连贯"""
        
        # 1. 事实一致性检查
        fact_conflicts = self.fact_tracker.check_conflicts(
            new_content, 
            story_history
        )
        
        if fact_conflicts:
            # 自动修复事实冲突
            new_content = self.resolve_fact_conflicts(
                new_content, 
                fact_conflicts
            )
            
        # 2. 角色发展连续性
        character_issues = self.character_arcs.check_continuity(
            new_content
        )
        
        if character_issues:
            # 调整以符合角色弧线
            new_content = self.align_with_character_arcs(
                new_content, 
                character_issues
            )
            
        # 3. 主题一致性
        theme_score = self.theme_consistency.score(
            new_content, 
            story_history
        )
        
        if theme_score < 0.7:
            # 增强主题相关性
            new_content = self.reinforce_themes(
                new_content, 
                self.extract_themes(story_history)
            )
            
        # 4. 时间线验证
        timeline_valid = self.validate_timeline(
            new_content, 
            story_history
        )
        
        if not timeline_valid:
            new_content = self.fix_timeline_issues(new_content)
            
        return new_content
    
    def merge_parallel_branches(self, branch_histories):
        """合并平行分支的历史"""
        
        # 找出共同历史点
        common_ancestor = self.find_common_ancestor(branch_histories)
        
        # 识别每个分支的独特发展
        unique_developments = []
        for history in branch_histories:
            unique = self.extract_unique_developments(
                history, 
                common_ancestor
            )
            unique_developments.append(unique)
            
        # 创建统一的叙事
        merged_narrative = self.create_unified_narrative(
            common_ancestor, 
            unique_developments
        )
        
        return merged_narrative
```

#### 读者期待与惊喜平衡

```python
class ExpectationManager:
    def __init__(self):
        self.reader_model = ReaderExpectationModel()
        self.surprise_calculator = SurpriseCalculator()
        
    def balance_expectation_surprise(self, context, options):
        """平衡读者期待和惊喜"""
        
        # 预测读者期待
        expectations = self.reader_model.predict_expectations(
            context
        )
        
        # 计算每个选项的惊喜度
        surprise_scores = {}
        for option in options:
            surprise = self.surprise_calculator.calculate(
                option, 
                expectations
            )
            surprise_scores[option.id] = surprise
            
        # 最优平衡策略
        optimal_distribution = {
            "expected": 0.4,      # 40%符合期待
            "twist": 0.4,         # 40%合理转折  
            "shocking": 0.2       # 20%完全意外
        }
        
        # 分类选项
        categorized = self.categorize_by_surprise(
            options, 
            surprise_scores
        )
        
        # 根据分布选择
        final_selection = self.select_by_distribution(
            categorized, 
            optimal_distribution
        )
        
        return final_selection
    
    def adaptive_surprise_pacing(self, story_progress):
        """根据故事进度调整惊喜节奏"""
        
        pacing_curve = {
            "opening": {"surprise": 0.3, "comfort": 0.7},
            "rising_action": {"surprise": 0.5, "comfort": 0.5},
            "climax": {"surprise": 0.8, "comfort": 0.2},
            "falling_action": {"surprise": 0.4, "comfort": 0.6},
            "resolution": {"surprise": 0.2, "comfort": 0.8}
        }
        
        current_phase = self.identify_story_phase(story_progress)
        return pacing_curve[current_phase]
```

### 6.3.3 技术架构与优化

实际部署AI叙事系统的技术考虑：

#### API调用优化

```python
class APIOptimizer:
    def __init__(self):
        self.cache = ResponseCache()
        self.batch_queue = BatchQueue()
        self.predictor = ResponsePredictor()
        
    def optimize_api_calls(self, request):
        """优化API调用策略"""
        
        # 1. 缓存检查
        cached_response = self.cache.get(request)
        if cached_response and self.is_cache_valid(cached_response):
            return cached_response
            
        # 2. 批处理机会
        if self.can_batch(request):
            return self.batch_queue.add(request)
            
        # 3. 预测性生成
        if self.should_pregenerate(request):
            self.trigger_pregeneration(request)
            
        # 4. 智能重试
        response = self.call_with_retry(request)
        
        # 5. 缓存新响应
        self.cache.store(request, response)
        
        return response
    
    def streaming_generation(self, prompt, callback):
        """流式生成for better UX"""
        
        # 使用SSE (Server-Sent Events)
        def generate():
            partial_response = ""
            
            for token in self.api.stream_generate(prompt):
                partial_response += token
                
                # 实时语法检查
                if self.is_sentence_complete(partial_response):
                    yield partial_response
                    partial_response = ""
                    
                # 实时安全检查
                if self.safety_check_needed(partial_response):
                    if not self.is_safe(partial_response):
                        yield self.get_safe_alternative()
                        return
                        
        return generate()
```

#### 缓存策略

```python
class SmartCache:
    def __init__(self):
        self.response_cache = LRUCache(maxsize=10000)
        self.embedding_cache = {}
        self.pattern_cache = PatternCache()
        
    def cache_key_generation(self, prompt, context):
        """生成智能缓存键"""
        
        # 语义指纹而非精确匹配
        semantic_hash = self.get_semantic_hash(prompt)
        
        # 包含关键上下文
        context_features = self.extract_key_features(context)
        
        # 组合键
        cache_key = f"{semantic_hash}:{context_features}"
        
        return cache_key
    
    def fuzzy_cache_lookup(self, prompt):
        """模糊缓存查找"""
        
        # 获取语义相似的缓存条目
        prompt_embedding = self.get_embedding(prompt)
        
        similar_entries = []
        for cached_key, cached_embedding in self.embedding_cache.items():
            similarity = cosine_similarity(
                prompt_embedding, 
                cached_embedding
            )
            
            if similarity > 0.95:
                similar_entries.append((cached_key, similarity))
                
        if similar_entries:
            # 返回最相似的
            best_match = max(similar_entries, key=lambda x: x[1])
            return self.response_cache.get(best_match[0])
            
        return None
    
    def predictive_caching(self, current_state):
        """预测性缓存"""
        
        # 预测可能的下一步
        likely_actions = self.predict_next_actions(current_state)
        
        # 后台预生成
        for action in likely_actions[:3]:  # Top 3
            if action not in self.response_cache:
                self.background_generate(action, current_state)
```

#### 成本控制

```python
class CostController:
    def __init__(self, monthly_budget=1000):
        self.budget = monthly_budget
        self.usage_tracker = UsageTracker()
        self.optimization_engine = OptimizationEngine()
        
    def manage_costs(self, request):
        """智能成本管理"""
        
        # 1. 根据重要性分级
        priority = self.assess_request_priority(request)
        
        # 2. 选择appropriate模型
        if priority == "low":
            # 使用更便宜的模型或缓存
            return self.use_economy_mode(request)
        elif priority == "medium":
            # 标准处理
            return self.standard_processing(request)
        else:
            # 高质量生成
            return self.premium_processing(request)
            
    def dynamic_model_selection(self, task):
        """动态模型选择"""
        
        model_options = {
            "micro": {"cost": 0.001, "quality": 0.6},
            "small": {"cost": 0.01, "quality": 0.8},
            "large": {"cost": 0.1, "quality": 0.95}
        }
        
        # 根据任务复杂度选择
        complexity = self.assess_complexity(task)
        
        if complexity < 0.3:
            return "micro"
        elif complexity < 0.7:
            return "small"
        else:
            return "large"
    
    def token_optimization(self, prompt):
        """Token使用优化"""
        
        # 1. 压缩冗余信息
        compressed = self.compress_prompt(prompt)
        
        # 2. 使用引用而非重复
        with_references = self.use_references(compressed)
        
        # 3. 智能截断
        if len(with_references) > self.max_tokens:
            truncated = self.smart_truncate(with_references)
            return truncated
            
        return with_references
```

## 练习题

### 基础题

**练习6.1：设计一个简单的提示模板**
创建一个提示模板，能够生成不同风格的开场白（恐怖、浪漫、科幻）。模板应该包含可替换的变量。

<details>
<summary>提示（Hint）</summary>

考虑以下要素：
- 使用明确的风格标记
- 包含场景、角色、氛围的占位符
- 提供少样本示例

</details>

<details>
<summary>参考答案</summary>

```markdown
## 通用开场白生成模板

**风格**：{style}
**场景元素**：{setting_elements}
**主角特征**：{protagonist_trait}
**开场动作**：{opening_action}

### 风格示例：

#### 恐怖风格示例：
"月光透过破碎的窗户洒进来，在地板上投下扭曲的影子。张明能听到自己的心跳声，在这座废弃医院的走廊里显得格外响亮。"

#### 浪漫风格示例：
"咖啡店里飘着焦糖玛奇朵的香味，李薇第三次偷瞄坐在窗边的那个男生。阳光正好打在他的侧脸上，像极了她梦里的场景。"

#### 科幻风格示例：
"警报声撕裂了太空站的宁静。陈博士看着监控屏上的数据，不敢相信自己的眼睛——那个信号，来自已经毁灭了三百年的地球。"

### 现在生成一个{style}风格的开场白：
场景包含{setting_elements}，主角是一个{protagonist_trait}的人，故事开始于{opening_action}。
```

使用示例：
- style: "恐怖"
- setting_elements: "暴风雨的夜晚，古老的图书馆"
- protagonist_trait: "患有幽闭恐惧症"
- opening_action: "发现了一本会流血的书"

</details>

**练习6.2：实现基础的马尔可夫链生成器**
使用Python实现一个2阶马尔可夫链文本生成器，输入是一段中文文本，输出是基于该文本风格的新句子。

<details>
<summary>提示（Hint）</summary>

关键步骤：
1. 分词（使用jieba或类似工具）
2. 构建转移概率表
3. 实现加权随机选择
4. 处理句子开始和结束

</details>

<details>
<summary>参考答案</summary>

```python
import jieba
import random
from collections import defaultdict, Counter

class ChineseMarkovChain:
    def __init__(self, order=2):
        self.order = order
        self.transitions = defaultdict(Counter)
        self.starters = []
        
    def train(self, text):
        """训练马尔可夫链"""
        sentences = text.split('。')
        
        for sentence in sentences:
            if not sentence.strip():
                continue
                
            words = list(jieba.cut(sentence))
            
            # 记录句子开始
            if len(words) >= self.order:
                self.starters.append(tuple(words[:self.order]))
            
            # 构建转移表
            for i in range(len(words) - self.order):
                current_state = tuple(words[i:i+self.order])
                next_word = words[i+self.order]
                self.transitions[current_state][next_word] += 1
    
    def generate(self, max_length=50):
        """生成文本"""
        if not self.starters:
            return "没有足够的训练数据"
            
        # 随机选择开始
        current = random.choice(self.starters)
        result = list(current)
        
        for _ in range(max_length - self.order):
            if current not in self.transitions:
                break
                
            # 加权随机选择下一个词
            choices = self.transitions[current]
            if not choices:
                break
                
            next_word = self.weighted_choice(choices)
            result.append(next_word)
            
            # 更新状态
            current = current[1:] + (next_word,)
            
            # 自然结束检测
            if next_word in ['。', '！', '？']:
                break
                
        return ''.join(result)
    
    def weighted_choice(self, choices):
        """加权随机选择"""
        total = sum(choices.values())
        rand = random.uniform(0, total)
        
        cumsum = 0
        for word, count in choices.items():
            cumsum += count
            if rand <= cumsum:
                return word
                
        return random.choice(list(choices.keys()))

# 使用示例
generator = ChineseMarkovChain(order=2)
generator.train("""
夜深了，城市的灯光依然闪烁。
她独自走在空旷的街道上，脚步声在夜色中回响。
远处传来汽车的鸣笛声，像是这座不夜城的心跳。
她想起了许多往事，那些美好的和不那么美好的记忆。
城市的灯光映在她的眼中，闪烁着复杂的情绪。
""")

# 生成新文本
for _ in range(3):
    print(generator.generate())
```

</details>

**练习6.3：设计上下文压缩算法**
设计一个算法，将1000字的故事历史压缩到200字以内，同时保留关键信息。

<details>
<summary>提示（Hint）</summary>

考虑保留：
- 角色名称和关键特征
- 重要事件和转折点
- 未解决的悬念
- 当前场景的直接相关信息

</details>

<details>
<summary>参考答案</summary>

```python
class ContextCompressor:
    def __init__(self):
        self.importance_keywords = {
            '死亡', '爱', '背叛', '发现', '秘密', 
            '转折', '真相', '选择', '牺牲', '重生'
        }
        
    def compress(self, text, target_length=200):
        """压缩文本到目标长度"""
        
        # 1. 提取关键信息
        key_info = self.extract_key_information(text)
        
        # 2. 构建压缩版本
        compressed = self.build_compressed_version(key_info)
        
        # 3. 确保长度限制
        if len(compressed) > target_length:
            compressed = self.further_compress(compressed, target_length)
            
        return compressed
    
    def extract_key_information(self, text):
        """提取关键信息"""
        sentences = text.split('。')
        
        key_info = {
            'characters': set(),
            'events': [],
            'unresolved': [],
            'current_situation': ""
        }
        
        for sentence in sentences:
            # 提取角色（简化：假设大写开头的词是角色名）
            import re
            names = re.findall(r'[A-Z\u4e00-\u9fff]{2,4}(?=[做说想感])', sentence)
            key_info['characters'].update(names)
            
            # 识别重要事件
            importance_score = sum(
                1 for keyword in self.importance_keywords 
                if keyword in sentence
            )
            
            if importance_score > 0:
                key_info['events'].append({
                    'text': sentence,
                    'score': importance_score
                })
                
            # 识别悬念（问句或包含"但是"、"然而"等）
            if any(marker in sentence for marker in ['？', '但是', '然而', '却']):
                key_info['unresolved'].append(sentence)
                
        # 最后一句通常是当前情况
        if sentences:
            key_info['current_situation'] = sentences[-1]
            
        return key_info
    
    def build_compressed_version(self, key_info):
        """构建压缩版本"""
        parts = []
        
        # 1. 角色介绍（如果有）
        if key_info['characters']:
            chars = '、'.join(list(key_info['characters'])[:3])
            parts.append(f"故事涉及{chars}")
            
        # 2. 关键事件（按重要性排序）
        events = sorted(
            key_info['events'], 
            key=lambda x: x['score'], 
            reverse=True
        )[:3]
        
        for event in events:
            parts.append(event['text'])
            
        # 3. 未解决的悬念
        if key_info['unresolved']:
            parts.append(key_info['unresolved'][-1])
            
        # 4. 当前情况
        if key_info['current_situation']:
            parts.append(f"现在，{key_info['current_situation']}")
            
        return '。'.join(parts) + '。'
    
    def further_compress(self, text, target_length):
        """进一步压缩"""
        # 使用摘要技术或简单截断
        if len(text) <= target_length:
            return text
            
        # 保留开头和结尾
        available = target_length - 20  # 预留省略号空间
        half = available // 2
        
        return text[:half] + "......" + text[-half:]

# 使用示例
compressor = ContextCompressor()
long_story = """很长的故事内容..."""
compressed = compressor.compress(long_story, 200)
```

</details>

### 挑战题

**练习6.4：实现自适应温度控制系统**
设计一个系统，能够根据叙事需求自动调整AI生成的温度参数。系统应该能识别不同的叙事场景（动作、对话、描述等）并相应调整。

<details>
<summary>提示（Hint）</summary>

考虑因素：
- 场景类型检测（使用关键词或模式）
- 历史生成质量反馈
- 读者偏好学习
- 平滑过渡避免突变

</details>

<details>
<summary>参考答案</summary>

```python
class AdaptiveTemperatureController:
    def __init__(self):
        self.scene_patterns = {
            'action': {
                'keywords': ['冲', '跑', '打', '躲', '扑', '闪'],
                'patterns': [r'.*突然.*', r'.*瞬间.*', r'.*立刻.*'],
                'base_temp': 0.7
            },
            'dialogue': {
                'keywords': ['说', '问', '答', '道', '喊', '叫'],
                'patterns': [r'.*".*".*', r'.*「.*」.*'],
                'base_temp': 0.9
            },
            'description': {
                'keywords': ['是', '有', '像', '如同', '仿佛'],
                'patterns': [r'.*的.*', r'.*着.*'],
                'base_temp': 1.0
            },
            'emotion': {
                'keywords': ['感到', '觉得', '心', '想', '爱', '恨'],
                'patterns': [r'.*心.*', r'.*情.*'],
                'base_temp': 0.85
            }
        }
        
        self.history = []
        self.quality_feedback = []
        self.user_preferences = self.load_preferences()
        
    def get_temperature(self, context, upcoming_text_hint=None):
        """获取自适应温度"""
        
        # 1. 检测场景类型
        scene_type = self.detect_scene_type(context, upcoming_text_hint)
        
        # 2. 获取基础温度
        base_temp = self.scene_patterns[scene_type]['base_temp']
        
        # 3. 应用历史调整
        history_adjustment = self.calculate_history_adjustment()
        
        # 4. 应用用户偏好
        preference_adjustment = self.get_preference_adjustment(scene_type)
        
        # 5. 动态微调
        dynamic_adjustment = self.calculate_dynamic_adjustment(context)
        
        # 6. 组合并限制范围
        final_temp = base_temp + history_adjustment + preference_adjustment + dynamic_adjustment
        final_temp = max(0.1, min(1.5, final_temp))
        
        # 7. 平滑过渡
        if self.history:
            last_temp = self.history[-1]['temperature']
            max_change = 0.2
            if abs(final_temp - last_temp) > max_change:
                final_temp = last_temp + max_change * (1 if final_temp > last_temp else -1)
                
        # 记录
        self.history.append({
            'scene_type': scene_type,
            'temperature': final_temp,
            'timestamp': time.time()
        })
        
        return final_temp
    
    def detect_scene_type(self, context, hint=None):
        """检测场景类型"""
        scores = {}
        
        for scene_type, config in self.scene_patterns.items():
            score = 0
            
            # 关键词匹配
            for keyword in config['keywords']:
                score += context.count(keyword) * 2
                
            # 模式匹配
            for pattern in config['patterns']:
                matches = re.findall(pattern, context)
                score += len(matches) * 3
                
            scores[scene_type] = score
            
        # 如果有提示，加权
        if hint:
            for scene_type in scores:
                if scene_type in hint.lower():
                    scores[scene_type] *= 1.5
                    
        # 返回最高分的类型
        return max(scores, key=scores.get) if scores else 'description'
    
    def calculate_history_adjustment(self):
        """基于历史表现调整"""
        if len(self.quality_feedback) < 5:
            return 0
            
        recent_feedback = self.quality_feedback[-10:]
        
        # 计算最近的质量趋势
        quality_trend = sum(f['quality'] for f in recent_feedback) / len(recent_feedback)
        
        # 如果质量低，减少随机性
        if quality_trend < 0.5:
            return -0.1
        # 如果质量高但单调，增加随机性
        elif quality_trend > 0.8:
            diversity = self.calculate_diversity(recent_feedback)
            if diversity < 0.3:
                return 0.1
                
        return 0
    
    def calculate_dynamic_adjustment(self, context):
        """动态调整基于当前上下文"""
        adjustment = 0
        
        # 检测重复
        if self.detect_repetition(context):
            adjustment += 0.1  # 增加温度打破重复
            
        # 检测关键情节点
        if self.is_critical_plot_point(context):
            adjustment -= 0.15  # 降低温度确保连贯
            
        # 检测创意机会
        if self.detect_creative_opportunity(context):
            adjustment += 0.15  # 增加温度鼓励创新
            
        return adjustment
    
    def feedback(self, quality_score, diversity_score):
        """接收质量反馈用于学习"""
        self.quality_feedback.append({
            'quality': quality_score,
            'diversity': diversity_score,
            'temperature': self.history[-1]['temperature'] if self.history else 0.8,
            'scene_type': self.history[-1]['scene_type'] if self.history else 'unknown'
        })
        
        # 定期更新偏好
        if len(self.quality_feedback) % 20 == 0:
            self.update_preferences()
    
    def update_preferences(self):
        """基于反馈更新用户偏好"""
        scene_preferences = defaultdict(list)
        
        for feedback in self.quality_feedback:
            scene_type = feedback['scene_type']
            score = feedback['quality'] * 0.7 + feedback['diversity'] * 0.3
            temp = feedback['temperature']
            
            scene_preferences[scene_type].append((temp, score))
            
        # 为每个场景类型找到最佳温度范围
        for scene_type, data in scene_preferences.items():
            if len(data) > 5:
                # 按分数排序，取前30%
                sorted_data = sorted(data, key=lambda x: x[1], reverse=True)
                top_data = sorted_data[:max(1, len(sorted_data)//3)]
                
                # 计算最佳温度范围
                best_temps = [d[0] for d in top_data]
                self.user_preferences[scene_type] = {
                    'optimal': sum(best_temps) / len(best_temps),
                    'range': (min(best_temps), max(best_temps))
                }
                
        self.save_preferences()
```

</details>

**练习6.5：构建混合生成系统**
设计一个系统，结合规则生成和AI生成的优势。系统应该能够在适当的时候切换between两种方法。

<details>
<summary>提示（Hint）</summary>

考虑：
- 何时使用规则（需要精确控制时）
- 何时使用AI（需要创造性时）
- 如何无缝切换
- 如何组合两者的输出

</details>

<details>
<summary>参考答案</summary>

```python
class HybridGenerationSystem:
    def __init__(self):
        self.rule_engine = RuleBasedGenerator()
        self.ai_engine = AIGenerator()
        self.decision_model = DecisionModel()
        self.combiner = OutputCombiner()
        
    def generate(self, context, requirements):
        """混合生成主方法"""
        
        # 1. 分析生成需求
        analysis = self.analyze_requirements(context, requirements)
        
        # 2. 决定生成策略
        strategy = self.decision_model.decide_strategy(analysis)
        
        # 3. 执行生成
        if strategy == 'rule_only':
            return self.rule_based_generation(context, requirements)
            
        elif strategy == 'ai_only':
            return self.ai_based_generation(context, requirements)
            
        elif strategy == 'rule_first_ai_enhance':
            return self.rule_first_generation(context, requirements)
            
        elif strategy == 'ai_first_rule_constrain':
            return self.ai_first_generation(context, requirements)
            
        elif strategy == 'parallel_merge':
            return self.parallel_generation(context, requirements)
            
        else:  # 'adaptive'
            return self.adaptive_generation(context, requirements)
    
    def analyze_requirements(self, context, requirements):
        """分析生成需求"""
        return {
            'precision_needed': self.calculate_precision_need(requirements),
            'creativity_needed': self.calculate_creativity_need(requirements),
            'consistency_critical': self.check_consistency_criticality(context),
            'performance_constraint': requirements.get('max_latency', float('inf')),
            'content_type': self.detect_content_type(requirements),
            'risk_tolerance': requirements.get('risk_tolerance', 'medium')
        }
    
    def rule_first_generation(self, context, requirements):
        """规则优先，AI增强"""
        
        # 1. 规则生成骨架
        skeleton = self.rule_engine.generate_skeleton(context, requirements)
        
        # 2. 识别可增强的部分
        enhancement_points = self.identify_enhancement_opportunities(skeleton)
        
        # 3. AI增强
        enhanced_content = skeleton
        for point in enhancement_points:
            if point['type'] == 'description':
                # AI生成更丰富的描述
                enhanced = self.ai_engine.enhance_description(
                    point['content'],
                    context,
                    style=requirements.get('style', 'default')
                )
                enhanced_content = enhanced_content.replace(
                    point['marker'],
                    enhanced
                )
                
            elif point['type'] == 'dialogue':
                # AI生成更自然的对话
                enhanced = self.ai_engine.generate_dialogue(
                    point['context'],
                    point['character'],
                    point['intent']
                )
                enhanced_content = enhanced_content.replace(
                    point['marker'],
                    enhanced
                )
                
        return enhanced_content
    
    def ai_first_generation(self, context, requirements):
        """AI优先，规则约束"""
        
        # 1. AI自由生成
        ai_output = self.ai_engine.generate(context, requirements)
        
        # 2. 规则检查
        violations = self.rule_engine.check_violations(ai_output, context)
        
        if not violations:
            return ai_output
            
        # 3. 修复违规
        fixed_output = ai_output
        for violation in violations:
            if violation['severity'] == 'critical':
                # 用规则生成替换
                rule_replacement = self.rule_engine.generate_compliant_segment(
                    violation['segment'],
                    violation['rule'],
                    context
                )
                fixed_output = fixed_output.replace(
                    violation['segment'],
                    rule_replacement
                )
            else:
                # 轻微调整
                adjusted = self.ai_engine.adjust_for_rule(
                    violation['segment'],
                    violation['rule'],
                    context
                )
                fixed_output = fixed_output.replace(
                    violation['segment'],
                    adjusted
                )
                
        return fixed_output
    
    def parallel_generation(self, context, requirements):
        """并行生成，智能合并"""
        
        # 1. 同时生成
        rule_output = self.rule_engine.generate(context, requirements)
        ai_output = self.ai_engine.generate(context, requirements)
        
        # 2. 分析两个输出
        rule_analysis = self.analyze_output(rule_output)
        ai_analysis = self.analyze_output(ai_output)
        
        # 3. 智能合并
        merged = self.combiner.merge(
            rule_output,
            ai_output,
            rule_analysis,
            ai_analysis,
            requirements
        )
        
        return merged
    
    def adaptive_generation(self, context, requirements):
        """自适应生成"""
        
        # 将内容分段
        segments = self.segment_content(context, requirements)
        
        result = []
        for segment in segments:
            # 为每个段落选择最佳方法
            method = self.choose_method_for_segment(segment)
            
            if method == 'rule':
                generated = self.rule_engine.generate_segment(segment)
            elif method == 'ai':
                generated = self.ai_engine.generate_segment(segment)
            else:  # 'hybrid'
                generated = self.hybrid_segment_generation(segment)
                
            result.append(generated)
            
        # 确保段落之间的连贯性
        return self.ensure_coherence(result)
    
    def hybrid_segment_generation(self, segment):
        """混合方式生成单个段落"""
        
        # 生成多个候选
        candidates = []
        
        # 规则生成候选
        for i in range(2):
            candidates.append({
                'content': self.rule_engine.generate_segment(segment),
                'source': 'rule',
                'score': 0
            })
            
        # AI生成候选
        for temp in [0.7, 0.9, 1.1]:
            candidates.append({
                'content': self.ai_engine.generate_segment(
                    segment, 
                    temperature=temp
                ),
                'source': 'ai',
                'score': 0
            })
            
        # 评分
        for candidate in candidates:
            candidate['score'] = self.score_candidate(
                candidate['content'],
                segment
            )
            
        # 选择最佳或组合
        best_candidates = sorted(
            candidates, 
            key=lambda x: x['score'], 
            reverse=True
        )[:2]
        
        if best_candidates[0]['score'] > 0.9:
            return best_candidates[0]['content']
        else:
            # 组合两个最佳候选
            return self.combine_candidates(
                best_candidates[0],
                best_candidates[1],
                segment
            )

class OutputCombiner:
    """输出合并器"""
    
    def merge(self, rule_output, ai_output, rule_analysis, ai_analysis, requirements):
        """智能合并两个输出"""
        
        # 句子级别对齐
        rule_sentences = self.split_sentences(rule_output)
        ai_sentences = self.split_sentences(ai_output)
        
        merged_sentences = []
        
        for i in range(max(len(rule_sentences), len(ai_sentences))):
            if i < len(rule_sentences) and i < len(ai_sentences):
                # 两者都有，选择更好的
                rule_score = self.score_sentence(
                    rule_sentences[i], 
                    rule_analysis, 
                    requirements
                )
                ai_score = self.score_sentence(
                    ai_sentences[i], 
                    ai_analysis, 
                    requirements
                )
                
                if abs(rule_score - ai_score) < 0.1:
                    # 分数接近，尝试组合
                    combined = self.combine_sentences(
                        rule_sentences[i], 
                        ai_sentences[i]
                    )
                    merged_sentences.append(combined)
                else:
                    # 选择分数更高的
                    merged_sentences.append(
                        rule_sentences[i] if rule_score > ai_score 
                        else ai_sentences[i]
                    )
            elif i < len(rule_sentences):
                merged_sentences.append(rule_sentences[i])
            else:
                merged_sentences.append(ai_sentences[i])
                
        return self.post_process_merged(''.join(merged_sentences))
```

</details>

**练习6.6：设计可解释的AI叙事系统**
创建一个系统，不仅能生成叙事内容，还能解释其创作决策，帮助人类作者理解和改进。

<details>
<summary>提示（Hint）</summary>

系统应该能够：
- 追踪决策路径
- 解释为什么选择特定的情节发展
- 提供替代选项和理由
- 可视化叙事结构

</details>

<details>
<summary>参考答案</summary>

```python
class ExplainableNarrativeAI:
    def __init__(self):
        self.decision_tree = DecisionTree()
        self.explanation_generator = ExplanationGenerator()
        self.alternative_tracker = AlternativeTracker()
        
    def generate_with_explanation(self, context, requirements):
        """生成内容并提供解释"""
        
        # 记录决策过程
        decision_log = DecisionLog()
        
        # 1. 分析上下文
        context_analysis = self.analyze_context(context)
        decision_log.add("context_analysis", context_analysis)
        
        # 2. 生成多个候选
        candidates = self.generate_candidates(context, requirements)
        decision_log.add("candidates", candidates)
        
        # 3. 评估每个候选
        evaluations = []
        for candidate in candidates:
            evaluation = self.evaluate_candidate(
                candidate, 
                context, 
                requirements
            )
            evaluations.append(evaluation)
            
        decision_log.add("evaluations", evaluations)
        
        # 4. 选择最佳候选
        best_candidate = self.select_best(candidates, evaluations)
        decision_log.add("selection", {
            "chosen": best_candidate,
            "reason": self.explain_selection(evaluations)
        })
        
        # 5. 生成解释
        explanation = self.generate_explanation(decision_log)
        
        return {
            "content": best_candidate["content"],
            "explanation": explanation,
            "alternatives": self.format_alternatives(candidates, evaluations),
            "decision_tree": self.visualize_decisions(decision_log)
        }
    
    def evaluate_candidate(self, candidate, context, requirements):
        """详细评估候选内容"""
        
        evaluation = {
            "candidate_id": candidate["id"],
            "scores": {},
            "strengths": [],
            "weaknesses": [],
            "risks": []
        }
        
        # 多维度评分
        dimensions = [
            ("coherence", self.evaluate_coherence),
            ("creativity", self.evaluate_creativity),
            ("character_consistency", self.evaluate_character_consistency),
            ("plot_advancement", self.evaluate_plot_advancement),
            ("thematic_relevance", self.evaluate_thematic_relevance),
            ("emotional_impact", self.evaluate_emotional_impact),
            ("pacing", self.evaluate_pacing)
        ]
        
        for dimension_name, evaluator in dimensions:
            score, explanation = evaluator(candidate, context)
            evaluation["scores"][dimension_name] = {
                "value": score,
                "explanation": explanation
            }
            
            if score > 0.8:
                evaluation["strengths"].append({
                    "dimension": dimension_name,
                    "detail": explanation
                })
            elif score < 0.5:
                evaluation["weaknesses"].append({
                    "dimension": dimension_name,
                    "detail": explanation
                })
                
        # 风险评估
        risks = self.assess_risks(candidate, context)
        evaluation["risks"] = risks
        
        # 总体评分
        evaluation["overall_score"] = self.calculate_overall_score(
            evaluation["scores"],
            requirements.get("priorities", {})
        )
        
        return evaluation
    
    def generate_explanation(self, decision_log):
        """生成人类可读的解释"""
        
        explanation = {
            "summary": "",
            "key_factors": [],
            "decision_path": [],
            "tradeoffs": []
        }
        
        # 生成摘要
        explanation["summary"] = self.explanation_generator.summarize(
            decision_log
        )
        
        # 提取关键因素
        key_factors = self.extract_key_factors(decision_log)
        explanation["key_factors"] = [
            {
                "factor": factor["name"],
                "importance": factor["weight"],
                "influence": self.explain_influence(factor, decision_log)
            }
            for factor in key_factors
        ]
        
        # 决策路径
        path = self.trace_decision_path(decision_log)
        explanation["decision_path"] = [
            {
                "step": step["name"],
                "description": step["description"],
                "alternatives_considered": step["alternatives"],
                "reason_for_choice": step["reasoning"]
            }
            for step in path
        ]
        
        # 权衡说明
        tradeoffs = self.identify_tradeoffs(decision_log)
        explanation["tradeoffs"] = [
            {
                "gained": tradeoff["positive"],
                "sacrificed": tradeoff["negative"],
                "justification": tradeoff["why_acceptable"]
            }
            for tradeoff in tradeoffs
        ]
        
        return explanation
    
    def visualize_decisions(self, decision_log):
        """可视化决策树"""
        
        # 构建决策树结构
        tree = {
            "root": "Context Analysis",
            "children": []
        }
        
        # 添加候选生成分支
        candidates_branch = {
            "node": "Candidate Generation",
            "children": []
        }
        
        for candidate in decision_log.get("candidates", []):
            candidate_node = {
                "node": f"Candidate {candidate['id']}",
                "attributes": {
                    "approach": candidate["generation_method"],
                    "key_features": candidate["features"]
                },
                "children": []
            }
            
            # 添加评估结果
            evaluation = next(
                (e for e in decision_log.get("evaluations", []) 
                 if e["candidate_id"] == candidate["id"]),
                None
            )
            
            if evaluation:
                for dimension, score_info in evaluation["scores"].items():
                    score_node = {
                        "node": dimension,
                        "score": score_info["value"],
                        "color": self.score_to_color(score_info["value"])
                    }
                    candidate_node["children"].append(score_node)
                    
            candidates_branch["children"].append(candidate_node)
            
        tree["children"].append(candidates_branch)
        
        # 添加最终选择
        selection = decision_log.get("selection", {})
        if selection:
            selection_node = {
                "node": "Final Selection",
                "chosen": selection["chosen"]["id"],
                "reason": selection["reason"]
            }
            tree["children"].append(selection_node)
            
        return tree
    
    def explain_influence(self, factor, decision_log):
        """解释因素如何影响决策"""
        
        influence_description = []
        
        # 检查因素在各个阶段的影响
        if factor["name"] == "character_consistency":
            # 示例：角色一致性如何影响
            candidates = decision_log.get("candidates", [])
            evaluations = decision_log.get("evaluations", [])
            
            high_consistency = [
                e for e in evaluations 
                if e["scores"]["character_consistency"]["value"] > 0.8
            ]
            
            if high_consistency:
                influence_description.append(
                    f"强烈偏好保持角色性格的选项（{len(high_consistency)}个候选）"
                )
                
            low_consistency = [
                e for e in evaluations 
                if e["scores"]["character_consistency"]["value"] < 0.5
            ]
            
            if low_consistency:
                influence_description.append(
                    f"排除了{len(low_consistency)}个可能破坏角色形象的选项"
                )
                
        return "；".join(influence_description)
    
    def format_alternatives(self, candidates, evaluations):
        """格式化替代选项"""
        
        alternatives = []
        
        for candidate in candidates:
            if candidate["id"] == self.selected_id:
                continue  # 跳过已选择的
                
            evaluation = next(
                (e for e in evaluations if e["candidate_id"] == candidate["id"]),
                None
            )
            
            alternative = {
                "content": candidate["content"],
                "summary": self.summarize_content(candidate["content"]),
                "pros": evaluation["strengths"] if evaluation else [],
                "cons": evaluation["weaknesses"] if evaluation else [],
                "overall_score": evaluation["overall_score"] if evaluation else 0,
                "why_not_chosen": self.explain_why_not_chosen(
                    candidate, 
                    evaluation
                )
            }
            
            alternatives.append(alternative)
            
        # 按分数排序
        alternatives.sort(key=lambda x: x["overall_score"], reverse=True)
        
        return alternatives[:3]  # 返回前3个替代选项
    
    def suggest_improvements(self, content, evaluation):
        """基于评估提供改进建议"""
        
        suggestions = []
        
        for dimension, score_info in evaluation["scores"].items():
            if score_info["value"] < 0.7:
                suggestion = self.generate_improvement_suggestion(
                    dimension,
                    score_info,
                    content
                )
                suggestions.append(suggestion)
                
        return suggestions

class ExplanationGenerator:
    """解释生成器"""
    
    def summarize(self, decision_log):
        """生成决策摘要"""
        
        selection = decision_log.get("selection", {})
        evaluations = decision_log.get("evaluations", [])
        
        if not selection or not evaluations:
            return "无法生成解释"
            
        chosen_eval = next(
            (e for e in evaluations 
             if e["candidate_id"] == selection["chosen"]["id"]),
            None
        )
        
        if not chosen_eval:
            return "选择了候选内容，但缺少评估信息"
            
        # 找出最高分的维度
        top_dimensions = sorted(
            chosen_eval["scores"].items(),
            key=lambda x: x[1]["value"],
            reverse=True
        )[:3]
        
        summary = f"选择了方案{selection['chosen']['id']}，"
        summary += f"主要因为其在{top_dimensions[0][0]}方面表现优异"
        summary += f"（得分{top_dimensions[0][1]['value']:.2f}）。"
        
        if len(top_dimensions) > 1:
            summary += f"同时在{top_dimensions[1][0]}和{top_dimensions[2][0]}"
            summary += "方面也有良好表现。"
            
        # 提及主要权衡
        if chosen_eval["weaknesses"]:
            weakness = chosen_eval["weaknesses"][0]
            summary += f"虽然在{weakness['dimension']}方面稍显不足，"
            summary += "但整体效果最符合叙事需求。"
            
        return summary
```

</details>

## 常见陷阱与错误 (Gotchas)

### 1. 过度依赖AI而失去创作控制

**问题**：完全让AI自由发挥，导致故事失控。

**解决方案**：
```python
# 错误示例
def generate_story():
    return ai.generate("继续故事")  # 太宽泛

# 正确示例
def generate_story(context):
    prompt = f"""
    当前情节：{context.current_plot}
    角色状态：{context.character_states}
    必须包含：{context.requirements}
    禁止出现：{context.restrictions}
    
    请生成下一段，保持风格一致，推进情节向{context.plot_direction}发展。
    """
    return ai.generate(prompt)
```

### 2. 忽视上下文管理导致token浪费

**问题**：把所有历史都塞进上下文。

**解决方案**：实施智能上下文管理策略，只保留相关信息。

### 3. 温度参数使用不当

**问题**：全程使用固定温度，导致生成质量不稳定。

**解决方案**：根据叙事需求动态调整温度。

### 4. 缺乏一致性检查

**问题**：AI生成内容与已建立的事实矛盾。

**解决方案**：建立事实追踪系统，每次生成后验证一致性。

### 5. 忽视安全过滤

**问题**：AI生成不适当内容。

**解决方案**：多层安全检查，包括生成前的提示设计和生成后的内容过滤。

### 6. 规则系统过于死板

**问题**：规则限制太多，扼杀创造力。

**解决方案**：设计灵活的规则系统，允许在合理范围内的变化。

### 7. 性能优化不足

**问题**：实时生成延迟太高，影响用户体验。

**解决方案**：实施缓存、预生成和流式输出。

### 8. 忽视用户反馈循环

**问题**：系统不能从用户行为中学习改进。

**解决方案**：建立反馈机制，持续优化生成策略。

## 最佳实践检查清单

### 提示工程
- [ ] 提示清晰具体，避免歧义
- [ ] 包含必要的上下文信息
- [ ] 设置明确的约束和要求
- [ ] 使用少样本示例引导风格
- [ ] 为不同场景准备模板库

### 系统架构
- [ ] 实施智能上下文管理
- [ ] 建立多级缓存机制
- [ ] 设计容错和降级策略
- [ ] 监控API使用和成本
- [ ] 准备离线fallback方案

### 内容质量
- [ ] 建立一致性检查机制
- [ ] 实施多层内容过滤
- [ ] 保持角色性格连贯
- [ ] 验证事实不矛盾
- [ ] 平衡创新与可信度

### 用户体验
- [ ] 优化响应时间
- [ ] 提供生成进度反馈
- [ ] 支持中断和重试
- [ ] 解释系统决策
- [ ] 允许用户干预和修正

### 技术优化
- [ ] 使用流式生成改善体验
- [ ] 实施智能预生成
- [ ] 优化token使用
- [ ] 监控和控制成本
- [ ] 定期评估和调优

### 创作辅助
- [ ] 提供多个候选供选择
- [ ] 解释创作决策
- [ ] 支持版本管理
- [ ] 便于迭代改进
- [ ] 保留创作者最终控制权

## 本章小结

本章深入探讨了AI协作与生成式创作的核心技术和实践方法。我们学习了：

1. **提示工程的艺术**：如何通过精心设计的提示引导AI生成高质量的叙事内容，包括角色定义、世界设定和链式思考等高级技巧。

2. **程序化生成的力量**：掌握了使用语法规则、概率模型和混沌系统创造丰富多样的叙事内容，以及如何平衡规则的确定性和随机的创造性。

3. **实践案例的启示**：通过分析AI Dungeon和无限故事等成功项目，理解了记忆管理、分支控制和用户体验优化的重要性。

4. **混合系统的优势**：学会了如何结合AI和规则系统的各自优势，创造既可控又充满惊喜的叙事体验。

5. **技术实现的关键**：掌握了API优化、缓存策略和成本控制等实际部署中的重要考虑。

记住，AI不是要取代人类创作者，而是要成为强大的创作伙伴。通过本章学习的技术和方法，你可以利用AI的力量扩展叙事的边界，创造出前所未有的阅读体验。关键是保持平衡：既要拥抱AI带来的可能性，又要保持对创作的掌控和个人视野。

在下一章中，我们将探讨如何将这些概念转化为实际的技术实现，学习各种工具链和框架的使用。