# 第3章：数据库叙事与知识图谱
*当故事成为可查询的世界*

> "在数据库中，每个事实都是一个潜在的故事起点。" — Ted Nelson

想象一个故事世界，其中每个角色、地点、事件都是数据库中的一个条目。读者不再被动地翻页，而是主动地查询、筛选、关联，像考古学家一样挖掘隐藏的叙事脉络。这就是数据库叙事的魅力——它将故事从线性序列转化为可探索的信息空间。

本章将深入探讨如何利用数据库思维重构叙事体验。我们将学习Wiki模式的协作潜力、知识图谱的语义力量，以及如何设计让读者"查询"出自己独特故事的系统。无论是构建虚构世界的百科全书，还是将真实事件组织成可导航的叙事网络，数据库叙事都为创作者提供了前所未有的表达维度。

## 学习目标

完成本章后，你将能够：
- 理解数据库结构如何映射到叙事结构
- 设计和实现Wiki式的协作叙事平台
- 构建知识图谱来表达复杂的故事关系
- 创建动态的、查询驱动的叙事体验
- 评估不同信息架构对读者体验的影响

## 章节大纲

### 3.1 从线性到关系：数据库思维的叙事革命
- 数据库模式 vs 传统叙事结构
- CRUD操作作为叙事动作
- 查询即探索：读者成为数据侦探

### 3.2 Wiki模式：协作世界构建的艺术
- MediaWiki到现代知识库的演进
- 条目关系网：超链接的叙事张力
- 版本历史：时间维度的多重叙事
- 社区贡献：去中心化的世界观构建

### 3.3 知识图谱：实体关系驱动的叙事架构
- 三元组（主-谓-宾）的叙事原子
- 本体设计：定义你的叙事宇宙规则
- 推理引擎：自动生成的情节连接
- SPARQL查询：让读者编程式探索故事

### 3.4 信息架构与导航设计
- 分类法 vs 民众分类法（Folksonomy）
- 面包屑、标签云、知识地图
- 搜索即叙事：全文检索的戏剧性
- 可视化知识：图形化的故事网络

### 3.5 动态内容生成与模板系统
- 参数化页面：一个模板，千种故事
- 嵌入查询：实时聚合的叙事片段
- 条件显示：基于读者路径的内容变化
- 统计仪表板：量化的叙事进度

### 3.6 案例深度剖析
- SCP基金会：恐怖氛围的协作构建
- 口袋妖怪百科：游戏数据的叙事化
- WikiLeaks：真实事件的数据库叙事
- Memory Alpha：虚构宇宙的严谨考据

### 本章小结
### 练习题（6-8道）
### 常见陷阱与错误
### 最佳实践检查清单

---

## 3.1 从线性到关系：数据库思维的叙事革命

### 传统叙事的局限性

传统书籍遵循严格的线性结构：开始→中间→结束。即使是最复杂的小说，仍然被束缚在页码的顺序中。读者的路径是预定的，探索空间受限于作者的安排。

但现实世界并非线性的。记忆是关联的，历史是多线程的，知识是网状的。数据库叙事正是对这种复杂性的响应。

考虑一个谋杀悬疑故事。传统方式下，读者跟随侦探的视角，按作者安排的顺序获得线索。但在数据库叙事中：

- 读者可以查询"所有在案发时间有不在场证明的人"
- 可以追踪"凶器的所有权历史"
- 可以分析"受害者的所有社交关系"
- 甚至可以运行复杂查询："找出所有既有动机又有机会的嫌疑人"

这种方式不仅改变了阅读体验，更是改变了故事的本质——从"被讲述的故事"变成"可探索的世界"。

### 数据库模式 vs 传统叙事结构

让我们对比两种思维模式：

**传统叙事结构：**
```
章节1 → 章节2 → 章节3 → ... → 结局
```

**数据库叙事结构：**
```
实体（Characters, Places, Events）
    ↓
关系（Relationships, Interactions）
    ↓
查询（Reader Queries）
    ↓
动态叙事（Dynamic Narratives）
```

### 核心概念映射

| 数据库概念 | 叙事对应 | 实例 |
|-----------|---------|------|
| 表（Table） | 叙事元素类型 | 角色表、地点表、事件表 |
| 行（Row） | 具体实例 | 哈利·波特（角色）、霍格沃茨（地点） |
| 列（Column） | 属性特征 | 年龄、魔法能力、所属学院 |
| 主键（Primary Key） | 唯一标识 | 角色ID、事件编号 |
| 外键（Foreign Key） | 叙事关联 | 角色参与的事件、事件发生地点 |
| 索引（Index） | 快速访问路径 | 按时间线索引、按地理位置索引 |
| 视图（View） | 特定视角叙事 | 从某角色视角看世界、特定时期的事件 |

### CRUD操作作为叙事动作

在数据库叙事中，基本的CRUD（Create, Read, Update, Delete）操作转化为叙事机制：

**Create（创建）**：新角色登场、新事件发生
```sql
INSERT INTO characters (name, origin, ability) 
VALUES ('神秘来客', '未知维度', '时间操控');
```

**Read（读取）**：探索和发现
```sql
SELECT * FROM events 
WHERE location = '禁忌图书馆' 
AND year BETWEEN 1990 AND 2000;
```

**Update（更新）**：角色成长、状态改变
```sql
UPDATE characters 
SET power_level = power_level + 10 
WHERE name = '主角' AND completed_quest = TRUE;
```

**Delete（删除）**：死亡、遗忘、历史抹除
```sql
DELETE FROM memories 
WHERE reliability < 0.3 
AND timestamp < '10 years ago';
```

### 查询即探索：读者成为数据侦探

在数据库叙事中，读者通过查询来"阅读"故事。每个查询都可能揭示新的叙事线索。这种机制将被动接收转化为主动发现，让每个读者都能成为故事世界的"数据侦探"。

#### 查询的叙事力量

不同类型的查询对应不同的叙事功能：

```sql
-- 1. 发现隐藏的关联（揭示型查询）
SELECT c1.name AS character1, 
       c2.name AS character2, 
       r.relationship_type,
       r.revelation_trigger
FROM characters c1
JOIN relationships r ON c1.id = r.character1_id
JOIN characters c2 ON c2.id = r.character2_id
WHERE r.hidden = TRUE 
  AND r.revelation_condition_met = TRUE;
-- 结果示例："原来管家是失散多年的兄弟！"

-- 2. 时间线重构（考古型查询）
SELECT e.event_name, 
       e.date, 
       e.location,
       GROUP_CONCAT(w.witness_name) as witnesses,
       COUNT(DISTINCT w.testimony_version) as version_count
FROM events e
JOIN witnesses w ON e.id = w.event_id
WHERE e.date BETWEEN '事件前一周' AND '事件后一周'
GROUP BY e.id
HAVING version_count > 1  -- 有多个版本的证词
ORDER BY e.date ASC;

-- 3. 动机分析（心理型查询）
SELECT 
    c.name,
    m.motive_type,
    m.intensity,
    GROUP_CONCAT(me.event_name) as triggering_events
FROM characters c
JOIN motives m ON c.id = m.character_id
JOIN motive_events me ON m.id = me.motive_id
WHERE m.target_character_id = (SELECT id FROM characters WHERE name = '受害者')
  AND m.intensity > 7  -- 强烈动机
ORDER BY m.intensity DESC;

-- 4. 网络分析（社交型查询）
WITH RECURSIVE character_network AS (
    -- 起始：主角的直接关系
    SELECT c1.id, c1.name, 0 as degree
    FROM characters c1
    WHERE c1.name = '主角'
    
    UNION ALL
    
    -- 递归：扩展到间接关系
    SELECT c2.id, c2.name, cn.degree + 1
    FROM character_network cn
    JOIN relationships r ON cn.id = r.character1_id
    JOIN characters c2 ON r.character2_id = c2.id
    WHERE cn.degree < 3  -- 限制在三度关系内
)
SELECT name, degree,
       CASE degree 
           WHEN 0 THEN '本人'
           WHEN 1 THEN '直接认识'
           WHEN 2 THEN '朋友的朋友'
           WHEN 3 THEN '间接关联'
       END as relationship_distance
FROM character_network
ORDER BY degree;
```

### 关系的力量

数据库的真正力量在于关系。在叙事中，这转化为：

1. **一对一关系**：独特的个人物品、灵魂伴侣
2. **一对多关系**：导师与学生们、君主与臣民
3. **多对多关系**：复杂的社交网络、阴谋集团

```
角色 ←→ 参与 ←→ 事件
 ↓                    ↓
拥有                 发生于
 ↓                    ↓
物品 ←→ 存放于 ←→ 地点
```

### 实践示例：家族史数据库

想象一个跨越三百年的家族传奇数据库。这不是简单的家谱，而是一个充满秘密、诅咒和命运纠缠的叙事宇宙：

```yaml
tables:
  family_members:
    columns:
      - id: primary_key
      - name: string
      - birth_year: integer
      - death_year: integer (nullable)
      - generation: integer
      - branch: enum('主家', '东支', '西支', '北支')
      - gift: string ("特殊能力：预言、治愈、诅咒等")
      - curse_mark: boolean
    triggers:
      - on_birth: "检查是否继承家族诅咒"
      - on_death: "触发遗产分配和秘密解锁"
  
  secrets:
    columns:
      - id: primary_key
      - secret_content: encrypted_text
      - keeper_id: foreign_key(family_members)
      - discovery_condition: json
      - danger_level: integer(1-10)
      - related_curse_id: foreign_key(curses)
    examples:
      - "真正的继承人其实是..."
      - "诅咒的起源来自第一代的..."
      - "家族财富的真正来源是..."
  
  heirlooms:
    columns:
      - id: primary_key
      - item_name: string
      - description: text
      - current_owner_id: foreign_key(family_members)
      - original_owner_id: foreign_key(family_members)
      - curse_level: integer(0-10)
      - power_type: enum('保护', '诅咒', '预言', '召唤')
      - activation_condition: string
    special_rules:
      - "诅咒物品会影响持有者的命运"
      - "某些物品只能被特定血脉激活"
      - "物品可能有自己的意志"
  
  curses:
    columns:
      - id: primary_key
      - curse_name: string
      - origin_story: text
      - affected_branches: array
      - breaking_condition: json
      - manifestation_pattern: string
    patterns:
      - "每三代必有一人早逝"
      - "长子永远无法继承"
      - "触碰某物必遭厄运"
  
  prophecies:
    columns:
      - id: primary_key
      - prophecy_text: text
      - prophet_id: foreign_key(family_members)
      - target_generation: integer
      - fulfillment_status: enum('未验证', '部分应验', '完全应验', '被打破')
      - interpretation_versions: json_array
```

#### 读者的探索方式

**1. 诅咒追踪器**
```sql
-- 追踪诅咒在家族中的传播路径
SELECT 
    fm.name,
    fm.generation,
    fm.branch,
    c.curse_name,
    cm.manifestation_date,
    cm.symptom
FROM family_members fm
JOIN curse_manifestations cm ON fm.id = cm.affected_member_id
JOIN curses c ON cm.curse_id = c.id
WHERE c.curse_name = '长子诅咒'
ORDER BY fm.generation, cm.manifestation_date;
```

**2. 秘密解锁系统**
```sql
-- 检查当前可解锁的秘密
SELECT 
    s.id,
    DECRYPT_HINT(s.secret_content, current_user_progress) as hint,
    s.discovery_condition
FROM secrets s
WHERE JSON_EXTRACT(s.discovery_condition, '$.type') = 'items_collected'
  AND JSON_EXTRACT(s.discovery_condition, '$.required_items') 
      SUBSET OF (SELECT item_id FROM user_inventory WHERE user_id = current_user)
  AND s.keeper_id IN (
      SELECT id FROM family_members 
      WHERE death_year < current_story_year
  );
```

**3. 命运模式识别**
```sql
-- 发现家族中的重复模式
WITH fate_patterns AS (
    SELECT 
        death_year - birth_year as lifespan,
        death_cause,
        generation,
        branch
    FROM family_members
    WHERE death_year IS NOT NULL
)
SELECT 
    branch,
    AVG(lifespan) as avg_lifespan,
    MODE(death_cause) as common_death_cause,
    COUNT(*) as sample_size
FROM fate_patterns
GROUP BY branch
HAVING sample_size > 5;
```

**4. 预言验证引擎**
```sql
-- 实时检查预言应验程度
SELECT 
    p.prophecy_text,
    p.target_generation,
    fm.name as possible_subject,
    CALCULATE_MATCH_SCORE(p.prophecy_text, fm.life_events) as match_score,
    SUGGEST_NEXT_EVENTS(p.prophecy_text, fm.current_state) as possible_futures
FROM prophecies p
CROSS JOIN family_members fm
WHERE fm.generation = p.target_generation
  AND fm.death_year IS NULL  -- 仍然在世
  AND p.fulfillment_status != '完全应验'
ORDER BY match_score DESC;
```

#### 动态叙事生成

基于查询结果，系统可以生成个性化的叙事片段：

```python
class FamilyNarrativeGenerator:
    def generate_discovery_scene(self, secret_id, discoverer_id):
        secret = self.db.get_secret(secret_id)
        discoverer = self.db.get_character(discoverer_id)
        keeper = self.db.get_character(secret.keeper_id)
        
        # 根据发现者与守密者的关系生成不同的叙事
        relationship = self.db.get_relationship(discoverer_id, keeper.id)
        
        if relationship.type == '直系后代':
            return f"{discoverer.name}在整理{keeper.name}的遗物时，"\
                   f"一个尘封的{secret.container}掉落在地。"\
                   f"颤抖的手打开它，里面的真相让血液都凝固了..."
        elif relationship.type == '宿敌后代':
            return f"复仇的执念引导{discoverer.name}找到了这个秘密。"\
                   f"{keeper.name}的罪行终于要大白于天下..."
```

## 3.2 Wiki模式：协作世界构建的艺术

### Wiki的叙事基因

Wiki不仅仅是一种技术，更是一种叙事哲学。它体现了几个革命性理念：

1. **去中心化创作**：没有单一作者，只有贡献者社区
2. **永恒的未完成性**：故事永远在生长、修正、演化
3. **透明的创作过程**：每次编辑都留下痕迹，过程即叙事
4. **民主化的知识架构**：读者可以成为作者，评论可以成为正文

### MediaWiki到现代知识库的演进

从最初的WikiWikiWeb到今天的各种平台，Wiki技术不断演化：

```
1995: WikiWikiWeb → 简单的超链接页面
         ↓
2001: Wikipedia → 中立观点、可验证性、协作规范
         ↓  
2007: Fandom → 粉丝驱动的虚构世界百科
         ↓
2020s: 现代知识图谱 → AI辅助、语义化、多媒体集成
```

### 条目关系网：超链接的叙事张力

在Wiki叙事中，超链接不仅是导航工具，更是叙事设备：

**链接类型与叙事功能：**

| 链接类型 | 叙事作用 | 示例 |
|---------|---------|------|
| 定义链接 | 世界观构建 | [[魔法体系]] → 解释运行规则 |
| 因果链接 | 情节推进 | [[大灾变]] → [[英雄崛起]] |
| 并列链接 | 视角扩展 | 同一事件的[[官方记录]]与[[民间传说]] |
| 时序链接 | 历史脉络 | [[第一纪元]] → [[第二纪元]] |
| 矛盾链接 | 叙事张力 | [[预言]]与[[实际结局]]的冲突 |

### 版本历史：时间维度的多重叙事

Wiki的版本控制系统创造了独特的"叙事考古学"：

```python
class WikiNarrative:
    def __init__(self, page_title):
        self.title = page_title
        self.versions = []
    
    def add_version(self, timestamp, author, content, edit_summary):
        version = {
            'time': timestamp,
            'author': author,
            'content': content,
            'summary': edit_summary,
            'diff': self.calculate_diff(content)
        }
        self.versions.append(version)
    
    def get_evolution_story(self):
        """版本历史本身成为一个元叙事"""
        story = []
        for i, v in enumerate(self.versions):
            if '争议' in v['summary']:
                story.append(f"第{i}次编辑引发社区辩论")
            if v['diff']['additions'] > 1000:
                story.append(f"{v['author']}贡献了重要扩展")
        return story
```

### 社区贡献：去中心化的世界观构建

成功的Wiki叙事需要精心设计的社区机制：

**1. 贡献者角色系统**
```yaml
roles:
  - 世界构建者: 创建新条目，定义基础设定
  - 编年史家: 维护时间线，确保历史一致性
  - 关系编织者: 创建和维护角色/事件关联
  - 考据学者: 添加引用，验证内容准确性
  - 调解员: 解决编辑冲突，维护中立性
```

**2. 编辑规范与叙事一致性**

```markdown
# 编辑指南示例

## 视角要求
- 使用第三人称全知视角
- 避免价值判断，保持中立语调
- 区分"官方记载"和"传闻"

## 时态规范
- 历史事件：过去时
- 世界设定：现在时
- 预言/可能：条件语气

## 剧透处理
- 使用折叠框{{Spoiler|关键情节}}
- 分离"基础信息"和"深度内容"
```

**3. 模板系统：结构化的创意**

```mediawiki
{{角色信息框
|名称 = 时间守护者
|首次登场 = [[纪元之初]]
|能力 = 时间操控、预知
|阵营 = {{争议|中立|混沌}}
|关键事件 = 
* [[时间大战]]
* [[永恒议会成立]]
|相关角色 = [[空间编织者]]（宿敌）
}}
```

### 协作冲突与叙事深度

Wiki的编辑战（Edit Wars）反而能产生叙事深度。当不同的贡献者对同一事件有不同解释时，这种"冲突"本身就成为了叙事的一部分。

**案例研究：SCP基金会的"多重Canon"系统**

SCP基金会是这种协作冲突转化为叙事深度的典范：

```yaml
SCP-001提案体系:
  meta_narrative: "真相被分散在多个互相矛盾的文件中"
  
  proposals:
    - 守门者:
        author: "Dr. Clef"
        concept: "天使实体守卫伊甸园"
        believers: 15000+
        contradicts: ["工厂", "破碎之神"]
    
    - 工厂:
        author: "AdminBright"
        concept: "制造异常的超维工厂"
        believers: 12000+
        implies: "所有SCP都是被制造的"
    
    - 数据库:
        author: "S Andrew Swann"
        concept: "SCP基金会意识到自己是虚构的"
        meta_level: 4
        breaks_fourth_wall: true
  
  community_response:
    - "没有官方真相，每个提案都可能是真的"
    - "或许所有提案都是故意的误导"
    - "真正的SCP-001可能还未被提出"
```

#### 冲突管理机制

**1. 讨论页作为元叙事空间**

```mediawiki
== 关于时间线的争议 ==

[[User:TimeKeeper]]: 根据[[Event:Great_War]]的描述，这件事应该发生在第三纪元。
: [[User:ChronoMaster]]: 但[[Book:Ancient_Prophecy]]明确指出是第二纪元末。
:: [[User:LoreMaster]]: 我建议创建[[Theory:时间线分歧]]页面，让两种解释共存。
::: [[User:WikiAdmin]]: 同意。这种模糊性反而增加了叙事深度。

{{EditConflictResolution|
  solution = "创建多重时间线理论页面"
  outcome = "让读者自己选择相信哪个版本"
}}
```

**2. 版本分支系统**

```python
class WikiNarrativeBranching:
    def handle_edit_conflict(self, page, conflicting_edits):
        # 不是选择一个，而是创建平行版本
        branches = []
        
        for edit in conflicting_edits:
            branch = {
                'version': f"{page.title}/Version_{edit.author}",
                'content': edit.content,
                'supporters': [],
                'evidence': edit.cited_sources,
                'implications': self.analyze_narrative_impact(edit)
            }
            branches.append(branch)
        
        # 创建索引页
        index_page = f"""
        == {page.title} - 多重解释 ==
        
        本条目存在{len(branches)}种互相竞争的解释：
        
        {self.generate_branch_comparison_table(branches)}
        
        === 选择你的真相 ===
        * [[{branches[0]['version']}|{branches[0]['summary']}]]
        * [[{branches[1]['version']}|{branches[1]['summary']}]]
        
        {{ReaderPoll|question="你相信哪个版本？"}}
        """
        
        return index_page
```

**3. 协作写作的涌现机制**

```javascript
// 实时协作编辑中的叙事融合
class CollaborativeNarrative {
  constructor() {
    this.activeEditors = new Map();
    this.narrativeThreads = [];
  }
  
  onSimultaneousEdit(edit1, edit2) {
    // 检测叙事协同效应
    if (this.detectSynergy(edit1, edit2)) {
      // 两个编辑互相增强
      return {
        result: 'merge',
        newContent: this.weaveNarratives(edit1, edit2),
        notification: '你们的编辑产生了美妙的叙事共鸣！'
      };
    } else if (this.detectConflict(edit1, edit2)) {
      // 冲突但有趣
      return {
        result: 'fork',
        branches: [edit1, edit2],
        notification: '你们创造了一个叙事分歧点！'
      };
    }
  }
  
  detectSynergy(edit1, edit2) {
    // 例：一人编辑角色背景，一人编辑相关事件
    const topics1 = this.extractTopics(edit1);
    const topics2 = this.extractTopics(edit2);
    const overlap = topics1.filter(t => topics2.includes(t));
    
    return overlap.length > 0 && 
           edit1.section !== edit2.section;
  }
  
  weaveNarratives(edit1, edit2) {
    // 智能合并两个编辑，创造更丰富的叙事
    return `
      ${edit1.content}
      
      <!-- 协同编辑标记 -->
      {{Collaboration|${edit1.author}|${edit2.author}}}
      
      ${edit2.content}
      
      === 编辑者笔记 ===
      <div class="editor-notes">
      ${edit1.author}: ${edit1.summary}
      ${edit2.author}: ${edit2.summary}
      
      这两个编辑互相补充，共同构建了更完整的图景。
      </div>
    `;
  }
}
```

### 实现Wiki叙事的技术栈

构建一个现代Wiki叙事平台需要精心设计的技术架构：

```javascript
// 现代Wiki叙事平台架构
const WikiNarrativeStack = {
  frontend: {
    framework: 'Vue.js/React',
    editor: 'TinyMCE/ProseMirror',
    visualization: 'D3.js/Cytoscape.js',
    features: {
      '实时预览': 'Markdown + 自定义模板解析',
      '版本对比': 'diff-match-patch',
      '关系图谱': 'Force-directed graph',
      '语义搜索': 'Algolia/MeiliSearch'
    }
  },
  
  backend: {
    cms: {
      base: 'MediaWiki/DokuWiki',
      extensions: [
        'SemanticMediaWiki', // 语义化数据
        'Cargo', // 结构化数据存储
        'PageForms', // 表单化编辑
      ]
    },
    database: {
      primary: 'PostgreSQL', // 主数据
      graph: 'Neo4j', // 关系数据
      cache: 'Redis', // 缓存层
      search: 'Elasticsearch' // 全文搜索
    },
    apis: {
      rest: 'Express.js/FastAPI',
      graphql: 'Apollo Server',
      websocket: 'Socket.io'
    }
  },
  
  narrativeFeatures: {
    realtime: {
      collaboration: 'OT/CRDT算法',
      notifications: 'Server-Sent Events',
      presence: '在线编辑者状态'
    },
    
    ai: {
      contentGeneration: 'GPT-4 API',
      consistency: '叙事一致性检查',
      suggestions: '内容补全建议',
      translation: '多语言同步'
    },
    
    gamification: {
      achievements: [
        '初级编辑者', '知识守护者', '世界构建师'
      ],
      reputation: '贡献积分系统',
      privileges: '权限解锁机制'
    },
    
    analytics: {
      userBehavior: '编辑模式分析',
      contentGrowth: '知识图谱增长',
      narrativeFlow: '读者路径分析'
    }
  },
  
  deployment: {
    containerization: 'Docker + Kubernetes',
    cdn: 'CloudFlare/Fastly',
    monitoring: 'Prometheus + Grafana',
    backup: '增量备份 + 版本快照'
  }
};
```

#### 实战案例：构建一个微型Wiki叙事

```python
# 使用Flask + SQLAlchemy快速搭建
from flask import Flask, render_template, request
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime
import difflib

app = Flask(__name__)
db = SQLAlchemy(app)

class Page(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(200), unique=True)
    content = db.Column(db.Text)
    
    # 叙事元数据
    narrative_type = db.Column(db.String(50))  # character, event, location
    importance = db.Column(db.Integer, default=5)
    
    # 版本控制
    versions = db.relationship('PageVersion', backref='page')
    
    # 关系
    links_to = db.relationship('PageLink', 
                               foreign_keys='PageLink.from_page_id')
    linked_from = db.relationship('PageLink', 
                                  foreign_keys='PageLink.to_page_id')

class PageVersion(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    page_id = db.Column(db.Integer, db.ForeignKey('page.id'))
    content = db.Column(db.Text)
    author = db.Column(db.String(100))
    timestamp = db.Column(db.DateTime, default=datetime.utcnow)
    edit_summary = db.Column(db.String(500))
    
    # 叙事影响分析
    narrative_impact = db.Column(db.JSON)
    # 例: {"introduces": ["new_character"], "resolves": ["conflict_x"]}

class PageLink(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    from_page_id = db.Column(db.Integer, db.ForeignKey('page.id'))
    to_page_id = db.Column(db.Integer, db.ForeignKey('page.id'))
    link_type = db.Column(db.String(50))  # reference, causation, conflict
    
@app.route('/page/<title>/edit', methods=['GET', 'POST'])
def edit_page(title):
    page = Page.query.filter_by(title=title).first_or_404()
    
    if request.method == 'POST':
        new_content = request.form['content']
        
        # 检测叙事冲突
        conflicts = detect_narrative_conflicts(page, new_content)
        if conflicts:
            return render_template('resolve_conflicts.html', 
                                   conflicts=conflicts)
        
        # 创建新版本
        version = PageVersion(
            page_id=page.id,
            content=new_content,
            author=request.form['author'],
            edit_summary=request.form['summary'],
            narrative_impact=analyze_narrative_impact(page, new_content)
        )
        
        db.session.add(version)
        page.content = new_content
        db.session.commit()
        
        # 触发叙事事件
        trigger_narrative_events(page, version)
        
    return render_template('edit.html', page=page)

def detect_narrative_conflicts(page, new_content):
    """检测叙事逻辑冲突"""
    conflicts = []
    
    # 检查时间线冲突
    if '死亡' in new_content and page.narrative_type == 'character':
        future_events = db.session.query(Page).join(PageLink).filter(
            PageLink.from_page_id == page.id,
            Page.content.contains('参与')
        ).all()
        
        if future_events:
            conflicts.append({
                'type': 'timeline',
                'message': '该角色在后续事件中仍有出现',
                'affected_pages': future_events
            })
    
    return conflicts
```

### Wiki叙事的未来：去中心化与区块链

```solidity
// 基于区块链的Wiki叙事智能合约
pragma solidity ^0.8.0;

contract DecentralizedWikiNarrative {
    struct Page {
        string content;
        address author;
        uint256 timestamp;
        uint256 consensusScore;
        mapping(address => bool) validators;
    }
    
    mapping(string => Page[]) public pages;  // 标题 => 版本历史
    mapping(address => uint256) public editorReputation;
    
    event PageEdited(string title, address author, uint256 version);
    event ConflictResolved(string title, uint256 winningVersion);
    
    function editPage(string memory title, string memory content) public {
        require(editorReputation[msg.sender] >= 10, "声望不足");
        
        Page storage newVersion = pages[title].push();
        newVersion.content = content;
        newVersion.author = msg.sender;
        newVersion.timestamp = block.timestamp;
        
        emit PageEdited(title, msg.sender, pages[title].length - 1);
    }
    
    function validateEdit(string memory title, uint256 version) public {
        require(editorReputation[msg.sender] >= 50, "无验证权限");
        
        pages[title][version].validators[msg.sender] = true;
        pages[title][version].consensusScore += editorReputation[msg.sender];
        
        // 自动解决版本冲突
        if (pages[title][version].consensusScore > 1000) {
            resolveToCanonical(title, version);
        }
    }
}
```

## 3.3 知识图谱：实体关系驱动的叙事架构

### 从关系数据库到知识图谱

知识图谱将叙事推向了语义层面。不同于关系数据库的表格结构，知识图谱直接表达实体间的语义关系：

```
关系数据库思维：
人物表 ←外键→ 事件表 ←外键→ 地点表

知识图谱思维：
(人物)-[参与]->(事件)-[发生于]->(地点)
   ↓            ↓           ↓
[认识]      [导致]      [连接]
   ↓            ↓           ↓
(人物)      (事件)      (地点)
```

### 三元组：叙事的原子单位

知识图谱的基础是RDF三元组（主语-谓语-宾语）：

```turtle
# 基础叙事三元组
:哈利波特 :打败了 :伏地魔 .
:伏地魔 :杀死了 :哈利的父母 .
:哈利的父母 :保护了 :哈利波特 .

# 时空标注
:决战 :发生于 :霍格沃茨 ;
      :时间 "1998-05-02" .

# 条件关系
:老魔杖 :属于 :打败其主人者 .
```

### 本体设计：定义叙事宇宙的规则

本体（Ontology）是知识图谱的"宪法"，定义了什么样的关系是合法的。一个精心设计的本体能够：

1. **约束叙事逻辑**：防止出现矛盾或不合理的情节
2. **启发新发现**：通过规则推理出隐含的关系
3. **支持多重解释**：允许同一事实的不同视角

```yaml
# 叙事本体示例：奇幻世界设定
namespace: http://narrative.example/fantasy#
version: 2.0

classes:
  Character:
    description: "叙事中的有意识实体"
    properties:
      - name: 
          type: string
          required: true
          unique: true
      - birthDate: 
          type: date
          constraints: "must be before current_story_time"
      - deathDate:
          type: date
          constraints: "must be after birthDate"
      - abilities: 
          type: array[Ability]
          maxItems: 5  # 限制能力数量，防止"玛丽苏"
      - affiliation: 
          type: Organization
          cardinality: "0..*"  # 可以属于多个组织
      - moralAlignment:
          type: enum["lawful_good", "chaotic_evil", ...]
    
    rules:
      - name: "location_exclusivity"
        sparql: |
          FILTER NOT EXISTS {
            ?character :locatedAt ?place1 .
            ?character :locatedAt ?place2 .
            ?place1 :mutuallyExclusiveWith ?place2 .
            FILTER(?place1 != ?place2)
          }
      
      - name: "death_prevents_action"
        condition: "IF character.deathDate < event.date"
        restriction: "character cannot be event.performer"
        exception: "UNLESS character has ability:resurrection"
      
      - name: "power_balance"
        description: "防止角色过于强大"
        formula: "SUM(ability.powerLevel) <= character.level * 10"
  
  Event:
    description: "故事中的重要事件"
    properties:
      - name:
          type: string
          required: true
      - participants: 
          type: array[Character]
          minItems: 1
      - location: 
          type: Place
          required: true
      - timeRange:
          type: object
          properties:
            start: datetime
            end: datetime
            precision: enum["exact", "day", "month", "year", "era"]
      - consequences: 
          type: array[Event]
          description: "直接导致的后续事件"
      - narrativeWeight:
          type: float
          range: [0, 10]
          description: "事件在叙事中的重要程度"
    
    rules:
      - name: "temporal_causality"
        sparql: |
          # 因果关系必须遵循时间顺序
          ?event1 :causes ?event2 .
          ?event1 :endTime ?time1 .
          ?event2 :startTime ?time2 .
          FILTER(?time1 <= ?time2)
      
      - name: "participant_lifespan"
        description: "参与者必须在事件期间存活"
        validation: |
          FOR each participant IN event.participants:
            participant.birthDate <= event.timeRange.start AND
            (participant.deathDate IS NULL OR 
             participant.deathDate >= event.timeRange.end)
      
      - name: "butterfly_effect"
        description: "重大事件必须有后续影响"
        condition: "IF event.narrativeWeight > 7"
        requirement: "event.consequences.length > 0"

  Place:
    description: "叙事发生的地点"
    properties:
      - name: string
      - coordinates: 
          type: object
          properties:
            x: float
            y: float
            z: float  # 支持多维空间
            dimension: string  # 平行世界/梦境/精神空间
      - accessRules:
          type: array[AccessRule]
          description: "进入此地的条件"
      - properties:
          type: array[PlaceProperty]
          examples: ["魔法禁区", "时间流速异常", "重力颠倒"]

relationships:
  # 角色间关系
  - LOVES:
      domain: Character
      range: Character
      properties: 
        since: date
        intensity: float[0-1]
        type: enum["romantic", "platonic", "familial", "obsessive"]
      symmetry: false  # 爱可以是单向的
      rules:
        - "不能爱自己（除非有narcissist属性）"
  
  - RIVAL_OF:
      domain: Character
      range: Character
      symmetry: true  # 竞争关系是双向的
      transitivity: false  # A的竞争对手B，B的竞争对手C，但A和C未必
  
  # 事件关系
  - CAUSES:
      domain: Event
      range: Event
      properties:
        probability: float[0-1]  # 因果关系的确定性
        delay: duration  # 原因和结果之间的时间间隔
      transitivity: true  # A导致B，B导致C，则A间接导致C
      rules:
        - "temporal_order: cause.endTime <= effect.startTime"
  
  - PREVENTS:
      domain: Event
      range: Event
      description: "一个事件的发生阻止了另一个事件"
      properties:
        effectiveness: float[0-1]
  
  # 地点关系  
  - CONNECTED_TO:
      domain: Place
      range: Place
      properties:
        distance: float
        travelTime: duration
        method: enum["walk", "portal", "teleport", "dream"]
      symmetry: false  # 可能是单向通道

# 推理规则
inferenceRules:
  - name: "enemy_of_enemy"
    description: "敌人的敌人可能是朋友"
    sparql: |
      CONSTRUCT { ?a :potentialAlly ?c }
      WHERE {
        ?a :enemyOf ?b .
        ?b :enemyOf ?c .
        FILTER(?a != ?c)
      }
  
  - name: "love_triangle_detection"
    description: "自动检测三角恋关系"
    sparql: |
      CONSTRUCT { 
        ?love_triangle a :LoveTriangle ;
                       :involves ?a, ?b, ?c .
      }
      WHERE {
        ?a :loves ?b .
        ?b :loves ?c .
        ?c :loves ?a .
        FILTER(?a != ?b && ?b != ?c && ?a != ?c)
      }

# 约束校验
constraints:
  - name: "no_paradoxes"
    description: "防止时间悖论"
    check: |
      NOT EXISTS {
        ?event1 :causes ?event2 .
        ?event2 :causes ?event1 .
      }
  
  - name: "conservation_of_characters"
    description: "角色不能凭空消失"
    check: |
      IF character.lastSeenAt != NULL AND character.deathDate == NULL
      THEN EXISTS { ?event :explains character.disappearance }
```

### 推理引擎：自动生成的情节连接

知识图谱的强大之处在于推理能力：

```python
# 推理规则示例
rules = [
    # 传递性关系
    "(?x :是父母 ?y) ∧ (?y :是父母 ?z) → (?x :是祖父母 ?z)",
    
    # 对称性关系
    "(?x :是兄弟姐妹 ?y) → (?y :是兄弟姐妹 ?x)",
    
    # 复仇动机推理
    "(?x :杀死了 ?y) ∧ (?z :爱 ?y) → (?z :有动机报复 ?x)",
    
    # 联盟推理
    "(?x :是敌人 ?y) ∧ (?z :是敌人 ?y) → (?x :潜在盟友 ?z)"
]

# 推理新关系
def infer_hidden_plots(knowledge_graph, rules):
    new_triples = []
    for rule in rules:
        matches = knowledge_graph.match_pattern(rule.condition)
        for match in matches:
            new_triple = rule.apply(match)
            new_triples.append(new_triple)
    return new_triples
```

### SPARQL查询：读者的编程式探索

让读者通过查询语言探索故事世界：

```sparql
# 查询：找出所有复仇链条
SELECT ?avenger ?victim ?killer WHERE {
  ?killer :杀死了 ?victim .
  ?avenger :爱 ?victim .
  ?avenger :执行了 ?revenge_act .
  ?revenge_act :针对 ?killer .
  ?revenge_act :发生时间 ?time .
  FILTER(?time > ?victim_death_time)
}

# 查询：发现隐藏的血缘关系
SELECT ?person1 ?person2 ?relationship WHERE {
  ?person1 :有共同祖先 ?ancestor .
  ?person2 :有共同祖先 ?ancestor .
  FILTER(?person1 != ?person2)
  BIND(
    IF(?ancestor :世代 <= 2, "表亲",
    IF(?ancestor :世代 <= 4, "远亲", "同族"))
    AS ?relationship
  )
}
```

### 语义丰富性：同一事实的多重表达

知识图谱的强大之处在于它能够捕捉同一事实的多重维度和解释。这种语义丰富性不仅让叙事更加立体，还为读者提供了多种探索路径。

#### 多层次语义标注

```turtle
@prefix : <http://narrative.example/> .
@prefix view: <http://narrative.example/viewpoint/> .
@prefix meta: <http://narrative.example/meta/> .
@prefix emotion: <http://narrative.example/emotion/> .

# 核心事实：背叛事件
:背叛事件_001 a :Event ;
    :name "犹大之吻" ;
    :timestamp "0033-04-14T21:00:00" ;
    :certainty 0.95 .

# 基本事实层
:犹大 :执行了 :背叛事件_001 ;
      :方式 :亲吻暗号 ;
      :地点 :客西马尼园 ;
      :时间 "0033-04-14T21:00:00" ;
      :结果 :耶稣被捕 .

# 动机层（多重可能性）
:背叛事件_001 :hasMotive [
    a :FinancialMotive ;
    :amount "30枚银币" ;
    :probability 0.6
] , [
    a :PoliticalMotive ;
    :description "对耶稣政治路线的失望" ;
    :probability 0.3
] , [
    a :DivineMotive ;
    :description "宿命的安排" ;
    :probability 0.1
] .

# 视角层（不同参与者的解读）
:犹大 view:interprets :背叛事件_001 [
    view:interpretation "我被迫做出选择，这是命运的安排" ;
    view:guilt_level 0.7 ;
    emotion:primary "regret" ;
    emotion:secondary "fear"
] .

:彼得 view:interprets :背叛事件_001 [
    view:interpretation "不可饶恕的背叛！" ;
    view:anger_level 0.9 ;
    emotion:primary "betrayal" ;
    emotion:secondary "rage"
] .

:约翰 view:interprets :背叛事件_001 [
    view:interpretation "我早就预感到会发生这事" ;
    view:acceptance_level 0.8 ;
    emotion:primary "sadness" ;
    emotion:secondary "understanding"
] .

# 元叙事层（文学分析）
:背叛事件_001 meta:hasNarrativeFunction [
    a meta:PlotDevice ;
    meta:type meta:Climax ;
    meta:tension_level 9.5 ;
    meta:triggers :救赎叙事线
] .

:背叛事件_001 meta:hasArchetype [
    a meta:BetrayalArchetype ;
    meta:variant "内部背叛" ;
    meta:culturalResonance [
        meta:culture "西方" ;
        meta:significance "极高" ;
        meta:symbolizes "信任的脆弱性"
    ]
] .

# 因果链层
:背叛事件_001 :causes :耶稣被捕 ;
                :causes :彼得三次不认主 ;
                :causes :犹大自缢 ;
                :indirectlyCauses :基督教诞生 .

# 象征层
:亲吻 meta:symbolizes [
    meta:symbol "亲密关系" ;
    meta:irony_level "extreme" ;
    meta:description "用最亲密的动作执行最残酷的背叛"
] .

:三十枚银币 meta:symbolizes [
    meta:symbol "奴隶的价格" ;
    meta:biblical_reference "Exodus 21:32" ;
    meta:meaning "人的价值被贬低到物质"
] .
```

#### 时态与不确定性的表达

```python
class TemporalNarrative:
    """处理叙事中的时态和不确定性"""
    
    def add_event_with_uncertainty(self, graph, event_data):
        # 基础事件
        event = URIRef(f"event_{event_data['id']}")
        graph.add((event, RDF.type, NARRATIVE.Event))
        
        # 时间不确定性
        if event_data.get('time_precision') == 'approximate':
            time_node = BNode()
            graph.add((event, NARRATIVE.occurredAt, time_node))
            graph.add((time_node, NARRATIVE.earliest, 
                      Literal(event_data['earliest_time'])))
            graph.add((time_node, NARRATIVE.latest, 
                      Literal(event_data['latest_time'])))
            graph.add((time_node, NARRATIVE.mostLikely, 
                      Literal(event_data['estimated_time'])))
        
        # 真实性程度
        certainty_node = BNode()
        graph.add((event, NARRATIVE.certainty, certainty_node))
        graph.add((certainty_node, NARRATIVE.factual, 
                  Literal(event_data.get('factual_certainty', 0.5))))
        graph.add((certainty_node, NARRATIVE.source, 
                  Literal(event_data.get('source', 'unknown'))))
        
        # 多重版本
        for version in event_data.get('versions', []):
            version_node = URIRef(f"event_{event_data['id']}_v{version['id']}")
            graph.add((event, NARRATIVE.hasVersion, version_node))
            graph.add((version_node, NARRATIVE.according_to, 
                      Literal(version['source'])))
            graph.add((version_node, NARRATIVE.description, 
                      Literal(version['description'])))
            
        return event

# 使用示例
narrative = TemporalNarrative()
event_data = {
    'id': 'battle_001',
    'name': '传说中的决战',
    'time_precision': 'approximate',
    'earliest_time': '1200-01-01',
    'latest_time': '1300-12-31',
    'estimated_time': '1250-06-15',
    'factual_certainty': 0.3,
    'versions': [
        {
            'id': 1,
            'source': '官方史书',
            'description': '英雄单枪匹马击败恶龙'
        },
        {
            'id': 2,
            'source': '民间传说',
            'description': '村民合力驱赶了恶龙'
        },
        {
            'id': 3,
            'source': '考古发现',
            'description': '可能只是一场大型风暴'
        }
    ]
}
```

### 知识图谱可视化：故事的全景图

可视化是让知识图谱真正"活起来"的关键。通过图形化展示，读者可以直观地看到故事世界的全貌和关系网络。

#### 交互式叙事图谱

```javascript
// 使用Cytoscape.js构建交互式叙事网络
class InteractiveNarrativeGraph {
  constructor(container) {
    this.cy = cytoscape({
      container: container,
      style: this.getStylesheet(),
      layout: { name: 'cose-bilkent' }
    });
    
    this.setupInteractivity();
    this.loadNarrativeData();
  }
  
  getStylesheet() {
    return [
      // 角色节点样式
      {
        selector: 'node[type="character"]',
        style: {
          'shape': 'ellipse',
          'background-color': '#4A90E2',
          'label': 'data(name)',
          'width': 'mapData(importance, 1, 10, 30, 100)',
          'height': 'mapData(importance, 1, 10, 30, 100)',
          'font-size': 'mapData(importance, 1, 10, 10, 20)',
          'border-width': 3,
          'border-color': '#2E5C8A'
        }
      },
      
      // 事件节点样式
      {
        selector: 'node[type="event"]',
        style: {
          'shape': 'diamond',
          'background-color': 'mapData(tension, 0, 10, #90EE90, #DC143C)',
          'label': 'data(name)',
          'width': 60,
          'height': 60
        }
      },
      
      // 地点节点样式
      {
        selector: 'node[type="place"]',
        style: {
          'shape': 'rectangle',
          'background-color': '#8B7355',
          'label': 'data(name)',
          'text-valign': 'center',
          'text-halign': 'center'
        }
      },
      
      // 关系边样式
      {
        selector: 'edge',
        style: {
          'curve-style': 'bezier',
          'target-arrow-shape': 'triangle',
          'line-color': 'mapData(strength, 0, 1, #CCCCCC, #333333)',
          'width': 'mapData(strength, 0, 1, 1, 5)',
          'label': 'data(relation)',
          'font-size': 10,
          'text-opacity': 0.7
        }
      },
      
      // 高亮样式
      {
        selector: '.highlighted',
        style: {
          'background-color': '#FFD700',
          'line-color': '#FFD700',
          'z-index': 999
        }
      },
      
      // 模糊样式（未选中时）
      {
        selector: '.faded',
        style: {
          'opacity': 0.25
        }
      }
    ];
  }
  
  setupInteractivity() {
    // 点击节点显示详细信息
    this.cy.on('tap', 'node', (evt) => {
      const node = evt.target;
      this.showNodeDetails(node);
      this.highlightNeighborhood(node);
    });
    
    // 悬停显示关系
    this.cy.on('mouseover', 'edge', (evt) => {
      const edge = evt.target;
      this.showRelationshipTooltip(edge);
    });
    
    // 双击展开隐藏关系
    this.cy.on('dbltap', 'node', (evt) => {
      const node = evt.target;
      this.revealHiddenConnections(node);
    });
    
    // 右键过滤
    this.cy.on('cxttap', 'node', (evt) => {
      const node = evt.target;
      this.showFilterMenu(node);
    });
  }
  
  highlightNeighborhood(node) {
    // 重置所有元素
    this.cy.elements().removeClass('highlighted faded');
    
    // 高亮选中节点及其邻居
    const neighborhood = node.closedNeighborhood();
    const others = this.cy.elements().not(neighborhood);
    
    neighborhood.addClass('highlighted');
    others.addClass('faded');
  }
  
  showNodeDetails(node) {
    const data = node.data();
    const details = this.generateNodeDetails(data);
    
    // 创建信息面板
    const panel = document.getElementById('detail-panel');
    panel.innerHTML = `
      <h3>${data.name}</h3>
      <p>类型: ${data.type}</p>
      ${details}
      <button onclick="queryRelated('${data.id}')">查询相关信息</button>
    `;
    
    // 触发 SPARQL 查询获取更多信息
    this.fetchAdditionalData(data.id);
  }
  
  async fetchAdditionalData(nodeId) {
    const query = `
      SELECT ?property ?value WHERE {
        <${nodeId}> ?property ?value .
        FILTER(?property != rdf:type)
      }
      LIMIT 20
    `;
    
    const results = await this.sparqlQuery(query);
    this.updateDetailPanel(results);
  }
  
  revealHiddenConnections(node) {
    // 查询隐藏的关系
    const query = `
      SELECT ?target ?relation ?hidden_since WHERE {
        <${node.id()}> ?relation ?target .
        ?relation :hidden true ;
                  :revealCondition ?condition .
        FILTER(checkCondition(?condition))
      }
    `;
    
    this.sparqlQuery(query).then(results => {
      results.forEach(result => {
        // 添加新发现的边
        this.cy.add({
          data: {
            source: node.id(),
            target: result.target,
            relation: result.relation,
            newly_revealed: true
          },
          classes: 'newly-revealed'
        });
      });
      
      // 重新布局
      this.cy.layout({ name: 'cose-bilkent' }).run();
      
      // 动画效果
      this.animateReveal();
    });
  }
  
  loadNarrativeData() {
    // 从知识图谱加载数据
    const nodesQuery = `
      SELECT ?id ?name ?type ?importance WHERE {
        ?id a ?type ;
            :name ?name ;
            :narrativeImportance ?importance .
        FILTER(?type IN (:Character, :Event, :Place))
      }
    `;
    
    const edgesQuery = `
      SELECT ?source ?target ?relation ?strength WHERE {
        ?source ?relation ?target .
        ?relation :narrativeStrength ?strength .
        FILTER(?relation != rdf:type)
      }
    `;
    
    Promise.all([
      this.sparqlQuery(nodesQuery),
      this.sparqlQuery(edgesQuery)
    ]).then(([nodes, edges]) => {
      this.cy.add([
        ...nodes.map(n => ({ data: n })),
        ...edges.map(e => ({ data: e }))
      ]);
      
      this.cy.layout({ name: 'cose-bilkent' }).run();
    });
  }
}

// 时间线可视化
class TemporalNarrativeVisualization {
  constructor(container) {
    this.timeline = new vis.Timeline(container);
    this.setupTimeline();
  }
  
  setupTimeline() {
    // 配置时间线
    const options = {
      width: '100%',
      height: '400px',
      margin: { item: 20 },
      stack: true,
      showCurrentTime: false,
      
      // 自定义时间格式
      format: {
        minorLabels: {
          year: 'YYYY',
          month: 'MMM',
          day: 'D'
        }
      },
      
      // 分组显示
      groupOrder: (a, b) => a.order - b.order,
      
      // 交互设置
      editable: {
        add: false,
        updateTime: false,
        updateGroup: false,
        remove: false
      },
      
      // 工具提示
      tooltip: {
        followMouse: true,
        overflowMethod: 'cap'
      }
    };
    
    this.timeline.setOptions(options);
  }
  
  loadNarrativeEvents() {
    const query = `
      SELECT ?event ?name ?start ?end ?type ?importance WHERE {
        ?event a :Event ;
               :name ?name ;
               :startTime ?start ;
               :narrativeImportance ?importance .
        OPTIONAL { ?event :endTime ?end }
        OPTIONAL { ?event :eventType ?type }
      }
      ORDER BY ?start
    `;
    
    this.sparqlQuery(query).then(results => {
      const items = results.map(r => ({
        id: r.event,
        content: r.name,
        start: r.start,
        end: r.end || r.start,
        type: r.type || 'point',
        className: this.getEventClass(r.importance),
        title: this.generateTooltip(r)
      }));
      
      const groups = this.generateEventGroups(results);
      
      this.timeline.setGroups(groups);
      this.timeline.setItems(items);
    });
  }
  
  getEventClass(importance) {
    if (importance > 8) return 'critical-event';
    if (importance > 5) return 'major-event';
    return 'minor-event';
  }
}
```

#### 三维叙事空间

```javascript
// 使用Three.js创建3D叙事空间
class NarrativeSpace3D {
  constructor(container) {
    this.scene = new THREE.Scene();
    this.camera = new THREE.PerspectiveCamera(75, 
      window.innerWidth / window.innerHeight, 0.1, 1000);
    this.renderer = new THREE.WebGLRenderer();
    
    this.setupScene();
    this.loadNarrativeElements();
  }
  
  createCharacterNode(character) {
    const geometry = new THREE.SphereGeometry(
      character.importance * 2, 32, 32
    );
    
    const material = new THREE.MeshPhongMaterial({
      color: this.getCharacterColor(character),
      emissive: 0x444444,
      shininess: 100
    });
    
    const mesh = new THREE.Mesh(geometry, material);
    mesh.position.set(
      character.x || Math.random() * 100 - 50,
      character.y || Math.random() * 100 - 50,
      character.z || Math.random() * 100 - 50
    );
    
    // 添加标签
    const label = this.createTextLabel(character.name);
    label.position.copy(mesh.position);
    label.position.y += character.importance * 2.5;
    
    mesh.userData = character;
    this.scene.add(mesh);
    this.scene.add(label);
    
    return mesh;
  }
  
  createRelationshipLine(source, target, relationship) {
    const points = [
      source.position,
      target.position
    ];
    
    const geometry = new THREE.BufferGeometry().setFromPoints(points);
    const material = new THREE.LineBasicMaterial({
      color: this.getRelationshipColor(relationship),
      linewidth: relationship.strength * 3,
      opacity: 0.6,
      transparent: true
    });
    
    const line = new THREE.Line(geometry, material);
    line.userData = relationship;
    
    this.scene.add(line);
    return line;
  }
  
  animateNarrativeFlow() {
    // 创建叙事流动效果
    const clock = new THREE.Clock();
    
    const animate = () => {
      requestAnimationFrame(animate);
      
      const delta = clock.getDelta();
      const time = clock.getElapsedTime();
      
      // 角色节点脉动
      this.characterNodes.forEach(node => {
        const scale = 1 + Math.sin(time * 2) * 0.1;
        node.scale.set(scale, scale, scale);
      });
      
      // 关系线条流动
      this.relationshipLines.forEach(line => {
        line.material.opacity = 0.3 + Math.sin(time * 3) * 0.3;
      });
      
      this.renderer.render(this.scene, this.camera);
    };
    
    animate();
  }
}
```
```