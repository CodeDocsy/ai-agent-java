
mcp也依赖 Function call
统一了更上层的协议。
简单理解，MCP 相当于大模型领域的“HTTP 协议”，其并不绑定任何大模型，这意味着用户可以在支持MCP 的工具中，用任何大模型调用MCP 服务

其实就可以简单理解成服务与发现。 

 客户端怎么知道有哪些服务器？都是提前配置好的。
他肯定无法穷举所有的MCP Server。肯定要提前配置号。就跟服务注册与发现是一样的。客户端要知道用哪些mcp Server。
大模型根据场景去定制。

当然，用哪些mcp Server 本身也可以成为一个MCP Server。其类似意图分析。



MCP Clients : Stdio vs SSE




MCP 默认实现了一些接口。


# 基于Spring MVC 的函数式编程。

因为需要根据配置，动态发布服务。

McpSchema 类中定义了服务地址，来看看基于服务地址，是如何实现的。


都调用tool-call 方法去了。原来相当于在客户端调用 大模型值返回参数和id。 现在挪到了tool-call api 方法。


是规范了tool call调用。

SyncToolSpecification 定义了tool的实现


假设没有MCP：

支付宝为例：

封装了统一的接口。
Client-> 通过tools/call（JRPC、Http） 调用Server。

Server 实现 tool。

```java
private McpServerSession.RequestHandler<CallToolResult> toolsCallRequestHandler() {
		return (exchange, params) -> {
			McpSchema.CallToolRequest callToolRequest = objectMapper.convertValue(params,
					new TypeReference<McpSchema.CallToolRequest>() {
					});

			Optional<McpServerFeatures.AsyncToolSpecification> toolSpecification = this.tools.stream()
				.filter(tr -> callToolRequest.name().equals(tr.tool().name()))
				.findAny();

			if (toolSpecification.isEmpty()) {
				return Mono.error(new McpError("Tool not found: " + callToolRequest.name()));
			}

			return toolSpecification.map(tool -> tool.callHandler().apply(exchange, callToolRequest))
				.orElse(Mono.error(new McpError("Tool not found: " + callToolRequest.name())));
		};
	}
```
apply就是调用实际的lamda表达式，在定义的时候传入这个表达式，就是实际要处理的值

## 注解化




