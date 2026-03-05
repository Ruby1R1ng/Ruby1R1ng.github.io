# 写在开头

《The Complete Guide to Building Skills for Claude》是Claude官方发布的一个关于如何构建Skill的说明文档，这篇博客内容对文档的内容进行理解和二次加工，适合中文读者

原文档下载地址： https://claude.com/blog/complete-guide-to-building-skills-for-claude

# 一、什么是 Skill

一个 Skill 本质上是一个文件夹，里面通常包含这些内容：

The-Complete-Guide-to-Building-…

SKILL.md（必需）：主说明文件

scripts/（可选）：脚本代码，比如 Python、Bash

references/（可选）：参考文档

assets/（可选）：模板、字体、图标等资源

# 二、Skill 的三个核心设计思想

1）渐进式加载（Progressive Disclosure）

- YAML frontmatter（最前面的元数据）.   总是会被 Claude 看到，用来判断“什么时候该用这个 Skill”

- SKILL.md 正文.  当 Claude 觉得这个 Skill 相关时，才会加载

- 链接文件（references、其他附加文件）. 需要时再去看

2）可组合（Composability）

Claude 可以同时加载多个 Skill, 你做的 Skill 不能假设“自己是唯一的技能”，而要尽量设计成可以和别的 Skill 一起工作。

3）可移植（Portability）

同一个 Skill 可以在 Claude.ai、Claude Code、API 中使用。

# 三、如果你有 MCP，Skill 是什么关系

MCP 像专业厨房：提供工具、食材、设备, 解决的是：Claude 能做什么

Skill 像菜谱：告诉 Claude 该怎么做

所以两者配合最好：

MCP 提供工具连接（如 Notion、Linear、Asana）

Skill 提供标准流程和最佳实践

# 四、开始做 Skill 之前，要先想清楚“用例”

文档强调：先定义 2~3 个具体使用场景（use cases），再开始写。

比如它举的例子是 “项目冲刺规划（Sprint Planning）”：

- 用户说：“帮我规划这个 sprint”

- 然后 Skill 自动完成：
  - 拉取项目状态
  - 分析团队速度和容量
  - 给出任务优先级
  - 创建任务

你要问自己的几个问题：
- 用户真正想完成什么？
- 这个目标需要哪些步骤？
- 要用哪些工具（内置工具 / MCP）？
- 哪些领域知识和经验要固化进去？

# 五、常见的 Skill 类型

你做 Skill 时，先判断自己属于哪一类。不同类别，写法和重点不一样。

1) 文档/资产生成类

用于创建高质量、风格统一的输出，比如：
- 文档
- 演示文稿
- 应用
- 设计稿
- 代码

特点:
- 内嵌风格规范
- 有模板
- 有质量检查清单
- 通常不依赖外部工具

2) 工作流自动化类

用于多步骤流程，比如 skill-creator 自己就是这个类型。

特点:
- 步骤清晰
- 有校验关卡
- 有模板
- 支持迭代修改

3) MCP 增强类

用于给已有 MCP 工具增加“流程化使用方法”。

特点:
- 多个 MCP 调用串联
- 带领域经验
- 减少用户自己描述流程的成本
- 能处理常见错误

## 六、怎么判断你的 Skill 做得好不好

文档给了两类标准：定量 和 定性。

### 定量指标
例如:
- 90% 的相关问题都能正确触发 Skill
- 工作流能在更少的工具调用内完成
- API 调用失败次数为 0

### 定性指标
例如:
- 用户不需要一步步教 Claude 接下来干什么
- 用户不用频繁纠正
- 不同会话里结果都比较一致

### 通俗讲解
核心目标就是三点:
- 该触发时能触发
- 执行时不出错
- 结果稳定

## 七、技术要求：文件结构怎么写

文档要求 Skill 文件夹结构大致如下：

- your-skill-name/
  - SKILL.md
  - scripts/
  - references/
  - assets/

### 非常关键的规则

文档明确说：

1.  **文件名必须是 SKILL.md**
    - 不能是 skill.md
    - 不能是 SKILL.MD

2.  **文件夹名必须是 kebab-case**
    - 对：`notion-project-setup`
    - 错：`Notion Project Setup`
    - 错：`notion_project_setup`
  
kebab-case（也叫烤串命名法 / 连字符命名法）是一种命名规则，核心特点：

全部小写：所有字符都是小写字母

连字符分隔：单词之间用短横线（-） 连接（这个短横线看起来像烤串的签子，所以叫 kebab-case）

其他常见命名法：

| 命名法      | 规则                     | 示例（以“项目设置”为例） |
|-------------|--------------------------|--------------------------|
| kebab-case  | 小写 + 短横线分隔        | `project-setup`          |
| camelCase   | 小驼峰（首字母小写）     | `projectSetup`           |
| PascalCase  | 大驼峰（首字母大写）     | `ProjectSetup`           |
| snake_case  | 小写 + 下划线分隔        | `project_setup`          |


3.  **Skill 文件夹里不要放 README.md**
    - 说明文档写进 SKILL.md 或 references/

## 八、最关键的部分：YAML frontmatter（前置元数据）

文档反复强调：
**YAML frontmatter 是最重要的部分，因为 Claude 靠它决定要不要加载这个 Skill。**

### 最小格式

```yaml
---
name: your-skill-name
description: What it does. Use when user asks to [specific phrases].
---
```

### 怎么写一个好的 description

一个公式：`[做什么] + [什么时候用] + [关键能力]`

#### 好的写法（特点）
- 具体
- 有触发词
- 说明价值

#### 差的写法（特点）
- 太泛
- 没有触发条件
- 只有技术描述，没有用户语言

#### 例如：
- 差：`Helps with projects.`
- 好：明确写出“当用户说 sprint、create tickets、project planning 时使用”

## 十、SKILL.md 正文怎么写

文档推荐的结构是：
- 标题
- Instructions（说明）
- Step 1 / Step 2 / Step 3
- Examples（示例）
- Troubleshooting（排错）

### 写作原则

文档强调：
- 要具体、可执行
- 要写错误处理
- 要明确引用 `references/` 中的资料
- 详细资料放到 `references/`，不要把 SKILL.md 写得太臃肿

## 十一、怎么测试 Skill

文档说可以有三种测试方式：

1.  在 Claude.ai 里手动测
2.  在 Claude Code 里脚本化测试
3.  通过 API 做程序化测试

### 推荐测试三方面

#### 1) 触发测试
看该不该触发时，能不能触发。

- 明显相关的问题要触发
- 换种说法也要触发
- 不相关问题不要触发

#### 2) 功能测试
看执行是否正确。

- 输出是否正确
- API 是否成功
- 报错处理是否正常
- 边界情况是否覆盖

#### 3) 性能对比
和“不用 Skill”相比是否更好。

例如文档给出的对比：

- 不用 Skill：来回很多轮、失败多、token 更多
- 用了 Skill：流程更自动、失败更少、token 更省

## 十二、如何根据反馈迭代

### 常见问题

#### 1) 触发不足 (Undertriggering)
**表现：**
- 该触发时不触发
- 用户得手动开

**解决：**
- 在 description 中加更多关键词和细节

#### 2) 触发过度 (Overtriggering)
**表现：**
- 不相关问题也触发

**解决：**
- 加负面触发条件
- 把适用范围写得更具体

#### 3) 执行问题
**表现：**
- 结果不一致
- API 失败
- 用户频繁纠正

**解决：**
- 改进说明
- 补错误处理

## 十三、如何分发和分享 Skill

文档介绍了当前的分发方式：

### 个人用户怎么安装

1.  下载 Skill 文件夹
2.  如有需要，压缩成 zip
3.  上传到 Claude.ai 的 Skills
4.  或放进 Claude Code 的 skills 目录

### 组织级部署

管理员可以把 Skill 部署给整个工作区，支持自动更新。

### 推荐做法

- 把 Skill 放到 GitHub
- 写清楚安装说明
- 在 MCP 文档中说明为什么 “MCP + Skill” 更有价值

## 十四、文档给出的 5 种常见设计模式

文档在后面总结了 5 种很常见的 Skill 模式：

1) **顺序工作流编排**
适合：必须按顺序执行的多步流程

2) **多 MCP 协同**
适合：要跨多个服务（如 Figma + Drive + Linear + Slack）

3) **迭代优化**
适合：输出需要反复检查和打磨的任务（如报告生成）

4) **上下文感知工具选择**
适合：同一个目标，但根据情况选择不同工具

5) **领域智能增强**
适合：不仅调用工具，还要加入专业规则（如合规、金融规则）

## 十五、常见报错与排查

### 1) 上传失败
**常见原因：**
- 找不到 `SKILL.md`
- YAML 格式错了
- skill 名字不合法

### 2) Skill 不触发
**常见原因：**
- description 太泛
- 没有触发词
- 没写相关文件类型

### 3) Skill 触发太频繁
**解决：**
- 加 “Do NOT use for …”
- 写得更具体
- 限定使用范围

### 4) MCP 连接失败
**排查：**
- 扩展是否连接
- API key 是否有效
- 单独测试 MCP 能不能调用
- 工具名是否写对（区分大小写）

### 5) 说明不生效
**常见原因：**
- 写太长
- 关键规则埋太深
- 语言模糊

**文档建议：**
- 把关键要求写在前面
- 用 `CRITICAL`
- 必要时用脚本做确定性校验

### 6) 上下文太大
**原因：**
- Skill 内容太长
- 同时启用太多 Skill

**解决：**
- `SKILL.md` 尽量精简
- 细节挪去 `references/`
- 不要同时开太多 Skill
