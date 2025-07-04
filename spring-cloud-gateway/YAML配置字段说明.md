# Spring Cloud Gateway YAML 配置字段说明

---

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: example_route
          uri: http://httpbin.org:80
          order: 0
          predicates:
            - Path=/get
            - Method=GET,POST
            - Host=**.example.com
          filters:
            - AddRequestHeader=X-Request-Example, Gateway
            - StripPrefix=1
      default-filters:
        - AddResponseHeader=X-Response-Default, Default
      discovery:
        locator:
          enabled: true
          lower-case-service-id: true
```

---

## 字段详细说明

### 1. `spring.cloud.gateway.routes`
- **routes**：路由数组，每个元素定义一条路由规则。

#### 路由字段
- **id**：路由唯一标识（字符串，必填）。
- **uri**：目标服务地址。支持 HTTP、HTTPS、lb（服务发现）、ws（WebSocket）等协议。例如：`http://host:port`、`lb://service-name`。
- **order**：路由优先级，数字越小优先级越高（可选，默认0）。
- **predicates**：谓词数组，决定请求是否匹配该路由。常见谓词有：
  - `Path`：按路径匹配，如 `Path=/foo/**`
  - `Method`：按 HTTP 方法匹配，如 `Method=GET,POST`
  - `Host`：按 Host 匹配，如 `Host=**.example.com`
  - `Header`：按请求头匹配，如 `Header=X-Request-Id, \d+`
  - `Query`：按查询参数匹配，如 `Query=foo, bar`
  - `After`、`Before`、`Between`：按时间匹配
- **filters**：过滤器数组，对请求或响应进行处理。常见过滤器有：
  - `AddRequestHeader`：添加请求头
  - `AddResponseHeader`：添加响应头
  - `StripPrefix`：去除路径前缀
  - `RequestRateLimiter`：限流
  - `Retry`：重试
  - `CircuitBreaker`：熔断
  - `RewritePath`：重写路径

### 2. `spring.cloud.gateway.default-filters`
- **default-filters**：全局默认过滤器，应用于所有路由。

### 3. `spring.cloud.gateway.discovery.locator`
- **discovery.locator.enabled**：是否开启服务发现自动路由（如 Eureka/Nacos），开启后会自动为注册中心的服务创建路由。
- **discovery.locator.lower-case-service-id**：服务名是否转为小写（推荐 true，避免大小写不一致问题）。

---

## 典型配置示例

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/user/**
          filters:
            - StripPrefix=1
            - RequestRateLimiter=redis-rate-limiter
        - id: static
          uri: http://static.example.com
          predicates:
            - Path=/static/**
      default-filters:
        - AddResponseHeader=X-Global, true
      discovery:
        locator:
          enabled: true
          lower-case-service-id: true
```

---

## 其他常用配置

- **spring.cloud.gateway.globalcors**：全局 CORS 配置
- **spring.cloud.gateway.httpclient**：底层 HTTP 客户端相关配置（如超时、代理等）
- **spring.cloud.gateway.metrics.enabled**：是否开启网关指标监控

---

如需某个谓词或过滤器的详细参数说明，或需要更复杂的配置示例，请告知！ 