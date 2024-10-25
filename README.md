
连接Elasticsearch（ES）服务器是进行数据搜索和分析的常用操作。Elasticsearch是一个基于Lucene的搜索引擎，提供了RESTful API来进行索引、搜索和管理数据。


以下是一个详细的Python代码示例，展示如何连接到Elasticsearch服务器并执行一些基本操作。这个示例使用了官方的`elasticsearch-py`客户端库。


### 1\. 安装Elasticsearch客户端库


首先，你需要安装`elasticsearch`库。如果你还没有安装，可以使用pip进行安装：



```
bash复制代码

pip install elasticsearch

```

### 2\. 连接到Elasticsearch服务器


以下是一个完整的Python脚本，展示了如何连接到Elasticsearch服务器，创建索引，添加文档，并进行搜索。



```
from elasticsearch import Elasticsearch, helpers  
  
# 配置Elasticsearch连接  
es = Elasticsearch(  
    ['http://localhost:9200'],  # Elasticsearch服务器地址和端口  
    http_auth=('username', 'password'),  # 如果需要认证，填写用户名和密码  
    use_ssl=False,  # 如果使用HTTPS，设置为True  
    verify_certs=False  # 如果使用HTTPS且自签名证书，设置为False  
)  
  
# 检查连接是否成功  
if es.ping():  
    print("Successfully connected to Elasticsearch!")  
else:  
    print("Could not connect to Elasticsearch")  
    exit()  
  
# 创建索引  
index_name = 'my_index'  
if not es.indices.exists(index=index_name):  
    # 定义索引的映射（Schema）  
    mappings = {  
        'properties': {  
            'title': {'type': 'text'},  
            'content': {'type': 'text'},  
            'author': {'type': 'keyword'}  
        }  
    }  
    # 创建索引  
    es.indices.create(index=index_name, body={'mappings': mappings})  
    print(f"Index '{index_name}' created successfully.")  
else:  
    print(f"Index '{index_name}' already exists.")  
  
# 添加文档  
documents = [  
    {"_id": 1, "title": "Elasticsearch Basics", "content": "Learn the basics of Elasticsearch.", "author": "John Doe"},  
    {"_id": 2, "title": "Advanced Elasticsearch", "content": "Go deeper into Elasticsearch features.", "author": "Jane Smith"},  
    {"_id": 3, "title": "Elasticsearch Performance", "content": "Optimize Elasticsearch for performance.", "author": "Alice Johnson"}  
]  
  
# 使用bulk API批量添加文档  
actions = [  
    {  
        "_index": index_name,  
        "_id": doc['_id'],  
        "_source": doc  
    }  
    for doc in documents  
]  
  
helpers.bulk(es, actions)  
print("Documents added successfully.")  
  
# 搜索文档  
search_body = {  
    "query": {  
        "match": {  
            "content": "Elasticsearch"  
        }  
    }  
}  
  
response = es.search(index=index_name, body=search_body)  
print("Search results:")  
for hit in response['hits']['hits']:  
    print(hit['_source'])  
  
# 清理（可选）：删除索引  
# es.indices.delete(index=index_name)  
# print(f"Index '{index_name}' deleted successfully.")

```

### 3\.代码解释


1. 连接配置：
	* `Elasticsearch(['http://localhost:9200'])`：连接到运行在本地主机上的Elasticsearch服务器，默认端口为9200。
	* `http_auth=('username', 'password')`：如果Elasticsearch服务器需要认证，填写用户名和密码。
	* `use_ssl`和`verify_certs`：如果连接使用HTTPS，可以启用这些选项。
2. 检查连接：
	* 使用`es.ping()`方法检查连接是否成功。
3. 创建索引：
	* 使用`es.indices.exists(index=index_name)`检查索引是否存在。
	* 使用`es.indices.create(index=index_name, body={'mappings': mappings})`创建索引，并定义文档的映射。
4. 添加文档：
	* 使用`helpers.bulk(es, actions)`批量添加文档到索引中。
5. 搜索文档：
	* 使用`es.search(index=index_name, body=search_body)`进行搜索，并打印搜索结果。
6. 清理（可选）：
	* 使用`es.indices.delete(index=index_name)`删除索引。


### 4\.注意事项


* **服务器地址**：确保Elasticsearch服务器正在运行，并且地址和端口配置正确。
* **认证**：如果Elasticsearch服务器需要认证，确保提供正确的用户名和密码。
* **SSL**：如果连接使用HTTPS，请正确配置`use_ssl`和`verify_certs`选项。


 本博客参考[悠兔机场](https://xinnongbo.com)。转载请注明出处！
