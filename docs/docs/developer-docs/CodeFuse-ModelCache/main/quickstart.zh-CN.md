---
store:
  title: CodeFuse-ModelCache
  version: main
group:
  title: 🌱 CodeFuse-ModelCache
  order: -1
title: 快速开始
order: 0
toc: content
---

ModelCache 易于使用，只需 1 步骤即可构建缓存测试 Demo

## 快速开始

### 构建 Cache

Cache 的默认接口如下所示：

```
class Cache:
    # it should be called when start the cache system
    def __init__(self):
        self.has_init = False
        self.cache_enable_func = None
        self.embedding_func = None
        self.post_process_messages_func = None
        self.config = Config()
```

在创建 ModelCache 之前，请考虑以下问题：

- 你将如何为查询生成嵌入向量？（embedding_func） 该函数将文本嵌入到一个用于上下文相似性搜索的密集向量中。ModelCache 可以支持多种嵌入上下文的方法：Huggingface、ONNX 和 SentenceTransformers。默认逻辑中，使用了在中文领域表现更好的 huggingface 中的 text2vec 模型。只需将你的嵌入函数初始化为：text2vec.to_embeddings

```
data_manager = get_data_manager(CacheBase("mysql", config=mysql_config),
                                VectorBase("milvus", dimension=data2vec.dimension, milvus_config=milvus_config))

cache.init(
    embedding_func=data2vec.to_embeddings,
    data_manager=data_manager,
    similarity_evaluation=SearchDistanceEvaluation(),
    query_pre_embedding_func=query_multi_splicing,
    insert_pre_embedding_func=insert_multi_splicing,
)
```

- 你将在哪里缓存数据？（data_manager 缓存存储） 缓存存储用于存储所有标量数据，例如原始问题、提示、答案和访问时间。ModelCache 支持多种缓存存储选项，如 SQLite、MySQL 和 OceanBase。未来还将添加更多的 NoSQL 数据库选项。
- 你将在哪里存储和搜索向量嵌入？（data_manager 向量存储） 向量存储组件用于存储和搜索所有嵌入向量，以便在语义上找到最相似的结果。ModelCache 支持使用 FAISS 等向量搜索库或 Milvus 等向量数据库。未来还将添加更多的向量数据库和云服务选项。

以下是一些示例：

```
data_manager = get_data_manager(CacheBase("sqlite"), VectorBase("faiss", dimension=data2vec.dimension))
data_manager = get_data_manager(CacheBase("oceanbase"), VectorBase("milvus", dimension=data2vec.dimension))
```
