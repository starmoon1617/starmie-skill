---
name: starmie-framework
description: "Starmie Framework 开发智能助手。基于 Spring Boot 的企业级 Java 开发框架。关键词: starmie, 实体, 服务, 控制器, 查询条件, 分页, Mapper, Service, Manager, Controller, BaseEntity, BaseCriteria, Pagination, Excel导出。用途: 创建实体类, 编写CRUD代码, 查询条件构建, 分页查询, Excel导出, 分层架构开发。"
---

# Starmie Framework - Java 开发智能助手

基于 Spring Boot 的企业级 Java 开发框架，提供标准化的分层架构、查询条件构建、分页支持和 Excel 导出功能。

## 触发条件

当用户输入包含以下关键字时，优先使用 Starmie Framework 的 API 进行开发：
- `starmie`
- 涉及 BaseEntity、BaseCriteria、BaseMapper、BaseService 等框架核心类

---

## 环境要求

初始化项目时，请遵循以下环境版本：
- **JDK**: 25
- **Starmie Framework**: 2.0.0

---

## 核心类导入

### 基础类

```java
import io.github.starmoon1617.starmie.core.base.BaseEntity;
import io.github.starmoon1617.starmie.core.base.BaseDto;
```

### 查询条件

```java
import io.github.starmoon1617.starmie.core.criterion.BaseCriteria;
import io.github.starmoon1617.starmie.core.criterion.enums.OperatorType;
import io.github.starmoon1617.starmie.core.criterion.enums.SortType;
import io.github.starmoon1617.starmie.core.page.Pagination;
```

### 服务层

```java
import io.github.starmoon1617.starmie.core.mapper.BaseMapper;
import io.github.starmoon1617.starmie.core.service.BaseService;
import io.github.starmoon1617.starmie.core.service.BaseServiceImpl;
import io.github.starmoon1617.starmie.core.manager.BaseManager;
import io.github.starmoon1617.starmie.core.manager.BaseManagerImpl;
```

### 控制器

```java
import io.github.starmoon1617.starmie.core.app.web.AbstractBaseController;
```

### 工具类

```java
import io.github.starmoon1617.starmie.core.util.EntityUtils;
import io.github.starmoon1617.starmie.core.util.DateUtils;
import io.github.starmoon1617.starmie.core.util.CommonUtils;
```

### Excel 导出

```java
import io.github.starmoon1617.starmie.utils.poi.write.ExcelWriter;
import io.github.starmoon1617.starmie.utils.doc.head.DocHead;
```

---

## 分层架构

Starmie Framework 采用标准的分层架构：

| 层级 | 基类 | 泛型参数 |
|------|------|----------|
| Entity | `BaseEntity<ID, UID>` | ID类型, 用户标识类型 |
| Mapper | `BaseMapper<E, ID, UID>` | 实体类, ID类型, 用户标识类型 |
| Service | `BaseService<E, ID, UID>` / `BaseServiceImpl<E, ID, UID>` | 实体类, ID类型, 用户标识类型 |
| Manager | `BaseManager<E, ID, UID>` / `BaseManagerImpl<E, ID, UID>` | 实体类, ID类型, 用户标识类型 |
| Controller | `AbstractBaseController<E, ID, UID>` | 实体类, ID类型, 用户标识类型 |

---

## 代码模板

### 实体类

```java
public class User extends BaseEntity<Long, Long> {
    
    private String username;
    private String email;
    private Integer status;
    
    // getters and setters
}
```

### Mapper 接口

```java
@Mapper
public interface UserMapper extends BaseMapper<User, Long, Long> {
    // 自定义查询方法
}
```

### Service 层

```java
// 接口
public interface UserService extends BaseService<User, Long, Long> {
    // 自定义业务方法
}

// 实现
@Service
public class UserServiceImpl extends BaseServiceImpl<User, Long, Long> 
    implements UserService {
    
    @Autowired
    private UserMapper userMapper;
    
    @Override
    protected BaseMapper<User, Long, Long> getMapper() {
        return userMapper;
    }
}
```

### Manager 层

```java
// 接口
public interface UserManager extends BaseManager<User, Long, Long> {
    // 跨服务业务方法
}

// 实现
@Component
public class UserManagerImpl extends BaseManagerImpl<User, Long, Long> 
    implements UserManager {
    
    @Autowired
    private UserService userService;
    
    @Override
    protected BaseService<User, Long, Long> getService() {
        return userService;
    }
}
```

### Controller 层

```java
@RestController
@RequestMapping("/api/users")
public class UserController extends AbstractBaseController<User, Long, Long> {
    
    @Autowired
    private UserManager userManager;
    
    @Override
    protected BaseManager<User, Long, Long> getManager() {
        return userManager;
    }
}
```

---

## 查询条件构建

### BaseCriteria 使用

```java
// 创建查询条件实例
BaseCriteria criteria = BaseCriteria.getInstance();

// 等于条件
criteria.addEqual("status", 1);

// 模糊查询
criteria.addLike("name", "张三");

// 使用 OperatorType 枚举
criteria.addCriterion(OperatorType.GT, "age", 18);
criteria.addCriterion(OperatorType.LTE, "createTime", new Date());

// IN 查询
criteria.addCriterion(OperatorType.IN, "status", Arrays.asList(1, 2, 3));

// BETWEEN 查询
criteria.addCriterion(OperatorType.BTW, "createTime", startDate, endDate);

// 排序
criteria.addSortCriterion("createTime", SortType.DESC);
criteria.addSortCriterion("id", SortType.ASC);
```

### OperatorType 枚举说明

| 枚举值 | 说明 | SQL 对应 |
|--------|------|----------|
| EQ | 等于 | = |
| NE | 不等于 | <> |
| GT | 大于 | > |
| GTE | 大于等于 | >= |
| LT | 小于 | < |
| LTE | 小于等于 | <= |
| LK | 模糊匹配 | LIKE %value% |
| RLKM | 右模糊 | LIKE value% |
| LLKM | 左模糊 | LIKE %value |
| IN | 包含 | IN (...) |
| NIN | 不包含 | NOT IN (...) |
| BTW | 区间 | BETWEEN ... AND ... |
| ISN | 为空 | IS NULL |
| ISNN | 不为空 | IS NOT NULL |

---

## 分页查询

```java
// 创建分页对象 (pageNo 从 0 开始)
Pagination pagination = new Pagination();
pagination.setPageNo(0);      // 第一页
pagination.setPageSize(10);   // 每页10条

// 设置查询条件
BaseCriteria criteria = BaseCriteria.getInstance();
criteria.addEqual("status", 1);
criteria.addSortCriterion("createTime", SortType.DESC);

// 执行分页查询
List<User> users = userService.findByCriteria(criteria, pagination);

// 获取分页信息
long total = pagination.getTotalCount();
int totalPages = pagination.getTotalPages();
```

---

## Excel 导出

```java
// 定义表头
List<DocHead> headers = new ArrayList<>();
headers.add(new DocHead("用户名", "username"));
headers.add(new DocHead("邮箱", "email"));
headers.add(new DocHead("状态", "status"));

// 获取数据
List<User> users = userService.findAll();

// 导出 Excel
ExcelWriter writer = new ExcelWriter();
writer.write(outputStream, headers, users);
```

---

## 注意事项

1. **包名规范**: 所有框架类都在 `io.github.starmoon1617.starmie.*` 包下
2. **泛型参数**: `<Entity, ID类型, 用户标识类型>`，常用组合为 `<E, Long, Long>`
3. **分页起始**: `Pagination.pageNo` 从 **0** 开始，不是从 1 开始
4. **字段映射**: 驼峰命名的 Java 字段自动转换为下划线命名的数据库列
5. **事务管理**: Service 层默认开启事务，Manager 层用于跨服务调用

---

## 最佳实践

### 代码规范

- [ ] 实体类继承 `BaseEntity<Long, Long>`
- [ ] Mapper 继承 `BaseMapper` 并添加 `@Mapper` 注解
- [ ] Service 实现类添加 `@Service` 注解
- [ ] Manager 实现类添加 `@Component` 注解
- [ ] Controller 添加 `@RestController` 和 `@RequestMapping` 注解

### 查询优化

- [ ] 复杂查询使用 `BaseCriteria` 构建条件
- [ ] 大数据量查询必须使用分页
- [ ] 排序字段建议添加数据库索引
- [ ] 避免在循环中执行数据库查询

### 异常处理

- [ ] Service 层处理业务异常
- [ ] Controller 层统一返回格式
- [ ] 使用框架提供的工具类进行数据校验
