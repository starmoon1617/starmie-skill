# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Starmie Framework Skill 是一个为 Claude Code 提供的开发智能助手，专注于 Starmie Framework（基于 Spring Boot 的企业级 Java 开发框架）的代码生成和开发指导。

## Skill 位置

```
.claude/skills/starmie-framework/
├── SKILL.md                      # Skill 定义文件，包含框架使用指南
```

## 触发关键字

当用户输入包含以下关键字时，此 Skill 会被激活：
- `starmie`
- `BaseEntity`、`BaseCriteria`、`BaseMapper`、`BaseService` 等框架核心类

## 核心功能

1. **分层架构代码生成** - Entity, Mapper, Service, Manager, Controller
2. **查询条件构建** - BaseCriteria 和 OperatorType 的使用
3. **分页查询** - Pagination 的正确使用方式
4. **Excel 导出** - ExcelWriter 和 DocHead 的使用

## 框架包名

所有 Starmie Framework 类都在以下包下：
```
io.github.starmoon1617.starmie.*
```

## 重要约定

- 泛型参数格式：`<Entity, ID类型, 用户标识类型>`
- 分页页码从 **0** 开始
- 驼峰字段自动转下划线

## 使用方式

在 Claude Code 中直接提问涉及 Starmie Framework 的开发问题，例如：
- "用 starmie 创建一个用户管理模块"
- "如何使用 BaseCriteria 构建复杂查询"
- "starmie 分页查询怎么写"
