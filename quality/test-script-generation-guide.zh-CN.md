# 测试脚本生成检查策略快速使用指南

## 📋 概述

本指南帮助您快速了解和使用测试脚本生成检查策略，确保生成的测试脚本符合质量标准和最佳实践。

## 🚀 快速开始

### 1. 启用测试脚本检查策略

在 AI 对话中引用规范文件：

```
@quality/test-script-generation-check.zh-CN.md

请为以下函数生成测试脚本：
function calculateDiscount(price: number, discountRate: number): number {
  if (price < 0 || discountRate < 0 || discountRate > 1) {
    throw new Error('Invalid input');
  }
  return price * (1 - discountRate);
}
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

```typescript
/**
 * @test-check strict
 * @require-m-tests true
 * @min-test-points 5
 */
function calculateDiscount(price: number, discountRate: number): number {
  // ...
}
```

## 📝 14个检查项详解

### [检查 1] 结构完整性

**检查内容**：
- ✅ 测试文件命名规范（*.test.js, *.spec.js）
- ✅ 包含测试框架导入
- ✅ 包含被测试模块导入
- ✅ 包含测试套件（describe）
- ✅ 包含测试用例（it/test）

**示例**：
```typescript
// ✅ 正确
import { describe, it, expect } from '@jest/globals';
import { UserService } from '../src/UserService';

describe('UserService', () => {
  it('should create user', () => {
    // ...
  });
});
```

### [检查 2] 代码质量

**检查内容**：
- ✅ 无语法错误，可编译运行
- ✅ 单个测试用例不超过20行
- ✅ 使用工厂函数生成测试数据
- ✅ 避免硬编码数据

**示例**：
```typescript
// ✅ 正确：使用工厂函数
function createTestUser(overrides = {}) {
  return { id: '1', email: 'test@example.com', ...overrides };
}

it('should create user', () => {
  const user = createTestUser({ email: 'custom@example.com' });
  // ...
});
```

### [检查 3] 命名规范

**检查内容**：
- ✅ 测试文件：`{name}.test.{ext}`
- ✅ 测试套件：`describe('被测试单元名称')`
- ✅ 测试用例：`{type}-{seq:03d}: should {behavior}`
- ✅ 描述长度：10-100字符

**示例**：
```typescript
// ✅ 正确：符合命名规范
describe('UserService', () => {
  it('M-001: should create user with valid email', () => {});
  it('M-002: should throw error for invalid email', () => {});
});
```

### [检查 4] 依赖和导入

**检查内容**：
- ✅ 导入路径正确
- ✅ 所有导入的模块都存在
- ✅ 避免循环依赖

### [检查 5] 断言完整性

**检查内容**：
- ✅ 每个测试用例至少一个断言
- ✅ 断言验证正确的值或行为
- ✅ 避免无意义的断言

**示例**：
```typescript
// ✅ 正确：包含有意义的断言
it('M-001: should return discounted price', () => {
  const result = calculateDiscount(100, 0.1);
  expect(result).toBe(90);
});

// ❌ 错误：无意义的断言
it('should work', () => {
  expect(true).toBe(true);
});
```

### [检查 6] Mock和Stub使用

**检查内容**：
- ✅ 外部依赖必须Mock
- ✅ Mock设置正确
- ✅ 测试后清理Mock
- ✅ 禁止Mock核心逻辑

**示例**：
```typescript
// ✅ 正确：Mock外部依赖
beforeEach(() => {
  mockFetch = jest.fn().mockResolvedValue({ data: {} });
  global.fetch = mockFetch;
});

afterEach(() => {
  jest.clearAllMocks();
});
```

### [检查 7] 隔离性和清理

**检查内容**：
- ✅ 使用beforeEach/afterEach清理状态
- ✅ 每个测试使用独立数据
- ✅ 避免共享状态
- ✅ 测试可并行运行

### [检查 8] 异步处理

**检查内容**：
- ✅ 异步测试使用async/await
- ✅ 正确处理Promise
- ✅ 正确处理异步错误

**示例**：
```typescript
// ✅ 正确：正确处理异步
it('M-001: should fetch user', async () => {
  const user = await userService.getUser(1);
  expect(user).toBeDefined();
});
```

### [检查 9] 性能要求

**检查内容**：
- ✅ 单元测试 < 100ms
- ✅ 集成测试 < 1s
- ✅ 避免不必要的延迟

### [检查 10] 类型安全（TypeScript）

**检查内容**：
- ✅ 通过TypeScript类型检查
- ✅ 避免使用any类型
- ✅ Mock类型定义正确

### [检查 11] 错误处理

**检查内容**：
- ✅ 包含错误处理测试
- ✅ 错误断言正确
- ✅ 测试边界条件

**示例**：
```typescript
// ✅ 正确：包含错误处理测试
it('M-002: should throw error for invalid input', () => {
  expect(() => calculateDiscount(-100, 0.1)).toThrow('Invalid input');
});
```

### [检查 12] 可维护性

**检查内容**：
- ✅ 使用辅助函数减少重复
- ✅ 使用常量替代魔法值
- ✅ 代码重复率 < 30%

### [检查 13] 配置检查

**检查内容**：
- ✅ 测试框架配置正确
- ✅ 路径别名配置正确（如使用）

### [检查 14] 测试点对应关系

**检查内容**：
- ✅ 包含M测试点（单功能测试）
- ✅ 包含F测试点（组合测试，可选）
- ✅ 包含Q测试点（非功能测试，可选）
- ✅ 测试点命名格式正确

**示例**：
```typescript
describe('UserService', () => {
  // M-单功能测试点
  it('M-001: should create user with valid email', () => {});
  it('M-002: should throw error for invalid email', () => {});
  
  // F-组合测试点（可选）
  it('F-001: should handle combination of email and password', () => {});
  
  // Q-非功能测试点（可选）
  it('Q-001: should create user within 100ms', async () => {});
});
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
warn_threshold: 3  # 最多允许3个警告
# 检查项失败时发出警告，但不阻止测试脚本生成
```

### 检查项优先级

#### 关键检查项（必须通过）
- 结构完整性（语法错误）
- 依赖导入（不存在的模块）
- 断言完整性（至少一个断言）
- Mock使用（禁止Mock核心逻辑）
- 隔离性（禁止共享状态）
- 异步处理（禁止未处理的Promise）
- 类型安全（TypeScript类型检查）

#### 重要检查项（建议通过）
- 命名规范（测试点前缀）
- Mock使用（外部依赖Mock）
- 隔离性（独立数据）
- 错误处理（错误测试）

#### 一般检查项（可选）
- 性能要求（执行时间）
- 可维护性（代码重复率）
- 配置检查（覆盖率配置）

### 配置示例

```yaml
test_script_generation:
  check_mode: "strict"
  
  structure:
    enabled: true
    require_imports: true
    require_describe: true
  
  naming:
    enabled: true
    require_test_point_prefix: true
    test_case_pattern: "{type}-{seq:03d}: should {behavior}"
  
  test_point_mapping:
    enabled: true
    require_m_tests: true
    require_f_tests: false
    require_q_tests: false
```

## 🎯 使用场景示例

### 场景 1：生成单元测试脚本

```
@quality/test-script-generation-check.zh-CN.md

请为以下函数生成单元测试脚本：

function validateEmail(email: string): boolean {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
}
```

**AI 将生成**：
- ✅ 完整的测试脚本结构
- ✅ M-001 到 M-005：单功能测试点
- ✅ 符合所有检查项要求

### 场景 2：生成集成测试脚本

```
@quality/test-script-generation-check.zh-CN.md

请为以下API端点生成集成测试脚本：

POST /api/users
Body: { username, email, password }
```

**AI 将生成**：
- ✅ 包含M和F测试点
- ✅ 正确使用Mock
- ✅ 包含错误处理测试

### 场景 3：自定义检查策略

```yaml
# .test-script-check.yaml
test_script_generation:
  check_mode: "strict"
  
  naming:
    require_test_point_prefix: true
    test_case_pattern: "{type}-{seq:03d}: {description}"
  
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
    "naming": { "status": "failed", "reason": "缺少测试点前缀" },
    "assertions": { "status": "passed" },
    "mocks": { "status": "warning", "reason": "建议添加Mock清理" }
  },
  "suggestions": [
    "测试用例命名应包含测试点前缀（M-001, F-001等）",
    "建议在afterEach中清理Mock"
  ]
}
```

## 🔍 验证和检查

### 自动检查

AI 将自动检查：
- ✅ 测试脚本结构完整性
- ✅ 代码质量和命名规范
- ✅ 断言和Mock使用
- ✅ 测试点对应关系

### 手动验证

```bash
# 运行测试
npm test

# 检查TypeScript类型（如适用）
npm run type-check

# 运行lint检查
npm run lint
```

## 📚 最佳实践

1. **使用严格模式**：确保测试脚本质量
2. **包含M测试点**：所有功能都应该有M测试点
3. **正确使用Mock**：Mock外部依赖，不要Mock核心逻辑
4. **保持测试独立**：每个测试用例应该独立运行
5. **包含错误处理**：测试错误情况和边界条件
6. **使用工厂函数**：避免硬编码测试数据
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
test_script_generation:
  performance:
    enabled: false  # 禁用性能检查
```

### Q3: 测试脚本必须包含F和Q测试点吗？

**A**: 根据项目需求：
- M测试点：必须包含
- F测试点：多参数函数建议包含
- Q测试点：关键功能建议包含

### Q4: 如何自定义检查规则？

**A**: 在配置文件中修改：
```yaml
test_script_generation:
  naming:
    test_case_pattern: "自定义模式"
```

## 📖 相关文档

- [测试脚本生成检查策略规范](./test-script-generation-check.zh-CN.md)
- [测试点生成策略规范](./test-case-generation-strategy.zh-CN.md)
- [测试规范](./testing-spec.zh-CN.md)
- [开发需求规范](../core/requirements-spec.zh-CN.md)

---

**最后更新**：2025-01-XX  
**版本**：v1.0
