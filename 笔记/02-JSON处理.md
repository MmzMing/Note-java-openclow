- --
uid: 20260429002
title: JSON处理
tags:
  - JSON
  - FastJson
  - Jackson
  - 序列化
category: 基础
type: 笔记
expired: 2088-01-01
publish: true
- --

# JSON 处理

> Java 主流 JSON 库：FastJson 与 Jackson

- --

## 依赖引入

### FastJson

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>x.x.x</version>
</dependency>

```

### Jackson

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.6</version>
</dependency>

```

> SpringBoot 项目通过 `spring-boot-starter-web` 自动引入 Jackson

- --

## 目录

- [FastJson](#fastjson)
  - [JSON 类](#1-json-类)
  - [JSONObject 类](#2-jsonobject-类)
  - [JSONArray 类](#3-jsonarray-类)
  - [JSONPath 类](#4-jsonpath-类)
  - [JSONB 类](#5-jsonb-类)
  - [@JSONField 注解](#6-jsonfield-注解)
- [Jackson](#jackson)
  - [注解](#注解)
  - [配置](#配置)

- --

## FastJson

### 1. JSON 类

静态方法，提供核心的序列化与反序列化功能。

| 方法 | 说明 |
|------|------|
| `parseObject(String, Class)` | JSON 字符串解析为 Java 对象 |
| `parseObject(String)` | 解析为 JSONObject |
| `parseArray(String, Class)` | JSON 数组解析为 List |
| `parseArray(String)` | 解析为 JSONArray |
| `toJSONString(Object)` | 对象序列化为 JSON 字符串 |
| `toJSONBytes(Object)` | 序列化为字节数组 |
| `toJSON(Object)` | 转换为 JSONObject/JSONArray |
| `parse(String)` | 自动识别类型解析 |

```java
// 解析对象
User user = JSON.parseObject(json, User.class);

// 解析数组
List<User> list = JSON.parseArray(jsonArray, User.class);

// 序列化
String json = JSON.toJSONString(user);

```

- --

### 2. JSONObject 类

实现 `Map<String, Object>` 接口，表示 JSON 对象。

```java
JSONObject obj = new JSONObject();
obj.put("name", "张三");
obj.put("age", 20);

// 获取值
String name = obj.getString("name");
int age = obj.getIntValue("age");
JSONObject sub = obj.getJSONObject("address");

// 转换为 Bean
User user = obj.toJavaObject(User.class);

```

- --

### 3. JSONArray 类

实现 `List<Object>` 接口，表示 JSON 数组。

```java
JSONArray arr = new JSONArray();
arr.add("first");
arr.add(123);
arr.add(new JSONObject().fluentPut("key", "value"));

// 获取值
String first = arr.getString(0);
JSONObject obj = arr.getJSONObject(2);

// 转换为 List
List<User> list = arr.toJavaList(User.class);

```

- --

### 4. JSONPath 类

XPath 风格的 JSON 查询。

```java
// 查询
JSONPath path = JSONPath.of("$.store.book[0].title");
Object result = path.eval(jsonObject);

// 设置
JSONPath.set(jsonObject, "$.name", "李四");

// 删除
JSONPath.remove(jsonObject, "$.items[0]");

// 判断存在
boolean exists = JSONPath.contains(jsonObject, "$.name");

```

- --

### 5. JSONB 类

二进制 JSON，序列化性能更高。

```java
// 序列化
byte[] bytes = JSONB.toBytes(user);

// 反序列化
User user = JSONB.parseObject(bytes, User.class);
List<User> list = JSONB.parseArray(bytes, User.class);

```

- --

### 6. @JSONField 注解

用于定制字段的序列化/反序列化行为。

| 属性 | 说明 |
|------|------|
| `name` | 序列化后的字段名 |
| `format` | 日期格式，如 `yyyy-MM-dd` |
| `ordinal` | 序列化顺序 |
| `serialize` | 是否参与序列化 |
| `deserialize` | 是否参与反序列化 |
| `defaultValue` | 默认值 |
| `jsonDirect` | 直接输出 JSON 字符串 |
| `serializeUsing` | 自定义序列化器 |
| `deserializeUsing` | 自定义反序列化器 |
| `label` | 分组标签 |
| `unwrapped` | 扁平化输出 |

```java
public class User {
    @JSONField(name = "userName")
    private String name;

    @JSONField(ordinal = 1, format = "yyyy-MM-dd")
    private Date birthDate;

    @JSONField(serialize = false)
    private String password;

    @JSONField(defaultValue = "汉族")
    private String nation;
}

```

#### 常用 SerializerFeature

| 名称 | 含义 |
|------|------|
| `WriteMapNullValue` | 输出 null 值字段 |
| `WriteNullNumberAsZero` | null 数值输出为 0 |
| `WriteNullStringAsEmpty` | null 字符串输出为空 |
| `WriteNullBooleanAsFalse` | null 布尔输出为 false |
| `PrettyFormat` | 格式化输出 |
| `WriteClassName` | 输出类型信息 |
| `DisableCircularReferenceDetect` | 禁用循环引用检测 |

```java
// 输出 null 值
JSON.toJSONString(obj, SerializerFeature.WriteMapNullValue)

```

- --

## Jackson

### 核心注解

#### 序列化

| 注解 | 说明 |
|------|------|
| `@JsonProperty` | 指定字段名 |
| `@JsonPropertyOrder` | 指定序列化顺序 |
| `@JsonRawValue` | 原样输出 JSON 字符串 |
| `@JsonValue` | 方法返回值为整个对象 |
| `@JsonRootName` | 包装根节点 |
| `@JsonSerialize` | 自定义序列化器 |

#### 反序列化

| 注解 | 说明 |
|------|------|
| `@JsonCreator` | 指定构造器/工厂方法 |
| `@JsonSetter` | 指定 setter 方法 |
| `@JsonAlias` | 反序列化别名 |
| `@JsonDeserialize` | 自定义反序列化器 |

#### 属性包含

| 注解 | 说明 |
|------|------|
| `@JsonIgnoreProperties` | 类级别忽略 |
| `@JsonIgnore` | 属性级别忽略 |
| `@JsonIgnoreType` | 类型级别忽略 |
| `@JsonInclude` | 包含规则 |
| `@JsonAutoDetect` | 可见性控制 |

#### 高级特性

| 注解 | 说明 |
|------|------|
| `@JsonUnwrapped` | 扁平化 |
| `@JsonView` | 视图控制 |
| `@JsonManagedReference` | 父属性 |
| `@JsonBackReference` | 子属性（不序列化） |
| `@JsonIdentityInfo` | 对象标识 |
| `@JsonFilter` | 过滤器 |
| `@JsonNaming` | 命名策略 |

- --

### 配置示例

```java
ObjectMapper mapper = new ObjectMapper();

// 格式化输出
mapper.enable(SerializationFeature.INDENT_OUTPUT);

// 日期格式
mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));

// 命名策略（SNAKE_CASE）
mapper.setPropertyNamingStrategy(PropertyNamingStrategies.SNAKE_CASE);

// 忽略未知属性
mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);

```

- --

### SpringBoot 配置

```yaml
spring:
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
    serialization:
      write-dates-as-timestamps: false
      inclusion: non_null
    deserialization:
      fail-on-unknown-properties: false

```

- --

## 相关文档

- [[03-Algorithm-QuickStart]] - 算法基础
- [[01-IDE-IDEA快捷键]] - IDEA快捷键