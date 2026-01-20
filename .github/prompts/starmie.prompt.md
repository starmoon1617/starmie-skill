---
name: starmie
description: 'Starmie Framework - Java企业级开发框架 (Spring Boot 4.0 + MyBatis 3.5 + Java 25)'
agent: 'agent'
---

# Starmie Framework - Java开发智能助手

Starmie Framework是一个模块化的Java企业级开发框架，提供基于泛型的基础类定义、通用CRUD实现、查询条件构建器、分页支持、Excel/PDF工具和代码生成器。

## 技术栈

- **Java**: 25
- **Spring Boot**: 4.0.0
- **Spring Framework**: 7.0.2
- **MyBatis**: 3.5.19
- **构建工具**: Maven

## 模块结构

| 模块 | 说明 |
|------|------|
| `starmie-core-base` | 基础实体类定义 (Base, BaseEntity, BaseDto, BaseResult) |
| `starmie-core-common` | 通用工具类、查询条件构建器 (Criterion, Pagination) |
| `starmie-core-service` | Service/Mapper/Manager层基类定义 |
| `starmie-app-base` | Controller基类 |
| `starmie-app-web` | Web层通用CRUD实现 |
| `starmie-boot-executor` | 线程池自动配置 |
| `starmie-boot-executor-vt` | 虚拟线程执行器自动配置 |
| `starmie-utils-poi` | Excel导入导出工具 |
| `starmie-utils-pdf` | PDF生成工具 |
| `starmie-generator-core` | 代码生成器核心 |
| `starmie-generator-freemarker` | Freemarker模板生成器 |
| `starmie-generator-thymeleaf` | Thymeleaf模板生成器 |

---

## 如何使用此Prompt

当用户请求基于Starmie Framework进行Java开发时，请按照以下工作流程：

### Step 1: 分析用户需求

从用户请求中提取关键信息：
- **实体名称**: User, Order, Product等
- **字段定义**: 字段名、类型、约束
- **业务需求**: CRUD、分页查询、导出等
- **层次需求**: 只需Entity/需要完整分层

### Step 2: 按分层顺序生成代码

1. **Entity** - 继承 `BaseEntity<ID, U>`
2. **Mapper接口** - 继承 `BaseMapper<E, ID, U>`
3. **Mapper XML** - MyBatis映射文件
4. **Service接口** - 继承 `BaseService<E, ID, U>`
5. **Service实现** - 继承 `BaseServiceImpl<E, ID, U>`
6. **Manager接口** - 继承 `BaseManager<E, ID, U>`
7. **Manager实现** - 继承 `BaseManagerImpl<E, ID, U>`
8. **Controller** - 继承 `AbstractBaseController<E, ID, U>`

---

## 核心类使用指南

### 1. 实体类 (Entity)

继承 `BaseEntity<ID, U>` 创建实体类：

```java
package com.example.entity;

import io.github.starmoon1617.starmie.core.base.BaseEntity;

/**
 * 用户实体
 */
public class User extends BaseEntity<Long, Long> {
    
    private static final long serialVersionUID = 1L;
    
    private String username;
    private String email;
    private Integer status;
    
    // getters and setters
    public String getUsername() {
        return username;
    }
    
    public void setUsername(String username) {
        this.username = username;
    }
    
    public String getEmail() {
        return email;
    }
    
    public void setEmail(String email) {
        this.email = email;
    }
    
    public Integer getStatus() {
        return status;
    }
    
    public void setStatus(Integer status) {
        this.status = status;
    }
}
```

**继承字段说明:**
- `id` - 主键ID (来自BaseEntity)
- `createTime` - 创建时间 (来自Base)
- `updateTime` - 更新时间 (来自Base)
- `createBy` - 创建用户 (来自Base)
- `updateBy` - 更新用户 (来自Base)

### 2. Mapper接口

继承 `BaseMapper<E, ID, U>` 创建Mapper：

```java
package com.example.mapper;

import io.github.starmoon1617.starmie.core.mapper.BaseMapper;
import com.example.entity.User;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface UserMapper extends BaseMapper<User, Long, Long> {
    // 自定义方法
}
```

**继承方法:**
- `E select(E e)` - 查询单条
- `int insert(E e)` - 插入
- `int update(E e)` - 更新
- `int delete(E e)` - 删除
- `List<E> selectList(BaseCriteria criteria)` - 条件查询列表
- `int count(BaseCriteria criteria)` - 条件计数

### 3. Service层

继承 `BaseServiceImpl<E, ID, U>` 实现Service：

```java
package com.example.service;

import io.github.starmoon1617.starmie.core.service.BaseService;
import com.example.entity.User;

public interface UserService extends BaseService<User, Long, Long> {
    // 自定义方法
}
```

```java
package com.example.service.impl;

import io.github.starmoon1617.starmie.core.service.BaseServiceImpl;
import io.github.starmoon1617.starmie.core.mapper.BaseMapper;
import com.example.entity.User;
import com.example.mapper.UserMapper;
import com.example.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl extends BaseServiceImpl<User, Long, Long> implements UserService {
    
    @Autowired
    private UserMapper userMapper;
    
    @Override
    protected BaseMapper<User, Long, Long> getMapper() {
        return userMapper;
    }
}
```

### 4. Manager层 (带事务)

继承 `BaseManagerImpl<E, ID, U>` 实现Manager：

```java
package com.example.manager;

import io.github.starmoon1617.starmie.core.manager.BaseManager;
import com.example.entity.User;

public interface UserManager extends BaseManager<User, Long, Long> {
    // 自定义业务方法
}
```

```java
package com.example.manager.impl;

import io.github.starmoon1617.starmie.core.manager.BaseManagerImpl;
import io.github.starmoon1617.starmie.core.service.BaseService;
import com.example.entity.User;
import com.example.service.UserService;
import com.example.manager.UserManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class UserManagerImpl extends BaseManagerImpl<User, Long, Long> implements UserManager {
    
    @Autowired
    private UserService userService;
    
    @Override
    protected BaseService<User, Long, Long> getService() {
        return userService;
    }
}
```

**Manager层特性:**
- `@Transactional` 自动事务管理
- 支持批量保存 `save(Collection<E> es)`
- 支持分页查询 `find(Pagination<E> pagination, BaseCriteria criteria)`

### 5. Controller层

继承 `AbstractBaseController<E, ID, U>` 创建Controller：

```java
package com.example.controller;

import io.github.starmoon1617.starmie.core.app.web.AbstractBaseController;
import io.github.starmoon1617.starmie.core.manager.BaseManager;
import com.example.entity.User;
import com.example.manager.UserManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/user")
public class UserController extends AbstractBaseController<User, Long, Long> {
    
    @Autowired
    private UserManager userManager;
    
    @Override
    protected BaseManager<User, Long, Long> getManager() {
        return userManager;
    }
    
    @Override
    protected String getPath() {
        return "user";
    }
}
```

**自动提供的端点:**
- `POST /list` - 分页列表查询
- `POST /save` - 新增
- `POST /update` - 更新
- `POST /delete` - 删除
- `POST /export` - Excel导出
- `POST /import` - Excel导入
- `GET /toList` - 列表页面
- `GET /toAdd` - 新增页面
- `GET /toEdit` - 编辑页面

---

## 查询条件构建器 (BaseCriteria)

### 基本用法

```java
import io.github.starmoon1617.starmie.core.criterion.BaseCriteria;
import io.github.starmoon1617.starmie.core.criterion.enums.OperatorType;
import io.github.starmoon1617.starmie.core.criterion.enums.SortType;

// 创建查询条件
BaseCriteria criteria = BaseCriteria.getInstance();

// 添加等值查询
criteria.addEqual("status", 1);

// 添加模糊查询
criteria.addLike("username", "admin");

// 添加多条件查询
criteria.addCriterion(OperatorType.GTE, "createTime", startDate);
criteria.addCriterion(OperatorType.LTE, "createTime", endDate);

// 添加IN查询
criteria.addEqual("status", 1, 2, 3);

// 添加排序
criteria.addSortCriterion("createTime", SortType.DESC);

// 链式调用
BaseCriteria criteria = BaseCriteria.getInstance()
    .addEqual("status", 1)
    .addLike("username", "test")
    .addSortCriterion("id", SortType.DESC);
```

### 操作符类型 (OperatorType)

| 类型 | 说明 | 示例 |
|------|------|------|
| `EQ` | 等于 | `addCriterion(OperatorType.EQ, "status", 1)` |
| `NE` | 不等于 | `addCriterion(OperatorType.NE, "status", 0)` |
| `GT` | 大于 | `addCriterion(OperatorType.GT, "age", 18)` |
| `GTE` | 大于等于 | `addCriterion(OperatorType.GTE, "age", 18)` |
| `LT` | 小于 | `addCriterion(OperatorType.LT, "age", 60)` |
| `LTE` | 小于等于 | `addCriterion(OperatorType.LTE, "age", 60)` |
| `LK` | 模糊匹配 | `addCriterion(OperatorType.LK, "name", "test")` |
| `RLKM` | 右模糊 | `addCriterion(OperatorType.RLKM, "name", "test")` |
| `LLKM` | 左模糊 | `addCriterion(OperatorType.LLKM, "name", "test")` |
| `IN` | IN查询 | `addCriterion(OperatorType.IN, "status", 1, 2, 3)` |
| `NIN` | NOT IN | `addCriterion(OperatorType.NIN, "status", 0)` |
| `BTW` | BETWEEN | `addCriterion(OperatorType.BTW, "age", 18, 60)` |
| `ISN` | IS NULL | `addCriterion(OperatorType.ISN, "deletedAt")` |
| `ISNN` | IS NOT NULL | `addCriterion(OperatorType.ISNN, "email")` |

### 组合条件查询

```java
import io.github.starmoon1617.starmie.core.criterion.enums.CombinaType;

// OR组合条件: (status = 1 OR status = 2)
criteria.addCriterion(CombinaType.COMBINA_OUTER_OR, "statusGroup", OperatorType.EQ, "status", 1);
criteria.addCriterion(CombinaType.COMBINA_OR, "statusGroup", OperatorType.EQ, "status", 2);
```

---

## 分页查询 (Pagination)

```java
import io.github.starmoon1617.starmie.core.page.Pagination;
import io.github.starmoon1617.starmie.core.criterion.BaseCriteria;

// 创建分页对象
Pagination<User> pagination = new Pagination<>();
pagination.setPageNo(0);    // 页码从0开始
pagination.setPageSize(10);

// 创建查询条件
BaseCriteria criteria = BaseCriteria.getInstance()
    .addEqual("status", 1)
    .addSortCriterion("createTime", SortType.DESC);

// 执行分页查询
userManager.find(pagination, criteria);

// 获取结果
List<User> users = pagination.getElms();  // 数据列表
int total = pagination.getTotal();         // 总记录数
int totalPage = pagination.getTotalPage(); // 总页数
```

---

## 返回结果封装

### BaseResult

```java
import io.github.starmoon1617.starmie.core.base.BaseResult;

// 包含 code 和 msg
```

### BaseDto

```java
import io.github.starmoon1617.starmie.core.base.BaseDto;

// 继承BaseResult, 增加泛型data字段
BaseDto<User> dto = new BaseDto<>();
dto.setCode(0);
dto.setMsg("success");
dto.setData(user);
```

### Controller中使用

```java
// 成功返回
return getSuccess(data);

// 失败返回
return getFailure(-1, "操作失败");

// 自定义返回
return getResult(200, "操作成功", data);
```

---

## MyBatis Mapper XML模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.UserMapper">

    <resultMap id="BaseResultMap" type="com.example.entity.User">
        <id column="id" property="id" jdbcType="BIGINT"/>
        <result column="username" property="username" jdbcType="VARCHAR"/>
        <result column="email" property="email" jdbcType="VARCHAR"/>
        <result column="status" property="status" jdbcType="INTEGER"/>
        <result column="create_time" property="createTime" jdbcType="TIMESTAMP"/>
        <result column="update_time" property="updateTime" jdbcType="TIMESTAMP"/>
        <result column="create_by" property="createBy" jdbcType="BIGINT"/>
        <result column="update_by" property="updateBy" jdbcType="BIGINT"/>
    </resultMap>
    
    <sql id="Base_Column_List">
        id, username, email, status, create_time, update_time, create_by, update_by
    </sql>
    
    <!-- 查询条件片段 -->
    <sql id="Criteria_Where_Clause">
        <if test="criteria != null">
            <foreach collection="criteria" item="cond" separator="">
                <if test="cond.values != null">
                    <foreach collection="cond.values" item="criterion" separator="">
                        ${criterion.joinType} ${criterion.term} ${criterion.operator}
                        <choose>
                            <when test="criterion.type == 'single'">
                                #{criterion.values[0]}
                            </when>
                            <when test="criterion.type == 'dual'">
                                #{criterion.values[0]} AND #{criterion.values[1]}
                            </when>
                            <when test="criterion.type == 'multi'">
                                <foreach collection="criterion.values" item="val" open="(" separator="," close=")">
                                    #{val}
                                </foreach>
                            </when>
                        </choose>
                    </foreach>
                </if>
            </foreach>
        </if>
    </sql>
    
    <select id="select" resultMap="BaseResultMap" parameterType="com.example.entity.User">
        SELECT <include refid="Base_Column_List"/>
        FROM user
        WHERE id = #{id}
    </select>
    
    <select id="selectList" resultMap="BaseResultMap" parameterType="io.github.starmoon1617.starmie.core.criterion.BaseCriteria">
        SELECT <include refid="Base_Column_List"/>
        FROM user
        WHERE 1=1
        <include refid="Criteria_Where_Clause"/>
        <if test="sortCriteria != null">
            ORDER BY
            <foreach collection="sortCriteria" item="sort" separator=",">
                ${sort.term} ${sort.type}
            </foreach>
        </if>
        <if test="limit != null">
            LIMIT #{limit}
        </if>
        <if test="offset != null">
            OFFSET #{offset}
        </if>
    </select>
    
    <select id="count" resultType="int" parameterType="io.github.starmoon1617.starmie.core.criterion.BaseCriteria">
        SELECT COUNT(1) FROM user WHERE 1=1
        <include refid="Criteria_Where_Clause"/>
    </select>
    
    <insert id="insert" parameterType="com.example.entity.User" useGeneratedKeys="true" keyProperty="id">
        INSERT INTO user (username, email, status, create_time, update_time, create_by, update_by)
        VALUES (#{username}, #{email}, #{status}, #{createTime}, #{updateTime}, #{createBy}, #{updateBy})
    </insert>
    
    <update id="update" parameterType="com.example.entity.User">
        UPDATE user
        SET username = #{username},
            email = #{email},
            status = #{status},
            update_time = #{updateTime},
            update_by = #{updateBy}
        WHERE id = #{id}
    </update>
    
    <delete id="delete" parameterType="com.example.entity.User">
        DELETE FROM user WHERE id = #{id}
    </delete>

</mapper>
```

---

## Maven依赖配置

### 使用starmie-bom管理版本

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>io.github.starmoon1617</groupId>
            <artifactId>starmie-bom</artifactId>
            <version>2.0.1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <!-- Core -->
    <dependency>
        <groupId>io.github.starmoon1617</groupId>
        <artifactId>starmie-core-base</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>io.github.starmoon1617</groupId>
        <artifactId>starmie-core-common</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>io.github.starmoon1617</groupId>
        <artifactId>starmie-core-service</artifactId>
        <version>2.0.0</version>
    </dependency>
    
    <!-- App -->
    <dependency>
        <groupId>io.github.starmoon1617</groupId>
        <artifactId>starmie-app-web</artifactId>
        <version>2.0.0</version>
    </dependency>
    
    <!-- Boot Auto Configuration -->
    <dependency>
        <groupId>io.github.starmoon1617</groupId>
        <artifactId>starmie-boot-executor</artifactId>
        <version>2.0.0</version>
    </dependency>
    
    <!-- Utils (可选) -->
    <dependency>
        <groupId>io.github.starmoon1617</groupId>
        <artifactId>starmie-utils-poi</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>io.github.starmoon1617</groupId>
        <artifactId>starmie-utils-pdf</artifactId>
        <version>2.0.0</version>
    </dependency>
</dependencies>
```

---

## 开发规范

### 命名规范

| 类型 | 命名规则 | 示例 |
|------|----------|------|
| Entity | 名词单数, PascalCase | `User`, `OrderItem` |
| Mapper | Entity名 + Mapper | `UserMapper` |
| Service接口 | Entity名 + Service | `UserService` |
| Service实现 | Entity名 + ServiceImpl | `UserServiceImpl` |
| Manager接口 | Entity名 + Manager | `UserManager` |
| Manager实现 | Entity名 + ManagerImpl | `UserManagerImpl` |
| Controller | Entity名 + Controller | `UserController` |

### 包结构规范

```
com.example
├── entity/          # 实体类
├── mapper/          # MyBatis Mapper接口
├── service/         # Service接口
│   └── impl/        # Service实现
├── manager/         # Manager接口
│   └── impl/        # Manager实现
├── controller/      # Controller
└── config/          # 配置类
```

### 分层职责

| 层 | 职责 | 事务 |
|----|------|------|
| Controller | 接收请求, 参数验证, 返回响应 | 无 |
| Manager | 业务逻辑编排, 多Service协调 | 有 |
| Service | 单表CRUD操作 | 无 |
| Mapper | 数据访问 | 无 |

---

## 代码生成器

使用 `starmie-generator` 可以基于数据库表自动生成:
- Entity类
- Mapper接口 + XML
- Service接口 + 实现类
- Manager接口 + 实现类
- Controller类

支持模板引擎:
- Freemarker
- Thymeleaf

---

## 示例工作流

**用户请求:** "创建一个Product商品实体,包含name, price, stock字段"

**AI应该:**

1. 创建 `Product.java` Entity
2. 创建 `ProductMapper.java` 接口
3. 创建 `ProductMapper.xml` MyBatis映射
4. 创建 `ProductService.java` 接口
5. 创建 `ProductServiceImpl.java` 实现
6. 创建 `ProductManager.java` 接口
7. 创建 `ProductManagerImpl.java` 实现
8. 创建 `ProductController.java` 控制器

---

## 快速开始检查清单

- [ ] 添加starmie-bom依赖管理
- [ ] 添加所需模块依赖
- [ ] 创建Entity继承BaseEntity
- [ ] 创建Mapper继承BaseMapper
- [ ] 创建Service继承BaseServiceImpl
- [ ] 创建Manager继承BaseManagerImpl
- [ ] 创建Controller继承AbstractBaseController
- [ ] 配置MyBatis Mapper XML
- [ ] 配置数据源和事务管理器
