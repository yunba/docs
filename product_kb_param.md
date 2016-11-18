# 参数说明

本文用来对 SDK 中的部分常见参数进行综合的解释说明。

## topic

`topic` 用来表示 [频道名称](product_kb_topic_and_alias.md#%E9%A2%91%E9%81%93%E5%90%8D%E7%A7%B0) 或 [频道过滤器](product_kb_topic_filter.md#%E9%A2%91%E9%81%93%E8%BF%87%E6%BB%A4%E5%99%A8)。

- `topic`作为频道名称时，允许的字符包括：英文大小写字母、数字、下划线`_`和正斜杠`/`。
- `topic`作为频道过滤器时，允许的字符包括：英文大小写字母、数字、下划线`_`、正斜杠`/`，以及表示通配符的`#`和`+`。

### 取值范围

- 下列 API 中的`topic`可以作为 [频道名称](product_kb_topic_and_alias.md#%E9%A2%91%E9%81%93%E5%90%8D%E7%A7%B0) 或 [频道过滤器](product_kb_topic_filter.md#%E9%A2%91%E9%81%93%E8%BF%87%E6%BB%A4%E5%99%A8)。**取值范围为：英文大小写字母、数字、`_`、`/`、`#`和`+`，长度不超过 128 个字符。**

	- `subscribe()`（或`MQTTClient_subscribe`）
	- `unsubscribe()`（或`MQTTClient_unsubscribe`）
	- `get_alias_list()`（或`getAliasList()`、`MQTTGetAliasList()`等类似名称）

- 下列 API 中的`topic`只能是 [频道名称](product_kb_topic_and_alias.md#%E9%A2%91%E9%81%93%E5%90%8D%E7%A7%B0)。**取值范围为：英文大小写字母、数字、`_`和`/`，长度不超过 128 个字符。**

	- `publish()`（或`MQTTClient_publish`）
	- `publish2()`（或`MQTTClient_publish2`）
	- `subscribe_presence()`（或`MQTTClient_presence`）
	- `unsubscribe_presence()`（或`MQTTClient_unpresence`）

- `topics`数组的元素个数不超过 100。

## alias

`alias` 用来表示 [别名](product_kb_topic_and_alias.md)。

**注意**

- 同一个 AppKey 下，alias 是唯一的，不能有重复。假设 Client 1 别名为 A；此时如果设置 Client 2 的别名为 A 则 Client 1 的别名将会失效。

- 设置别名为空字符串，可以取消别名。

### 取值范围

- `alias`的取值范围为：英文（大小写）、数字、下划线`_`，长度不超过 128 个字符。



