---
title：RAG学习笔记
---

# Codex Cookbook
《Doing RAG on PDFs using File Search in the Responses API》 文章链接：https://developers.openai.com/cookbook/examples/file_search_responses

TL;DR: 如何用 OpenAI Responses API 里的 file_search 工具，把一批 PDF 变成可检索的知识库，并测试检索效果。

三部分：

- 把本地 PDF 上传到 OpenAI 的向量存储（Vector Store）

- 基于这些 PDF 做检索和问答

- 构造一个小型评测集，评估检索效果（Recall、MRR、MAP 等）

## 一、背景：它想解决什么问题？

传统 RAG：
- 解析 PDF
- 把文本切块（chunking）
- 生成 embeddings（向量）
- 把向量存入数据库（vector DB）
- 查询时再检索相关块
- 把检索结果拼进 prompt，让模型回答

这里介绍的 `file_search` 工具，就是想把这些复杂步骤“托管”掉：
- 你只要上传文件到 OpenAI 的 Vector Store
- OpenAI 自动：
  - 读取 PDF
  - 分块
  - 向量化
  - 存储
  - 检索
- 你提问时，模型可以直接调用 `file_search` 来找相关内容并回答

核心价值：把传统 RAG 简化成一个 API 调用。

## 二、代码解读

1) 初始化客户端，读取本地 PDF 列表
```python
client = OpenAI(api_key=os.getenv('OPENAI_API_KEY'))
dir_pdfs = 'openai_blog_pdfs' # have those PDFs stored locally here
pdf_files = [os.path.join(dir_pdfs, f) for f in os.listdir(dir_pdfs)]
```
创建 OpenAI 客户端，读取本地文件夹 openai_blog_pdfs 中所有 PDF 文件路径

2) 创建 Vector Store，并上传 PDF
```python
def upload_single_pdf(file_path: str, vector_store_id: str):
    file_response = client.files.create(file=open(file_path, 'rb'), purpose="assistants")
    attach_response = client.vector_stores.files.create(
        vector_store_id=vector_store_id,
        file_id=file_response.id
    )
```
files.create：先把 PDF 上传成一个 OpenAI 文件对象

vector_stores.files.create：把这个文件纳入某个知识库（挂到指定的 vector_store 里）

```python
vector_store = client.vector_stores.create(name=store_name)
```
创建一个新的向量存储空间（即知识库）

## 三、直接做向量检索（不让模型回答）
```python
search_results = client.vector_stores.search(
    vector_store_id=vector_store_details['id'],
    query=query
)
```

系统会返回若干个相关文本块（chunks），每个结果包含：
- 来自哪个文件
- 该块文本内容
- 相关性分数 score

## 四、把检索和模型回答合成一次 API 调用
```python
response = client.responses.create(
    input=query,
    model="gpt-4o-mini",
    tools=[{
        "type": "file_search",
        "vector_store_ids": [vector_store_details['id']],
    }]
)
```
用户输入一个问题

模型使用 gpt-4o-mini

并且允许它调用 file_search

file_search 只在指定的 vector store 中查找
## 五、自动生成评测问题
1）先从 PDF 提取原文
```python
def extract_text_from_pdf(pdf_path):
    ...
    reader = PyPDF2.PdfReader(f)
    for page in reader.pages:
        page_text = page.extract_text()
```
用 PyPDF2 把每个 PDF 的文本提出来。

注意：

PyPDF2 只适合文本型 PDF

对扫描版、排版复杂的 PDF，效果可能一般

2）让模型根据每个 PDF 自动生成一个问题
```python
def generate_questions(pdf_path):
    text = extract_text_from_pdf(pdf_path)

    prompt = (
        "Can you generate a question that can only be answered from this document?:\n"
        f"{text}\n\n"
    )

    response = client.responses.create(
        input=prompt,
        model="gpt-4o",
    )
```
这里它把 整篇 PDF 文本塞给模型，然后要求模型：“生成一个只能通过这篇文档回答的问题”。这样做的目的，是为每篇文档构造一个“应该由它自己命中”的测试问题。

3）对所有 PDF 批量生成问题
```python
questions_dict = {}
for pdf_path in pdf_files:
    questions = generate_questions(pdf_path)
    questions_dict[os.path.basename(pdf_path)] = questions
```
最终得到一个字典，相当于一个简单评测集：
```
{
  "某个文件.pdf": "针对这个文件生成的问题",
  ...
}
```
## 六、检索评估：看系统能不能找对文件
1）构造评测行
```python
rows = []
for filename, query in questions_dict.items():
    rows.append({"query": query, "_id": filename.replace(".pdf", "")})
```
每一行相当于：

query：提问

_id：期望命中的文件名（去掉 .pdf）

2）核心评测函数 process_query
对每个 query 做以下事情：
```python
response = client.responses.create(
    input=query,
    model="gpt-4o-mini",
    tools=[{
        "type": "file_search",
        "vector_store_ids": [vector_store_details['id']],
        "max_num_results": k,
    }],
    tool_choice="required"
)
```
max_num_results = k，只取前 k 个结果。

tool_choice="required" 强制模型必须调用 file_search。

这很关键，因为这里是在测“检索效果”，不是测模型自由发挥能力。如果不强制，它可能直接靠常识回答，就没法公平评估检索了。

4）怎么判断“检索对了”？
它会把返回的注释里的文件名取出来：
```python
retrieved_files = [result.filename for result in annotations[:k]]
```
然后看：

期望文件是否在前 5 个里

如果在，是第几名

由此计算：是否命中、倒数排名分数、平均精度
## 七、评测指标解释
下面指标主要属于 信息检索（Information Retrieval, IR）/ 排序（ranking） 评估指标。

先定义：
*   **TP (True Positive)**：检索出来且确实相关
*   **FP (False Positive)**：检索出来但不相关
*   **FN (False Negative)**：没检索出来但其实相关

**1）Recall@k 召回率**

$$Recall = \frac{TP}{TP + FN}$$

$$
\text{Recall@k} = \frac{\text{前k个结果中相关文档数}}{\text{相关文档总数}}
$$

```python
recall_at_k = correct_retrievals_at_k / total_queries
```
检索出来的结果里，有多少是真的相关的：在所有问题里，有多少比例的问题，其正确文件出现在前 k 个检索结果中。

比如 Recall@5 = 0.9048，意思是：大约 90.48% 的问题，正确文档出现在前 5 个结果里。这是一个“有没有找到”的指标。

**2）Precision@k 准确率（在检索里也常叫查准率）**
$$Precision = \frac{TP}{TP + FP} \quad$$ 

$$
\text{Precision@k} = \frac{\text{前k个结果中相关文档数}}{k}
$$

所有相关文档里，你找回来了多少
```python
precision_at_k = recall_at_k
```
这其实是一个 简化写法，严格来说并不是真正标准的 Precision@k。因为这里每个 query 只假设有 一个相关文档，并且它只关心“这个文档是否出现在前 k 里”。
在这种特定设定下，它直接把 Precision@k 写成和 Recall@k 一样。

Precision 高：返回结果更“准”，垃圾少

Recall 高：漏掉的更少，找得更全

很多系统里两者是要权衡的：

返回很少但都很准 → Precision 高，Recall 可能低

返回很多把相关的都捞回来 → Recall 高，但 Precision 可能下降

**3）MRR（Mean Reciprocal Rank）平均倒数排名**

1）单个 query 的 Reciprocal Rank（RR）

```python
rr = 1 / rank
mrr = sum(reciprocal_ranks) / total_queries
```
如果正确文档排第 1，得分 = 1

排第 2，得分 = 1/2

排第 3，得分 = 1/3

没找到，得分 = 0

最后取平均。

它衡量的是：正确结果排得靠不靠前。MRR 越高越好，说明正确文档通常排得很前。

2）多个 query 的 MRR

$$
MRR = \frac{1}{Q} \sum_{i=1}^{Q} \frac{1}{rank_i}
$$

其中：
- \( Q \) 是查询总数
- \( rank_i \) 是第 \( i \) 个查询中第一个相关结果的位置

**4）Hit Rate / Hits@k**

前 k 个结果里是否至少命中一个相关项

单个 query：

命中则 1

否则 0

整体取平均。在“每个 query 只有一个正确答案”时非常常用

**5）DCG**

如果相关性不是简单的 0/1，而是有等级（比如 0,1,2,3），就常用它。

DCG: Discounted Cumulative Gain, 折损累计增益

$$
DCG@k = \sum_{i=1}^{k} \frac{2^{rel_i} - 1}{\log_2(i + 1)}
$$

含义：
- 排名越靠前，收益越大
- 高相关内容放前面加分更多

**6）NDCG**

NDCG：把 DCG 再除以理想排序下的 IDCG，归一化到 0~1：

$$
NDCG@k = \frac{DCG@k}{IDCG@k}
$$


```python
rr = 1 / rank
mrr = sum(reciprocal_ranks) / total_queries
```


```python
rr = 1 / rank
mrr = sum(reciprocal_ranks) / total_queries
```


```python
rr = 1 / rank
mrr = sum(reciprocal_ranks) / total_queries
```


