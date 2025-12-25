# 测试点生成策略快速使用指南

## 📋 概述

本指南帮助您快速了解和使用测试点生成策略，包括：
- **M-单功能测试点生成**：针对单个功能的独立测试
- **F-组合交叉测试点生成**：多个功能组合和参数交叉测试
- **Q-非功能测试点生成**：性能、安全、并发等非功能测试

## 🚀 快速开始

### 1. 启用测试点生成策略

在 AI 对话中引用规范文件：

```
@quality/test-case-generation-strategy.zh-CN.md

请为以下函数生成测试点：
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
cp .test-strategy.yaml.example .test-strategy.yaml

# 编辑配置文件
vim .test-strategy.yaml
```

#### 方式二：在代码注释中指定

```typescript
/**
 * @test-strategy M-单功能测试点生成
 * @min-test-points 5
 * @require-boundary true
 */
function calculateDiscount(price: number, discountRate: number): number {
  // ...
}
```

## 📝 三种测试点类型详解

### M-单功能测试点（Monolithic）

**适用场景**：
- 单元测试
- 功能测试
- 单个函数/方法的测试

**生成规则**：
- ✅ 正向测试点：正常输入，预期正确输出
- ✅ 边界测试点：边界值输入（0、最大值、最小值）
- ✅ 异常测试点：无效输入，验证错误处理

**示例**：
```typescript
describe('calculateDiscount - M测试点', () => {
  // M-001: 正向测试点
  it('M-001: 正常价格和折扣率应返回正确折扣价格', () => {
    expect(calculateDiscount(100, 0.1)).toBe(90);
  });
  
  // M-002: 边界测试点
  it('M-002: 价格为0时应返回0', () => {
    expect(calculateDiscount(0, 0.1)).toBe(0);
  });
  
  // M-003: 异常测试点
  it('M-003: 负数价格应抛出错误', () => {
    expect(() => calculateDiscount(-100, 0.1)).toThrow('Invalid input');
  });
});
```

### F-组合交叉测试点（Functional Combination）

**适用场景**：
- 集成测试
- 系统测试
- 多参数组合测试

**生成方法**：
- **正交表（Orthogonal Array）**：使用数学正交表生成测试点
- **配对测试（Pairwise Testing）**：确保每对参数值至少组合一次

**优势**：
- 减少测试用例数量（通常减少 50-80%）
- 保持高覆盖率
- 发现参数组合相关的缺陷

**示例**：
```typescript
describe('registerUser - F组合测试点', () => {
  // 使用配对测试生成测试点
  const testCases = [
    ['valid', 'valid', 18, 'user'],      // F-001
    ['valid', 'invalid', 65, 'admin'],   // F-002
    ['invalid', 'valid', 18, 'admin'],   // F-003
    // ... 更多组合
  ];
  
  testCases.forEach(([username, email, age, role], index) => {
    it(`F-${String(index + 1).padStart(3, '0')}: 组合测试`, () => {
      // 测试逻辑
    });
  });
});
```

### Q-非功能测试点（Quality）

**适用场景**：
- 性能测试
- 安全测试
- 并发测试
- 压力测试

**测试类型**：
1. **性能测试**：响应时间、吞吐量、资源利用率
2. **安全测试**：SQL注入、XSS、CSRF、认证授权
3. **并发测试**：多用户并发、竞态条件、数据一致性
4. **压力测试**：极限负载、资源耗尽、降级策略

**示例**：
```typescript
describe('UserService - Q非功能测试点', () => {
  // Q-001: 性能测试点
  it('Q-001: API响应时间应小于100ms', async () => {
    const startTime = Date.now();
    await userService.getUser(1);
    const responseTime = Date.now() - startTime;
    expect(responseTime).toBeLessThan(100);
  });
  
  // Q-002: 安全测试点
  it('Q-002: SQL注入攻击应被阻止', async () => {
    const maliciousInput = "'; DROP TABLE users; --";
    await expect(userService.search(maliciousInput)).rejects.toThrow();
  });
  
  // Q-003: 并发测试点
  it('Q-003: 100个并发请求应全部成功', async () => {
    const promises = Array(100).fill(null).map(() => userService.getUser(1));
    const results = await Promise.all(promises);
    expect(results.every(r => r !== null)).toBe(true);
  });
});
```

## ⚙️ 检查策略配置

### 配置文件位置

- `.test-strategy.yaml` - YAML 格式（推荐）
- `.test-strategy.json` - JSON 格式
- `test-strategy.config.js` - JavaScript 格式（支持动态配置）

### 配置项说明

#### 覆盖率检查
```yaml
coverage:
  enabled: true
  line_coverage: 80        # 行覆盖率目标
  branch_coverage: 75      # 分支覆盖率目标
```

#### M-单功能测试点检查
```yaml
monolithic_tests:
  enabled: true
  min_test_points: 3       # 每个功能最少测试点数
  require_positive: true   # 必须包含正向测试点
  require_boundary: true   # 必须包含边界测试点
  require_negative: true   # 必须包含异常测试点
```

#### F-组合交叉测试点检查
```yaml
combination_tests:
  enabled: true
  method: "pairwise"       # 生成方法：orthogonal/pairwise/full
  max_test_points: 50     # 最大测试点数
  min_coverage: 80         # 最小组合覆盖率
```

#### Q-非功能测试点检查
```yaml
quality_tests:
  enabled: true
  performance:
    enabled: true
    min_test_points: 2
    response_time_ms: 100  # 响应时间阈值
  security:
    enabled: true
    min_test_points: 3
    owasp_top10: true      # 覆盖 OWASP Top 10
```

## 🎯 使用场景示例

### 场景 1：为新功能生成测试点

```
@quality/test-case-generation-strategy.zh-CN.md

请为以下新功能生成完整的测试点（M、F、Q）：

function validateEmail(email: string): boolean {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
}
```

**AI 将生成**：
- M-001 到 M-005：单功能测试点（正向、边界、异常）
- F-001 到 F-003：组合测试点（与其他验证函数组合）
- Q-001 到 Q-002：性能和安全测试点

### 场景 2：为 API 端点生成测试点

```
@quality/test-case-generation-strategy.zh-CN.md

请为以下 API 端点生成测试点：

POST /api/users
Body: { username, email, password, age }
```

**AI 将生成**：
- M 测试点：单个字段验证
- F 测试点：多个字段组合验证
- Q 测试点：性能、安全、并发测试

### 场景 3：自定义检查策略

```yaml
# .test-strategy.yaml
test_case_generation:
  monolithic_tests:
    min_test_points: 5      # 提高最低测试点数
    require_positive: true
    require_boundary: true
    require_negative: true
  
  quality_tests:
    performance:
      response_time_ms: 50   # 更严格的性能要求
```

## 📊 测试点命名规范

### 命名格式

```
{类型}-{序号}: {描述}
```

- **类型**：M（单功能）、F（组合）、Q（非功能）
- **序号**：3位数字，从 001 开始
- **描述**：清晰描述测试点的预期行为

### 示例

```
M-001: 正常价格和折扣率应返回正确折扣价格
F-002: 用户名和邮箱的有效组合应成功注册
Q-003: 100个并发请求应全部成功
```

## 🔍 验证和检查

### 自动检查

AI 将自动检查：
- ✅ 测试点数量是否符合要求
- ✅ 是否包含正向、边界、异常测试点
- ✅ 命名是否符合规范
- ✅ 覆盖率是否达标

### 手动验证

```bash
# 运行测试
npm test

# 检查覆盖率
npm run test:coverage

# 验证测试点命名
npm run test:validate-naming
```

## 📚 最佳实践

1. **优先使用 M 测试点**：为每个功能生成 M 测试点
2. **合理使用 F 测试点**：对于多参数函数，使用 F 测试点减少用例数
3. **关键功能使用 Q 测试点**：为核心功能生成 Q 测试点
4. **定期审查测试点**：确保测试点仍然有效和必要
5. **保持测试点独立**：每个测试点应独立运行，不依赖其他测试点

## 🛠️ 工具集成

### 配对测试工具

- **PICT**：Microsoft 的配对测试工具
- **AllPairs**：Python 实现的配对测试库
- **CTWedge**：在线组合测试工具

### 使用示例

```python
# 使用 AllPairs 生成配对测试点
from allpairs import all_pairs

parameters = [
    ['valid', 'invalid', 'empty'],
    ['gmail.com', 'yahoo.com', 'invalid'],
    [18, 30, 65]
]

test_cases = all_pairs(parameters)
for i, test_case in enumerate(test_cases, 1):
    print(f"F-{i:03d}: {test_case}")
```

## ❓ 常见问题

### Q1: 什么时候使用 M、F、Q 测试点？

- **M 测试点**：所有功能都应该有 M 测试点
- **F 测试点**：多参数函数或功能组合时使用
- **Q 测试点**：关键功能或性能敏感的功能使用

### Q2: 如何确定测试点数量？

- M 测试点：每个功能至少 3-5 个
- F 测试点：使用配对测试工具生成，通常少于全组合的 50%
- Q 测试点：每个非功能维度 2-3 个

### Q3: 如何验证测试点质量？

- 检查覆盖率报告
- 验证测试点独立性
- 确保包含边界和异常测试点
- 检查命名规范

## 📖 相关文档

- [测试点生成策略规范](./test-case-generation-strategy.zh-CN.md)
- [测试规范](./testing-spec.zh-CN.md)
- [开发需求规范](../core/requirements-spec.zh-CN.md)

---

**最后更新**：2025-01-XX  
**版本**：v1.0
