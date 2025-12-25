# RobotFramework 测试脚本生成检查策略快速使用指南

## 📋 概述

本指南帮助您快速了解和使用 RobotFramework 测试脚本生成检查策略，确保生成的测试脚本符合质量标准和最佳实践。

## 🚀 快速开始

### 1. 启用测试脚本检查策略

在 AI 对话中引用规范文件：

```
@quality/test-script-generation-check.zh-CN.md

请为以下功能生成 RobotFramework 测试脚本：
- 用户登录功能
- 输入：用户名、密码
- 验证：登录成功/失败
```

### 2. 配置检查策略

#### 方式一：使用配置文件（推荐）

```bash
# 复制配置文件模板
cp .test-script-check.yaml.example .test-script-check.yaml

# 编辑配置文件
vim .test-script-check.yaml
```

#### 方式二：在代码注释中指定

```robot
*** Settings ***
# @test-check strict
# @require-m-tests true
# @min-test-points 5
Documentation    用户登录功能测试
Library          SeleniumLibrary
```

## 📝 14个检查项详解

### [检查 1] 文件结构完整性

**检查内容**：
- ✅ 文件扩展名为 .robot
- ✅ 包含 *** Settings *** 部分
- ✅ 包含 *** Test Cases *** 部分
- ✅ 各部分顺序正确

**示例**：
```robot
*** Settings ***
Documentation    用户登录功能测试
Library          SeleniumLibrary

*** Test Cases ***
M-001: 用户登录应成功
    [Documentation]    测试用户登录功能
    [Tags]    smoke    login
    打开登录页面
    输入用户名    ${USERNAME}
    输入密码    ${PASSWORD}
    点击登录按钮
    验证登录成功
```

### [检查 2] Settings 部分配置

**检查内容**：
- ✅ 包含必要的库导入
- ✅ 库名称拼写正确
- ✅ 资源文件路径正确

**示例**：
```robot
*** Settings ***
Documentation    用户管理功能测试
Library          SeleniumLibrary
Library          Collections
Resource         ../resources/common.robot
Test Setup       打开浏览器
Test Teardown    关闭浏览器
```

### [检查 3] 测试用例结构

**检查内容**：
- ✅ 测试用例名称符合规范（M-001, F-001, Q-001）
- ✅ 包含 [Documentation]（建议）
- ✅ 包含 [Tags]（建议）
- ✅ 包含测试步骤和断言

**示例**：
```robot
*** Test Cases ***
M-001: 用户使用有效凭据登录应成功
    [Documentation]    测试用户使用有效用户名和密码登录成功
    [Tags]    smoke    login    positive
    [Setup]    打开登录页面
    输入用户名    ${VALID_USERNAME}
    输入密码    ${VALID_PASSWORD}
    点击登录按钮
    验证登录成功    ${VALID_USERNAME}
    [Teardown]    退出登录
```

### [检查 4] 关键字使用

**检查内容**：
- ✅ 关键字名称正确
- ✅ 关键字参数匹配
- ✅ 关键字来自已导入的库或资源文件

**示例**：
```robot
*** Test Cases ***
M-001: 用户登录测试
    打开登录页面
    输入用户名    ${USERNAME}
    输入密码    ${PASSWORD}
    点击登录按钮
    验证登录成功

*** Keywords ***
打开登录页面
    Go To    ${LOGIN_URL}
    Wait Until Page Contains Element    id=username
```

### [检查 5] 变量使用

**检查内容**：
- ✅ 变量格式正确（${var}, @{list}, &{dict}）
- ✅ 避免硬编码值
- ✅ 变量名称有意义

**示例**：
```robot
*** Variables ***
${LOGIN_URL}         https://example.com/login
${VALID_USERNAME}    testuser
${VALID_PASSWORD}    testpass123

*** Test Cases ***
M-001: 用户登录测试
    Go To    ${LOGIN_URL}
    Input Text    id=username    ${VALID_USERNAME}
    Input Text    id=password    ${VALID_PASSWORD}
```

### [检查 6] 测试点命名规范

**检查内容**：
- ✅ 测试用例名称格式：{类型}-{序号}: {描述}
- ✅ 测试点类型：M（单功能）、F（组合）、Q（非功能）
- ✅ 测试点序号：3位数字（001, 002, 003...）

**示例**：
```robot
*** Test Cases ***
M-001: 用户使用有效凭据登录应成功
M-002: 用户使用无效密码登录应失败
F-001: 用户名和密码组合验证应覆盖所有边界情况
Q-001: 登录接口响应时间应小于100毫秒
```

### [检查 7] 断言和验证

**检查内容**：
- ✅ 每个测试用例至少一个断言
- ✅ 使用合适的断言关键字
- ✅ 断言验证正确的值或状态

**示例**：
```robot
*** Test Cases ***
M-001: 用户登录应成功
    打开登录页面
    输入用户名    ${USERNAME}
    输入密码    ${PASSWORD}
    点击登录按钮
    Wait Until Page Contains    欢迎
    Page Should Contain    欢迎, ${USERNAME}
    Location Should Be    ${HOME_URL}
```

### [检查 8] 测试数据和Fixture

**检查内容**：
- ✅ 测试数据使用变量
- ✅ Setup 和 Teardown 配置正确
- ✅ 测试数据独立

**示例**：
```robot
*** Test Cases ***
M-001: 用户登录应成功
    [Setup]    打开浏览器并导航到登录页
    [Teardown]    关闭浏览器
    输入用户名    ${VALID_USERNAME}
    输入密码    ${VALID_PASSWORD}
    点击登录按钮
    验证登录成功
```

### [检查 9] 标签使用

**检查内容**：
- ✅ 测试用例包含有意义的标签
- ✅ 标签命名规范

**示例**：
```robot
*** Test Cases ***
M-001: 用户登录应成功
    [Tags]    smoke    login    positive

M-002: 用户登录失败应显示错误
    [Tags]    login    negative

Q-001: 登录接口性能测试
    [Tags]    performance    api
```

### [检查 10] 文档完整性

**检查内容**：
- ✅ 测试用例包含 [Documentation]
- ✅ 用户定义关键字包含文档

**示例**：
```robot
*** Test Cases ***
M-001: 用户使用有效凭据登录应成功
    [Documentation]    测试用户使用有效的用户名和密码登录系统
    ...                验证登录成功后跳转到首页并显示欢迎信息
    [Tags]    smoke    login
    打开登录页面
    输入用户名    ${VALID_USERNAME}
    输入密码    ${VALID_PASSWORD}
    点击登录按钮
    验证登录成功
```

### [检查 11] 资源文件和库导入

**检查内容**：
- ✅ 资源文件路径正确
- ✅ 库名称拼写正确
- ✅ 所有导入的库和资源文件存在

### [检查 12] 测试用例独立性

**检查内容**：
- ✅ 每个测试用例独立运行
- ✅ 使用 Setup 和 Teardown 确保环境隔离
- ✅ 测试用例不依赖执行顺序

### [检查 13] 错误处理

**检查内容**：
- ✅ 包含错误处理测试用例
- ✅ 测试边界条件和异常输入
- ✅ 验证错误消息和状态

**示例**：
```robot
*** Test Cases ***
M-002: 用户名为空时登录应显示错误提示
    [Documentation]    测试用户名为空时的错误处理
    [Tags]    login    negative    validation
    打开登录页面
    输入密码    ${VALID_PASSWORD}
    点击登录按钮
    等待错误提示显示
    验证错误消息    用户名不能为空
```

### [检查 14] 测试点对应关系

**检查内容**：
- ✅ 包含M测试点（单功能测试，必须）
- ✅ 包含F测试点（组合测试，可选）
- ✅ 包含Q测试点（非功能测试，可选）

**示例**：
```robot
*** Test Cases ***
# M-单功能测试点
M-001: 用户使用有效凭据登录应成功
M-002: 用户使用无效密码登录应失败

# F-组合测试点
F-001: 用户名和密码组合验证应覆盖所有边界情况
    [Template]    验证登录组合
    ${VALID_USERNAME}    ${VALID_PASSWORD}    ${TRUE}
    ${VALID_USERNAME}    ${INVALID_PASSWORD}    ${FALSE}

# Q-非功能测试点
Q-001: 登录接口响应时间应小于100毫秒
Q-002: 登录功能应能处理100个并发请求
```

## ⚙️ 检查策略配置

### 检查模式

#### 严格模式（Strict Mode）
```yaml
check_mode: "strict"
# 所有检查项必须通过，否则测试脚本生成失败
```

#### 宽松模式（Lenient Mode）
```yaml
check_mode: "lenient"
warn_threshold: 3
# 检查项失败时发出警告，但不阻止测试脚本生成
```

### 配置示例

```yaml
robotframework_test_script:
  check_mode: "strict"
  
  # 文件结构检查
  structure:
    require_settings: true
    require_test_cases: true
  
  # 测试点命名检查
  test_point_naming:
    require_test_point_prefix: true
    pattern: "{type}-{seq:03d}: {description}"
  
  # 测试点对应关系检查
  test_point_mapping:
    require_m_tests: true
    require_f_tests: false
    require_q_tests: false
```

## 🎯 使用场景示例

### 场景 1：生成Web UI测试脚本

```
@quality/test-script-generation-check.zh-CN.md

请为以下功能生成 RobotFramework Web UI 测试脚本：
- 用户登录功能
- 输入：用户名、密码
- 验证：登录成功/失败
```

**AI 将生成**：
- ✅ 完整的 .robot 文件结构
- ✅ M-001 到 M-005：单功能测试点
- ✅ 使用 SeleniumLibrary
- ✅ 符合所有检查项要求

### 场景 2：生成API测试脚本

```
@quality/test-script-generation-check.zh-CN.md

请为以下API端点生成 RobotFramework API 测试脚本：

POST /api/users/login
Body: { username, password }
Response: { token, user_id }
```

**AI 将生成**：
- ✅ 使用 RequestsLibrary
- ✅ 包含M和F测试点
- ✅ 包含Q性能测试点

### 场景 3：自定义检查策略

```yaml
# .test-script-check.yaml
robotframework_test_script:
  check_mode: "strict"
  
  test_point_naming:
    require_test_point_prefix: true
  
  test_point_mapping:
    require_m_tests: true
    m_test_requirements:
      min_count: 5  # 提高最低测试点数
```

## 📊 检查报告

### 检查报告格式

检查完成后会生成报告，包含：
- ✅ 通过的检查项
- ❌ 失败的检查项
- ⚠️ 警告项
- 💡 改进建议

### 报告示例

```json
{
  "summary": {
    "total_checks": 14,
    "passed": 12,
    "failed": 1,
    "warnings": 1
  },
  "checks": {
    "structure": { "status": "passed" },
    "test_point_naming": { "status": "failed", "reason": "缺少测试点前缀" },
    "assertions": { "status": "passed" },
    "keywords": { "status": "warning", "reason": "建议添加关键字文档" }
  },
  "suggestions": [
    "测试用例命名应包含测试点前缀（M-001, F-001等）",
    "建议为关键字添加 [Documentation]"
  ]
}
```

## 🔍 验证和检查

### 自动检查

AI 将自动检查：
- ✅ 文件结构完整性
- ✅ 关键字和变量使用
- ✅ 测试点命名规范
- ✅ 断言完整性

### 手动验证

```bash
# 运行 RobotFramework 测试
robot tests/login.robot

# 检查语法
robot --dryrun tests/login.robot

# 生成报告
robot --report report.html tests/login.robot
```

## 📚 最佳实践

1. **使用严格模式**：确保测试脚本质量
2. **包含M测试点**：所有功能都应该有M测试点
3. **使用变量**：避免硬编码值
4. **保持测试独立**：每个测试用例应该独立运行
5. **包含错误处理**：测试错误情况和边界条件
6. **使用Setup/Teardown**：确保测试环境隔离
7. **遵循命名规范**：使用清晰的测试用例描述

## ❓ 常见问题

### Q1: 检查失败怎么办？

**A**: 根据检查报告修复问题：
- 关键检查项失败：必须修复
- 重要检查项失败：建议修复
- 一般检查项失败：可选修复

### Q2: 如何跳过某些检查项？

**A**: 在配置文件中禁用：
```yaml
robotframework_test_script:
  documentation:
    enabled: false  # 禁用文档检查
```

### Q3: 测试脚本必须包含F和Q测试点吗？

**A**: 根据项目需求：
- M测试点：必须包含
- F测试点：多参数组合时建议包含
- Q测试点：关键功能建议包含

### Q4: 如何自定义检查规则？

**A**: 在配置文件中修改：
```yaml
robotframework_test_script:
  test_point_naming:
    pattern: "自定义模式"
```

## 📖 相关文档

- [RobotFramework 测试脚本生成检查策略规范](./test-script-generation-check.zh-CN.md)
- [测试点生成策略规范](./test-case-generation-strategy.zh-CN.md)
- [测试规范](./testing-spec.zh-CN.md)
- [RobotFramework 官方文档](https://robotframework.org/)

---

**最后更新**：2025-01-XX  
**版本**：v1.0
