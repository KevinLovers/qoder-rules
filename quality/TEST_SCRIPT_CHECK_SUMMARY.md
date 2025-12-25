# RobotFramework 测试脚本生成检查策略 - 创建总结

## 📋 概述

已为 RobotFramework 测试脚本生成创建了完整的检查策略规范和相关配置文件，确保生成的测试脚本符合质量标准和最佳实践。

**注意**：本规范专门针对 RobotFramework 测试脚本（.robot 文件），不适用于其他测试框架。

## 📁 已创建的文件

### 1. 核心规范文档

#### `test-script-generation-check.zh-CN.md`
**RobotFramework 测试脚本生成检查策略规范** - 包含14个检查项，专门针对 .robot 文件：
- [检查 1] RobotFramework 文件结构完整性（Settings、Test Cases、Keywords）
- [检查 2] Settings 部分配置完整性（库导入、资源文件）
- [检查 3] 测试用例结构完整性（Documentation、Tags、Setup/Teardown）
- [检查 4] 关键字使用规范（库关键字、用户定义关键字）
- [检查 5] 变量使用规范（${var}, @{list}, &{dict}）
- [检查 6] 测试点命名规范（M-001, F-001, Q-001）
- [检查 7] 断言和验证完整性（Should Be Equal、Page Should Contain等）
- [检查 8] 测试数据和Fixture管理（Variables、Setup/Teardown）
- [检查 9] 标签使用规范（smoke、regression、positive等）
- [检查 10] 文档完整性（测试用例和关键字文档）
- [检查 11] 资源文件和库导入（Library、Resource）
- [检查 12] 测试用例独立性（独立运行、环境隔离）
- [检查 13] 错误处理和异常场景（负向测试、边界条件）
- [检查 14] 测试点对应关系（M/F/Q测试点）

### 2. 配置文件模板

#### `.test-script-check.yaml.example`
YAML格式的检查策略配置模板，包含：
- 14个检查项的详细配置
- 项目类型特定配置（Web应用、CLI工具、库/SDK）
- 检查项优先级配置
- 检查报告配置

#### `.test-script-check.json.example`
JSON格式的检查策略配置模板，与YAML版本内容相同。

### 3. 使用指南

#### `test-script-generation-guide.zh-CN.md`
快速使用指南，包含：
- 快速开始步骤
- 14个检查项详解
- 配置示例
- 使用场景示例
- 常见问题解答

## 🚀 快速使用

### 步骤 1: 复制配置文件

```bash
# 复制配置文件模板
cp .test-script-check.yaml.example .test-script-check.yaml
```

### 步骤 2: 配置检查策略

编辑 `.test-script-check.yaml`，根据项目需求调整配置：

```yaml
test_script_generation:
  check_mode: "strict"  # strict | lenient
  
  structure:
    enabled: true
    require_imports: true
  
  naming:
    enabled: true
    require_test_point_prefix: true
  
  test_point_mapping:
    enabled: true
    require_m_tests: true
    require_f_tests: false
    require_q_tests: false
```

### 步骤 3: 在AI对话中使用

```
@quality/test-script-generation-check.zh-CN.md

请为以下功能生成 RobotFramework 测试脚本：
- 用户登录功能
- 输入：用户名、密码
- 验证：登录成功/失败
```

## 📊 检查策略配置说明

### 检查模式

#### 严格模式（Strict Mode）
```yaml
check_mode: "strict"
```
- 所有检查项必须通过
- 失败时阻止测试脚本生成
- 适用于生产环境

#### 宽松模式（Lenient Mode）
```yaml
check_mode: "lenient"
warn_threshold: 3
```
- 检查项失败时发出警告
- 不阻止测试脚本生成
- 适用于开发阶段

### 检查项优先级

#### 关键检查项（必须通过）
- 文件结构完整性（Settings、Test Cases部分）
- 关键字存在性（所有关键字必须存在）
- 断言完整性（每个测试用例至少一个断言）
- 测试点命名规范（必须包含M/F/Q前缀）
- 资源文件和库导入（所有导入必须存在）

#### 重要检查项（建议通过）
- 测试用例文档（Documentation）
- 标签使用（Tags）
- 变量使用（避免硬编码）
- 测试用例独立性（Setup/Teardown）
- 错误处理测试（负向测试）

#### 一般检查项（可选）
- 性能要求（执行时间）
- 可维护性（代码重复率）
- 配置检查（覆盖率配置）

## 🎯 检查项与测试点生成策略的对应关系

### M-单功能测试点检查
- 必须包含M测试点
- 每个功能最少3个测试点
- 必须包含正向、边界、异常测试点

### F-组合交叉测试点检查
- 可选包含F测试点
- 使用配对测试或正交表生成
- 减少测试用例数量50-80%

### Q-非功能测试点检查
- 可选包含Q测试点
- 包含性能、安全、并发测试
- 关键功能建议包含

## 📝 RobotFramework 测试脚本命名规范

### 文件命名
```
{功能名称}.robot
例如：user_login.robot
```

### 测试用例命名
```robot
*** Test Cases ***
M-001: 用户使用有效凭据登录应成功
F-001: 用户名和密码组合验证应覆盖所有边界情况
Q-001: 登录接口响应时间应小于100毫秒
```

### 关键字命名
```robot
*** Keywords ***
打开登录页面
输入用户名
    [Arguments]    ${username}
输入密码
    [Arguments]    ${password}
验证登录成功
    [Arguments]    ${expected_username}
```

## 🔗 与其他规范的集成

### 与测试规范集成
- 遵循 `testing-spec.zh-CN.md` 的测试完整性要求
- 遵循测试命名约定
- 遵循关键字和变量使用规范

### 与测试点生成策略集成
- 确保测试脚本包含M/F/Q测试点
- 遵循测试点命名规范
- 符合测试点数量要求

### 与开发需求规范集成
- 确保测试脚本可编译运行
- 使用真实存在的API和库
- 遵循代码质量标准

## 📚 相关文档

- [测试脚本生成检查策略规范](./test-script-generation-check.zh-CN.md)
- [测试脚本生成使用指南](./test-script-generation-guide.zh-CN.md)
- [测试点生成策略规范](./test-case-generation-strategy.zh-CN.md)
- [测试规范](./testing-spec.zh-CN.md)

## ✅ 检查清单

使用 RobotFramework 测试脚本生成检查策略时，确保：

- [ ] 已复制配置文件模板
- [ ] 已根据项目类型配置检查策略（Web UI / API / Database）
- [ ] 已在AI对话中引用检查策略规范
- [ ] 已验证生成的 .robot 文件符合检查项要求
- [ ] 已使用 `robot --dryrun` 验证语法
- [ ] 已查看检查报告并修复问题
- [ ] 已运行测试验证功能正确性

## 🔧 RobotFramework 特定要求

### 文件格式
- 文件扩展名必须是 `.robot`
- 必须包含 `*** Settings ***` 部分
- 必须包含 `*** Test Cases ***` 部分

### 常用库
- **Web UI测试**：SeleniumLibrary
- **API测试**：RequestsLibrary
- **数据库测试**：DatabaseLibrary

### 测试用例格式
```robot
*** Test Cases ***
M-001: 测试用例名称
    [Documentation]    测试用例文档
    [Tags]    smoke    login
    [Setup]    测试前准备
    测试步骤1
    测试步骤2
    验证步骤
    [Teardown]    测试后清理
```

---

**创建日期**：2025-01-XX  
**版本**：v1.0  
**适用范围**：RobotFramework 测试脚本（.robot 文件）
