---
trigger: manual
---

# 测试点生成策略规范 v1.0
# ============================================
# AI 辅助开发的测试点生成标准和要求
# 通过将 [ENABLED] 更改为 [DISABLED] 来启用/禁用规则
#
# 使用方法：
# 1. 将此文件放在项目根目录或 quality 目录
# 2. 根据项目需求启用/禁用规则
# 3. 在 AI 对话中使用 @test-case-generation-strategy.zh-CN.md 引用
# 4. AI 将只遵循 ENABLED 的规则
#
# 依赖规范：testing-spec.zh-CN.md、requirements-spec.zh-CN.md
# 最后更新：2025-01-XX
# ============================================

## [策略 1] M-单功能测试点生成 [ENABLED]
# 针对单个功能的独立测试点生成

STATUS: ENABLED
说明：
- M（Monolithic）- 单功能测试点：针对单个功能模块或函数的独立测试
- 每个功能点生成独立的测试用例
- 测试点应覆盖功能的正向路径、边界条件和异常情况
- 适用于单元测试和功能测试

生成规则：
1. **功能识别**：识别代码中的独立功能单元（函数、方法、类）
2. **输入分析**：分析功能的输入参数和前置条件
3. **输出验证**：确定功能的预期输出和结果
4. **路径覆盖**：生成覆盖主要执行路径的测试点
5. **边界测试**：生成边界值测试点（最小值、最大值、临界值）
6. **异常测试**：生成异常输入和错误处理的测试点

测试点分类：
- **正向测试点**：正常输入，预期正确输出
- **边界测试点**：边界值输入，验证边界处理
- **异常测试点**：无效输入，验证错误处理
- **空值测试点**：null、undefined、空字符串等
- **类型测试点**：类型不匹配、类型转换等

示例：
```typescript
// 功能：计算折扣价格
function calculateDiscount(price: number, discountRate: number): number {
  if (price < 0 || discountRate < 0 || discountRate > 1) {
    throw new Error('Invalid input');
  }
  return price * (1 - discountRate);
}

// M-单功能测试点生成：
describe('calculateDiscount - M测试点', () => {
  // 正向测试点
  it('M-001: 正常价格和折扣率应返回正确折扣价格', () => {
    expect(calculateDiscount(100, 0.1)).toBe(90);
  });
  
  // 边界测试点
  it('M-002: 价格为0时应返回0', () => {
    expect(calculateDiscount(0, 0.1)).toBe(0);
  });
  
  it('M-003: 折扣率为0时应返回原价', () => {
    expect(calculateDiscount(100, 0)).toBe(100);
  });
  
  it('M-004: 折扣率为1时应返回0', () => {
    expect(calculateDiscount(100, 1)).toBe(0);
  });
  
  // 异常测试点
  it('M-005: 负数价格应抛出错误', () => {
    expect(() => calculateDiscount(-100, 0.1)).toThrow('Invalid input');
  });
  
  it('M-006: 折扣率大于1应抛出错误', () => {
    expect(() => calculateDiscount(100, 1.5)).toThrow('Invalid input');
  });
});
```

检查策略：
- ✅ 每个功能至少生成 3-5 个测试点
- ✅ 必须包含正向、边界、异常三类测试点
- ✅ 测试点命名格式：M-{序号}: {测试描述}
- ✅ 测试点应独立，不依赖其他测试点


## [策略 2] F-组合交叉测试点生成 [ENABLED]
# 多个功能组合和参数交叉的测试点生成

STATUS: ENABLED
说明：
- F（Functional Combination）- 组合交叉测试点：测试多个功能组合或参数交叉的情况
- 使用正交表、配对测试等技术生成测试点
- 适用于集成测试和系统测试
- 减少测试用例数量，提高测试覆盖率

生成规则：
1. **功能组合识别**：识别需要组合测试的功能模块
2. **参数交叉分析**：分析多个参数的取值组合
3. **正交表生成**：使用正交表（Orthogonal Array）生成测试点
4. **配对测试**：使用配对测试（Pairwise Testing）生成测试点
5. **组合覆盖**：确保所有重要的参数组合被覆盖

正交表方法：
- L4(2^3)：4个测试用例覆盖3个2值参数的所有组合
- L8(2^7)：8个测试用例覆盖7个2值参数
- L9(3^4)：9个测试用例覆盖4个3值参数

配对测试方法：
- 确保每对参数值至少组合一次
- 使用工具如 PICT、AllPairs 生成测试点

示例：
```typescript
// 功能：用户注册（多个参数组合）
function registerUser(username: string, email: string, age: number, role: string): User {
  // 注册逻辑
}

// F-组合交叉测试点生成：
describe('registerUser - F组合测试点', () => {
  // 使用正交表生成测试点
  const testCases = [
    // username, email, age, role
    ['valid', 'valid', 18, 'user'],      // F-001
    ['valid', 'invalid', 65, 'admin'],   // F-002
    ['invalid', 'valid', 18, 'admin'],   // F-003
    ['invalid', 'invalid', 65, 'user'],  // F-004
    ['empty', 'valid', 30, 'user'],      // F-005
    ['valid', 'empty', 30, 'admin'],     // F-006
  ];
  
  testCases.forEach(([username, email, age, role], index) => {
    it(`F-${String(index + 1).padStart(3, '0')}: 组合测试 - username:${username}, email:${email}, age:${age}, role:${role}`, () => {
      // 测试逻辑
    });
  });
  
  // 配对测试点
  it('F-007: 配对测试 - 用户名和邮箱的有效组合', () => {
    // 确保 username 和 email 的有效组合被测试
  });
  
  it('F-008: 配对测试 - 年龄和角色的边界组合', () => {
    // 确保 age 和 role 的边界组合被测试
  });
});
```

检查策略：
- ✅ 使用正交表或配对测试生成测试点
- ✅ 测试点数量应少于全组合数量（通常减少 50-80%）
- ✅ 测试点命名格式：F-{序号}: {组合描述}
- ✅ 确保关键参数组合被覆盖
- ✅ 记录使用的正交表或配对策略


## [策略 3] Q-非功能测试点生成 [ENABLED]
# 非功能性需求的测试点生成

STATUS: ENABLED
说明：
- Q（Quality）- 非功能测试点：针对性能、安全、可用性等非功能需求的测试
- 包括性能测试、安全测试、并发测试、压力测试等
- 适用于系统测试和验收测试

测试类型：
1. **性能测试点**：响应时间、吞吐量、资源利用率
2. **安全测试点**：认证、授权、数据加密、注入攻击防护
3. **并发测试点**：多用户并发、竞态条件、死锁
4. **压力测试点**：极限负载、资源耗尽、降级策略
5. **可用性测试点**：容错、恢复、故障转移
6. **兼容性测试点**：浏览器、操作系统、设备兼容
7. **可维护性测试点**：代码质量、可读性、可扩展性

生成规则：
1. **需求分析**：识别非功能需求（性能指标、安全要求等）
2. **指标定义**：定义可量化的测试指标
3. **场景设计**：设计测试场景和负载模型
4. **工具选择**：选择合适的测试工具（JMeter、LoadRunner等）
5. **阈值设定**：设定性能阈值和通过标准

示例：
```typescript
// Q-非功能测试点生成：

describe('UserService - Q非功能测试点', () => {
  // 性能测试点
  describe('Q-性能测试', () => {
    it('Q-001: API响应时间应小于100ms', async () => {
      const startTime = Date.now();
      await userService.getUser(1);
      const responseTime = Date.now() - startTime;
      expect(responseTime).toBeLessThan(100);
    });
    
    it('Q-002: 并发100用户时吞吐量应大于1000 req/s', async () => {
      // 并发测试逻辑
    });
    
    it('Q-003: 内存使用不应超过100MB', () => {
      // 内存测试逻辑
    });
  });
  
  // 安全测试点
  describe('Q-安全测试', () => {
    it('Q-004: SQL注入攻击应被阻止', async () => {
      const maliciousInput = "'; DROP TABLE users; --";
      await expect(userService.search(maliciousInput)).rejects.toThrow();
    });
    
    it('Q-005: XSS攻击应被过滤', async () => {
      const xssInput = '<script>alert("XSS")</script>';
      const result = await userService.createUser({ name: xssInput });
      expect(result.name).not.toContain('<script>');
    });
    
    it('Q-006: 未授权访问应被拒绝', async () => {
      await expect(userService.deleteUser(1)).rejects.toThrow('Unauthorized');
    });
  });
  
  // 并发测试点
  describe('Q-并发测试', () => {
    it('Q-007: 100个并发请求应全部成功', async () => {
      const promises = Array(100).fill(null).map(() => userService.getUser(1));
      const results = await Promise.all(promises);
      expect(results.every(r => r !== null)).toBe(true);
    });
    
    it('Q-008: 并发更新不应产生数据竞争', async () => {
      // 并发更新测试逻辑
    });
  });
  
  // 压力测试点
  describe('Q-压力测试', () => {
    it('Q-009: 10000个请求下系统应保持稳定', async () => {
      // 压力测试逻辑
    });
    
    it('Q-010: 资源耗尽时应优雅降级', async () => {
      // 降级测试逻辑
    });
  });
});
```

检查策略：
- ✅ 每个非功能维度至少生成 2-3 个测试点
- ✅ 测试点命名格式：Q-{序号}: {测试描述}
- ✅ 性能指标应可量化（响应时间、吞吐量等）
- ✅ 安全测试点应覆盖 OWASP Top 10
- ✅ 并发测试点应验证数据一致性
- ✅ 压力测试点应验证系统极限


## [策略 4] 测试点生成检查策略配置 [ENABLED]
# 如何设置和配置测试点生成的检查策略

STATUS: ENABLED
说明：
- 定义测试点生成的检查规则和验证标准
- 确保生成的测试点符合质量和覆盖率要求
- 支持自定义检查策略配置

检查策略配置项：

### 4.1 覆盖率检查
```yaml
coverage:
  enabled: true
  line_coverage: 80        # 行覆盖率目标（%）
  branch_coverage: 75      # 分支覆盖率目标（%）
  function_coverage: 80    # 函数覆盖率目标（%）
  statement_coverage: 80   # 语句覆盖率目标（%）
```

### 4.2 M-单功能测试点检查
```yaml
monolithic_tests:
  enabled: true
  min_test_points: 3       # 每个功能最少测试点数
  require_positive: true   # 必须包含正向测试点
  require_boundary: true   # 必须包含边界测试点
  require_negative: true   # 必须包含异常测试点
  naming_pattern: "M-{seq:03d}: {description}"
```

### 4.3 F-组合交叉测试点检查
```yaml
combination_tests:
  enabled: true
  method: "pairwise"       # 生成方法：orthogonal/pairwise/full
  max_test_points: 50     # 最大测试点数（避免爆炸）
  min_coverage: 80         # 最小组合覆盖率（%）
  require_key_combinations: true  # 必须包含关键组合
  naming_pattern: "F-{seq:03d}: {description}"
```

### 4.4 Q-非功能测试点检查
```yaml
quality_tests:
  enabled: true
  performance:
    enabled: true
    min_test_points: 2
    metrics: ["response_time", "throughput", "memory"]
  security:
    enabled: true
    min_test_points: 3
    owasp_top10: true      # 覆盖 OWASP Top 10
  concurrency:
    enabled: true
    min_test_points: 2
  stress:
    enabled: true
    min_test_points: 1
  naming_pattern: "Q-{seq:03d}: {description}"
```

### 4.5 测试点命名检查
```yaml
naming:
  enabled: true
  pattern: "{type}-{seq:03d}: {description}"
  types: ["M", "F", "Q"]
  description_required: true
  description_min_length: 10
```

### 4.6 测试点质量检查
```yaml
quality:
  enabled: true
  require_assertions: true      # 必须包含断言
  require_independence: true    # 测试点必须独立
  require_cleanup: true         # 必须包含清理逻辑
  max_test_duration: 1000       # 最大执行时间（ms）
```

配置示例（JSON格式）：
```json
{
  "test_case_generation": {
    "coverage": {
      "enabled": true,
      "line_coverage": 80,
      "branch_coverage": 75
    },
    "monolithic_tests": {
      "enabled": true,
      "min_test_points": 3,
      "require_positive": true,
      "require_boundary": true,
      "require_negative": true
    },
    "combination_tests": {
      "enabled": true,
      "method": "pairwise",
      "max_test_points": 50,
      "min_coverage": 80
    },
    "quality_tests": {
      "enabled": true,
      "performance": {
        "enabled": true,
        "min_test_points": 2
      },
      "security": {
        "enabled": true,
        "min_test_points": 3,
        "owasp_top10": true
      }
    },
    "naming": {
      "enabled": true,
      "pattern": "{type}-{seq:03d}: {description}",
      "description_required": true
    }
  }
}
```

配置文件位置：
- `.test-strategy.yaml` - YAML 格式配置
- `.test-strategy.json` - JSON 格式配置
- `test-strategy.config.js` - JavaScript 格式配置（支持动态配置）

使用方式：
```bash
# 在项目根目录创建配置文件
cat > .test-strategy.yaml << EOF
test_case_generation:
  coverage:
    enabled: true
    line_coverage: 80
  monolithic_tests:
    enabled: true
    min_test_points: 3
EOF

# AI 将自动读取并应用配置
```

检查策略验证：
- ✅ 配置文件格式正确
- ✅ 所有必填项已配置
- ✅ 数值范围合理（覆盖率 0-100，测试点数 > 0）
- ✅ 命名模式符合规范
- ✅ 启用的检查项与项目类型匹配


## [策略 5] 测试点生成工作流 [ENABLED]
# 测试点生成的完整工作流程

STATUS: ENABLED
说明：
- 定义从代码分析到测试点生成的完整流程
- 确保测试点生成的系统性和完整性

工作流程：

### 步骤 1: 代码分析
1. 识别待测试的功能模块
2. 分析函数签名和参数
3. 识别依赖关系和调用链
4. 分析业务逻辑和分支条件

### 步骤 2: 测试策略选择
1. 根据功能类型选择测试策略（M/F/Q）
2. 确定测试优先级和范围
3. 选择测试工具和框架

### 步骤 3: M-单功能测试点生成
1. 为每个功能生成正向测试点
2. 生成边界值测试点
3. 生成异常处理测试点
4. 验证测试点独立性

### 步骤 4: F-组合交叉测试点生成
1. 识别需要组合测试的功能
2. 分析参数组合空间
3. 使用正交表或配对测试生成测试点
4. 验证组合覆盖率

### 步骤 5: Q-非功能测试点生成
1. 识别非功能需求
2. 定义性能和安全指标
3. 生成性能测试点
4. 生成安全测试点
5. 生成并发和压力测试点

### 步骤 6: 测试点验证
1. 检查测试点命名规范
2. 验证覆盖率要求
3. 检查测试点独立性
4. 验证断言完整性

### 步骤 7: 测试点执行和报告
1. 执行生成的测试点
2. 收集测试结果
3. 生成测试报告
4. 分析覆盖率报告

示例工作流脚本：
```bash
#!/bin/bash
# test-case-generation-workflow.sh

echo "步骤 1: 代码分析"
analyze_code() {
  # 代码分析逻辑
}

echo "步骤 2: 测试策略选择"
select_strategy() {
  # 策略选择逻辑
}

echo "步骤 3: M-单功能测试点生成"
generate_monolithic_tests() {
  # M测试点生成逻辑
}

echo "步骤 4: F-组合交叉测试点生成"
generate_combination_tests() {
  # F测试点生成逻辑
}

echo "步骤 5: Q-非功能测试点生成"
generate_quality_tests() {
  # Q测试点生成逻辑
}

echo "步骤 6: 测试点验证"
validate_test_points() {
  # 验证逻辑
}

echo "步骤 7: 测试点执行和报告"
execute_and_report() {
  # 执行和报告逻辑
}
```


## [策略 6] 测试点生成工具集成 [DISABLED]
# 测试点生成工具的集成和使用

STATUS: DISABLED
说明：
- 集成测试点生成工具（如 PICT、AllPairs 等）
- 支持自动化测试点生成
- 提供工具使用指南

推荐工具：
1. **PICT** (Pairwise Independent Combinatorial Testing)
   - 用途：配对测试点生成
   - 安装：`brew install pict` 或下载 Windows 版本
   - 使用：`pict model.txt > test_cases.txt`

2. **AllPairs**
   - 用途：配对测试点生成
   - 语言：Python
   - 安装：`pip install allpairs`

3. **CTWedge**
   - 用途：组合测试点生成
   - 平台：Web/CLI
   - 网址：https://ctwedge.fbk.eu/

4. **Jenny**
   - 用途：组合测试点生成
   - 语言：Java
   - GitHub：https://github.com/burtcorp/jenny

工具集成示例：
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


# ============================================
# 项目类型配置
# ============================================

Web 应用：
- 启用： [策略 1, 2, 3, 4, 5]
- M测试点：每个功能 3-5 个测试点
- F测试点：使用配对测试，减少 50-70% 测试用例
- Q测试点：性能（响应时间 < 200ms）、安全（OWASP Top 10）
- 覆盖率目标：80% 行覆盖率

CLI 工具：
- 启用： [策略 1, 2, 4, 5]
- M测试点：每个功能 3-5 个测试点
- F测试点：使用正交表，减少 60-80% 测试用例
- Q测试点：性能（执行时间）、并发（多进程）
- 覆盖率目标：85% 行覆盖率

库/SDK：
- 启用： [策略 1, 2, 3, 4, 5]
- M测试点：每个公共 API 5-8 个测试点
- F测试点：使用配对测试，覆盖所有参数组合
- Q测试点：性能（API 响应时间）、兼容性（多版本）
- 覆盖率目标：90% 行覆盖率，公共 API 100%


# ============================================
# 与其他规范的集成
# ============================================

DEPENDENCIES:
  test-case-generation-strategy.zh-CN.md::策略 1 -> testing-spec.zh-CN.md::规则 1
    note: M测试点生成遵循测试完整性要求
  test-case-generation-strategy.zh-CN.md::策略 2 -> testing-spec.zh-CN.md::规则 3
    note: F测试点生成遵循测试分层策略
  test-case-generation-strategy.zh-CN.md::策略 3 -> testing-spec.zh-CN.md::规则 7
    note: Q测试点生成包含边界条件和异常测试
  test-case-generation-strategy.zh-CN.md::策略 4 -> requirements-spec.zh-CN.md::规则 1
    note: 检查策略确保测试点完整可运行


# ============================================
# 摘要 - 启用的策略
# ============================================

✅ [策略 1]  M-单功能测试点生成 - 针对单个功能的独立测试点
✅ [策略 2]  F-组合交叉测试点生成 - 多个功能组合和参数交叉测试
✅ [策略 3]  Q-非功能测试点生成 - 性能、安全、并发等非功能测试
✅ [策略 4]  测试点生成检查策略配置 - 检查规则和验证标准
✅ [策略 5]  测试点生成工作流 - 完整的生成流程


# ============================================
# 版本历史
# ============================================
# v1.0 (2025-01-XX) - 初始测试点生成策略规范，包含 6 条策略
# ============================================
