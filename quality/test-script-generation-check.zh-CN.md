---
trigger: manual
---

# 测试脚本生成检查策略规范 v1.0
# ============================================
# AI 辅助开发的测试脚本生成检查标准和要求
# 通过将 [ENABLED] 更改为 [DISABLED] 来启用/禁用规则
#
# 使用方法：
# 1. 将此文件放在项目根目录或 quality 目录
# 2. 根据项目需求启用/禁用规则
# 3. 在 AI 对话中使用 @test-script-generation-check.zh-CN.md 引用
# 4. AI 将只遵循 ENABLED 的规则
#
# 依赖规范：testing-spec.zh-CN.md、test-case-generation-strategy.zh-CN.md
# 最后更新：2025-01-XX
# ============================================

## [检查 1] 测试脚本结构完整性 [ENABLED]
# 确保测试脚本包含必要的结构和元素

STATUS: ENABLED
说明：
- 测试脚本必须包含完整的测试框架结构
- 必须包含测试套件（describe）和测试用例（it/test）
- 必须包含必要的导入和依赖
- 必须包含测试数据和Mock设置

检查项：
- ✅ 测试文件命名符合规范（*.test.js, *.spec.js, *.test.ts等）
- ✅ 包含测试框架导入（jest, mocha, vitest等）
- ✅ 包含被测试模块的导入
- ✅ 包含测试套件（describe块）
- ✅ 包含测试用例（it/test块）
- ✅ 包含必要的测试工具导入（expect, assert等）

示例：
```typescript
// ✅ 正确：完整的测试脚本结构
import { describe, it, expect, beforeEach, afterEach } from '@jest/globals';
import { UserService } from '../src/UserService';
import { mockUserRepository } from './mocks/userRepository';

describe('UserService', () => {
  beforeEach(() => {
    // 测试前准备
  });

  afterEach(() => {
    // 测试后清理
  });

  describe('createUser', () => {
    it('should create user successfully', () => {
      // 测试逻辑
    });
  });
});
```

```typescript
// ❌ 错误：缺少必要的结构
// 缺少导入
describe('UserService', () => {
  it('should work', () => {
    // 缺少被测试模块导入
  });
});
```


## [检查 2] 测试脚本代码质量 [ENABLED]
# 确保测试脚本代码符合质量标准

STATUS: ENABLED
说明：
- 测试脚本必须可编译/运行，无语法错误
- 测试脚本必须使用正确的测试框架API
- 测试脚本必须避免反模式
- 测试脚本必须遵循最佳实践

检查项：
- ✅ 无语法错误，可正常编译/运行
- ✅ 使用正确的测试框架API（不使用已废弃的API）
- ✅ 避免硬编码的测试数据（使用工厂函数或Fixture）
- ✅ 避免测试逻辑过于复杂（单个测试用例不超过20行）
- ✅ 避免测试用例之间的依赖
- ✅ 使用有意义的变量名和函数名

示例：
```typescript
// ✅ 正确：使用工厂函数生成测试数据
function createTestUser(overrides = {}) {
  return {
    id: 'test-user-1',
    email: 'test@example.com',
    name: 'Test User',
    ...overrides
  };
}

it('should create user', () => {
  const userData = createTestUser({ email: 'custom@example.com' });
  const result = userService.createUser(userData);
  expect(result.email).toBe('custom@example.com');
});
```

```typescript
// ❌ 错误：硬编码测试数据，测试逻辑复杂
it('should create user', () => {
  const userData = { id: '1', email: 'a@b.c', name: 'User' };
  const result = userService.createUser(userData);
  if (result) {
    if (result.email === 'a@b.c') {
      expect(result.id).toBe('1');
      if (result.name === 'User') {
        expect(result.status).toBe('active');
      }
    }
  }
});
```


## [检查 3] 测试脚本命名规范 [ENABLED]
# 确保测试脚本和测试用例命名符合规范

STATUS: ENABLED
说明：
- 测试文件命名必须与被测试文件对应
- 测试套件命名必须清晰描述被测试单元
- 测试用例命名必须描述预期行为
- 命名必须使用业务语言，避免技术黑话

检查项：
- ✅ 测试文件：`{被测试文件名}.test.{ext}` 或 `{被测试文件名}.spec.{ext}`
- ✅ 测试套件：`describe('被测试单元名称')`
- ✅ 测试用例：`it('should + 预期行为')` 或 `test('does something')`
- ✅ 测试用例描述长度：10-100字符
- ✅ 测试用例描述使用现在时态
- ✅ 测试用例描述包含测试类型标识（M/F/Q）

示例：
```typescript
// ✅ 正确：符合命名规范
// 文件：UserService.test.ts
describe('UserService', () => {
  describe('createUser', () => {
    it('M-001: should create user with valid email', () => {
      // ...
    });
    
    it('M-002: should throw ValidationError when email is invalid', () => {
      // ...
    });
    
    it('F-001: should handle combination of email and password validation', () => {
      // ...
    });
    
    it('Q-001: should create user within 100ms', async () => {
      // ...
    });
  });
});
```

```typescript
// ❌ 错误：命名不规范
// 文件：test.ts
describe('tests', () => {
  it('test1', () => {
    // ...
  });
  
  it('works', () => {
    // ...
  });
});
```


## [检查 4] 测试脚本依赖和导入检查 [ENABLED]
# 确保测试脚本正确导入依赖

STATUS: ENABLED
说明：
- 测试脚本必须正确导入被测试模块
- 测试脚本必须正确导入测试框架
- 测试脚本必须正确导入Mock和工具函数
- 测试脚本必须避免导入不存在的模块

检查项：
- ✅ 被测试模块导入路径正确
- ✅ 测试框架导入正确（jest, mocha, vitest等）
- ✅ Mock和工具函数导入正确
- ✅ 所有导入的模块都存在
- ✅ 避免循环依赖
- ✅ 使用相对路径或配置的路径别名

示例：
```typescript
// ✅ 正确：导入路径正确，模块存在
import { UserService } from '../src/UserService';
import { mockUserRepository } from './mocks/userRepository';
import { createTestUser } from './helpers/userFactory';
```

```typescript
// ❌ 错误：导入不存在的模块
import { UserService } from '../src/UserService';  // 路径错误
import { magicHelper } from './non-existent';     // 模块不存在
```


## [检查 5] 测试脚本断言完整性 [ENABLED]
# 确保测试脚本包含完整的断言

STATUS: ENABLED
说明：
- 每个测试用例必须包含至少一个断言
- 断言必须验证预期的行为或结果
- 断言必须使用正确的断言方法
- 断言必须清晰表达预期结果

检查项：
- ✅ 每个测试用例至少包含一个断言
- ✅ 断言验证正确的值或行为
- ✅ 使用合适的断言方法（toBe, toEqual, toThrow等）
- ✅ 断言消息清晰（如需要）
- ✅ 避免无意义的断言（如 expect(true).toBe(true)）

示例：
```typescript
// ✅ 正确：包含完整的断言
it('M-001: should return discounted price', () => {
  const result = calculateDiscount(100, 0.1);
  expect(result).toBe(90);
  expect(typeof result).toBe('number');
});

it('M-002: should throw error for invalid input', () => {
  expect(() => calculateDiscount(-100, 0.1)).toThrow('Invalid input');
});
```

```typescript
// ❌ 错误：缺少断言或断言无意义
it('should calculate discount', () => {
  const result = calculateDiscount(100, 0.1);
  // 缺少断言
});

it('should work', () => {
  expect(true).toBe(true);  // 无意义的断言
});
```


## [检查 6] 测试脚本Mock和Stub使用 [ENABLED]
# 确保测试脚本正确使用Mock和Stub

STATUS: ENABLED
说明：
- 外部依赖必须使用Mock或Stub
- Mock设置必须正确
- Mock验证必须完整
- 避免Mock被测试的核心逻辑

检查项：
- ✅ 外部依赖（API、数据库、文件系统）已Mock
- ✅ Mock返回值设置正确
- ✅ Mock调用验证完整（如需要）
- ✅ Mock在测试后正确清理
- ✅ 避免Mock被测试的核心逻辑
- ✅ 使用真实的Mock数据结构

示例：
```typescript
// ✅ 正确：正确使用Mock
import { jest } from '@jest/globals';

describe('UserService', () => {
  let mockFetch: jest.Mock;

  beforeEach(() => {
    mockFetch = jest.fn().mockResolvedValue({
      json: () => Promise.resolve({ id: 1, name: 'Test User' })
    });
    global.fetch = mockFetch;
  });

  afterEach(() => {
    jest.clearAllMocks();
  });

  it('M-001: should fetch user from API', async () => {
    const user = await userService.getUser(1);
    expect(user.id).toBe(1);
    expect(mockFetch).toHaveBeenCalledWith('/api/users/1');
  });
});
```

```typescript
// ❌ 错误：Mock使用不当
it('should calculate discount', () => {
  // Mock了被测试的核心逻辑
  const mockCalculate = jest.fn().mockReturnValue(90);
  const result = mockCalculate(100, 0.1);
  expect(result).toBe(90);
});
```


## [检查 7] 测试脚本隔离性和清理 [ENABLED]
# 确保测试脚本测试之间相互隔离

STATUS: ENABLED
说明：
- 每个测试用例必须独立运行
- 测试之间不能共享状态
- 测试后必须清理资源
- 测试执行顺序不应影响结果

检查项：
- ✅ 使用 beforeEach/afterEach 清理状态
- ✅ 每个测试用例使用独立的测试数据
- ✅ 避免修改全局变量或单例
- ✅ 测试后清理Mock和Spy
- ✅ 测试后清理数据库或文件（如适用）
- ✅ 测试可以并行运行

示例：
```typescript
// ✅ 正确：测试隔离和清理
describe('UserService', () => {
  let userService: UserService;
  let mockRepository: jest.Mock;

  beforeEach(() => {
    mockRepository = jest.fn();
    userService = new UserService(mockRepository);
  });

  afterEach(() => {
    jest.clearAllMocks();
    // 清理其他资源
  });

  it('M-001: should create user', () => {
    const userData = createTestUser();
    userService.createUser(userData);
    expect(mockRepository.save).toHaveBeenCalledWith(userData);
  });

  it('M-002: should delete user', () => {
    const userId = 'test-id';
    userService.deleteUser(userId);
    expect(mockRepository.delete).toHaveBeenCalledWith(userId);
  });
});
```

```typescript
// ❌ 错误：测试之间共享状态
let sharedUser = { id: 1 };

it('should update user', () => {
  sharedUser.name = 'Updated';
  // ...
});

it('should delete user', () => {
  // 依赖上一个测试的状态
  userService.deleteUser(sharedUser.id);
});
```


## [检查 8] 测试脚本异步处理 [ENABLED]
# 确保测试脚本正确处理异步操作

STATUS: ENABLED
说明：
- 异步测试必须正确使用async/await或Promise
- 异步测试必须正确等待结果
- 异步测试必须正确处理错误
- 避免异步测试中的竞态条件

检查项：
- ✅ 异步测试使用async/await
- ✅ 异步操作正确等待完成
- ✅ 异步错误正确处理
- ✅ 避免未等待的Promise
- ✅ 超时设置合理（如需要）

示例：
```typescript
// ✅ 正确：正确处理异步
it('M-001: should fetch user asynchronously', async () => {
  const user = await userService.getUser(1);
  expect(user).toBeDefined();
});

it('M-002: should handle async errors', async () => {
  mockFetch.mockRejectedValue(new Error('Network error'));
  await expect(userService.getUser(1)).rejects.toThrow('Network error');
});
```

```typescript
// ❌ 错误：未正确处理异步
it('should fetch user', () => {
  const user = userService.getUser(1);  // 未等待Promise
  expect(user).toBeDefined();  // 可能失败
});
```


## [检查 9] 测试脚本性能要求 [ENABLED]
# 确保测试脚本执行性能符合要求

STATUS: ENABLED
说明：
- 单元测试必须快速执行（< 100ms）
- 集成测试执行时间合理（< 1s）
- 避免不必要的等待和延迟
- 使用Mock减少实际I/O操作

检查项：
- ✅ 单元测试执行时间 < 100ms
- ✅ 集成测试执行时间 < 1s
- ✅ 避免不必要的sleep或delay
- ✅ 使用Mock减少网络和数据库操作
- ✅ 批量测试执行时间 < 5分钟

示例：
```typescript
// ✅ 正确：快速执行的测试
it('M-001: should calculate discount quickly', () => {
  const start = Date.now();
  const result = calculateDiscount(100, 0.1);
  const duration = Date.now() - start;
  expect(result).toBe(90);
  expect(duration).toBeLessThan(10);  // 应该非常快
});
```

```typescript
// ❌ 错误：不必要的延迟
it('should fetch user', async () => {
  await new Promise(resolve => setTimeout(resolve, 1000));  // 不必要的延迟
  const user = await userService.getUser(1);
  expect(user).toBeDefined();
});
```


## [检查 10] 测试脚本类型安全 [ENABLED]
# 确保TypeScript测试脚本类型安全

STATUS: ENABLED
LANGUAGE: TypeScript
说明：
- TypeScript测试脚本必须通过类型检查
- 测试数据必须符合类型定义
- 避免使用any类型
- 使用类型断言时确保安全

检查项：
- ✅ 测试脚本通过TypeScript编译
- ✅ 测试数据符合类型定义
- ✅ 避免使用any类型（除非必要）
- ✅ 类型断言安全（使用类型守卫）
- ✅ Mock类型定义正确

示例：
```typescript
// ✅ 正确：类型安全的测试
interface User {
  id: string;
  email: string;
  name: string;
}

function createTestUser(overrides: Partial<User> = {}): User {
  return {
    id: 'test-id',
    email: 'test@example.com',
    name: 'Test User',
    ...overrides
  };
}

it('M-001: should create user with correct type', () => {
  const userData: User = createTestUser({ email: 'custom@example.com' });
  const result = userService.createUser(userData);
  expect(result).toMatchObject<User>(userData);
});
```

```typescript
// ❌ 错误：类型不安全
it('should create user', () => {
  const userData: any = { id: 1, email: 'test' };  // 使用any
  const result = userService.createUser(userData);  // 类型不匹配
  expect(result).toBeDefined();
});
```


## [检查 11] 测试脚本错误处理 [ENABLED]
# 确保测试脚本正确处理错误情况

STATUS: ENABLED
说明：
- 测试脚本必须测试错误处理路径
- 错误断言必须正确
- 必须测试异常情况
- 错误消息验证（如需要）

检查项：
- ✅ 测试错误处理路径
- ✅ 使用正确的错误断言（toThrow, rejects等）
- ✅ 验证错误类型和消息
- ✅ 测试边界条件和异常输入

示例：
```typescript
// ✅ 正确：完整的错误处理测试
it('M-001: should throw ValidationError for invalid email', () => {
  expect(() => {
    userService.createUser({ email: 'invalid-email' });
  }).toThrow(ValidationError);
});

it('M-002: should throw specific error message', () => {
  expect(() => {
    userService.createUser({ email: 'invalid' });
  }).toThrow('Email format is invalid');
});

it('M-003: should handle async errors', async () => {
  mockFetch.mockRejectedValue(new Error('Network error'));
  await expect(userService.getUser(1)).rejects.toThrow('Network error');
});
```

```typescript
// ❌ 错误：缺少错误处理测试
it('should create user', () => {
  const result = userService.createUser({ email: 'test@example.com' });
  expect(result).toBeDefined();
  // 缺少错误情况的测试
});
```


## [检查 12] 测试脚本可维护性 [ENABLED]
# 确保测试脚本易于维护和理解

STATUS: ENABLED
说明：
- 测试脚本必须易于理解和维护
- 测试逻辑必须清晰
- 必须使用辅助函数减少重复
- 必须包含必要的注释

检查项：
- ✅ 测试逻辑清晰，易于理解
- ✅ 使用辅助函数减少代码重复
- ✅ 包含必要的注释（复杂逻辑）
- ✅ 测试数据使用工厂函数
- ✅ 避免魔法数字和字符串

示例：
```typescript
// ✅ 正确：可维护的测试脚本
// 辅助函数
function createTestUser(overrides = {}) {
  return {
    id: 'test-user-1',
    email: 'test@example.com',
    name: 'Test User',
    ...overrides
  };
}

// 常量定义
const VALID_EMAIL = 'test@example.com';
const INVALID_EMAIL = 'invalid-email';

describe('UserService', () => {
  it('M-001: should create user with valid email', () => {
    const userData = createTestUser({ email: VALID_EMAIL });
    const result = userService.createUser(userData);
    expect(result.email).toBe(VALID_EMAIL);
  });

  it('M-002: should reject invalid email', () => {
    const userData = createTestUser({ email: INVALID_EMAIL });
    expect(() => userService.createUser(userData)).toThrow();
  });
});
```

```typescript
// ❌ 错误：难以维护的测试脚本
it('test1', () => {
  const u = { id: '1', email: 'a@b.c', name: 'u' };  // 魔法字符串
  const r = s.createUser(u);
  expect(r.email).toBe('a@b.c');
  // 重复的代码...
  const u2 = { id: '2', email: 'x@y.z', name: 'u2' };
  const r2 = s.createUser(u2);
  expect(r2.email).toBe('x@y.z');
});
```


## [检查 13] 测试脚本配置检查 [ENABLED]
# 确保测试脚本配置正确

STATUS: ENABLED
说明：
- 测试脚本必须使用正确的测试框架配置
- 测试环境配置必须正确
- 测试工具配置必须正确
- 路径别名配置必须正确（如使用）

检查项：
- ✅ 测试框架配置正确（jest.config.js, vitest.config.ts等）
- ✅ 测试环境变量设置正确
- ✅ 路径别名配置正确（@/, ~/等）
- ✅ 覆盖率配置正确
- ✅ Mock配置正确

配置示例：
```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'node',
  roots: ['<rootDir>/src', '<rootDir>/tests'],
  testMatch: ['**/__tests__/**/*.ts', '**/?(*.)+(spec|test).ts'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  coverageThreshold: {
    global: {
      branches: 75,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};
```


## [检查 14] 测试脚本与测试点对应关系 [ENABLED]
# 确保测试脚本与测试点生成策略对应

STATUS: ENABLED
说明：
- 测试脚本必须包含M/F/Q测试点
- 测试点命名必须符合规范
- 测试点类型必须正确标识
- 测试点数量必须符合要求

检查项：
- ✅ 测试脚本包含M测试点（单功能测试）
- ✅ 测试脚本包含F测试点（组合测试，如适用）
- ✅ 测试脚本包含Q测试点（非功能测试，如适用）
- ✅ 测试点命名格式正确（M-001, F-001, Q-001）
- ✅ 测试点数量符合配置要求

示例：
```typescript
describe('UserService', () => {
  describe('createUser', () => {
    // M-单功能测试点
    it('M-001: should create user with valid email', () => {});
    it('M-002: should throw error for invalid email', () => {});
    it('M-003: should handle empty name', () => {});
    
    // F-组合测试点
    it('F-001: should handle combination of email and password validation', () => {});
    
    // Q-非功能测试点
    it('Q-001: should create user within 100ms', async () => {});
  });
});
```


# ============================================
# 检查策略配置
# ============================================

## 检查策略配置文件

检查策略可以通过配置文件进行设置：

```yaml
# .test-script-check.yaml
test_script_generation:
  # 结构完整性检查
  structure:
    enabled: true
    require_imports: true
    require_describe: true
    require_test_cases: true
  
  # 代码质量检查
  code_quality:
    enabled: true
    max_test_case_lines: 20
    require_factory_functions: true
    forbid_hardcoded_data: true
  
  # 命名规范检查
  naming:
    enabled: true
    file_pattern: "{name}.test.{ext}"
    describe_pattern: "{unit_name}"
    test_case_pattern: "{type}-{seq:03d}: should {behavior}"
    min_description_length: 10
    max_description_length: 100
  
  # 断言检查
  assertions:
    enabled: true
    min_assertions_per_test: 1
    require_meaningful_assertions: true
  
  # Mock检查
  mocks:
    enabled: true
    require_external_mocks: true
    require_mock_cleanup: true
    forbid_core_logic_mocks: true
  
  # 隔离性检查
  isolation:
    enabled: true
    require_before_each: false
    require_after_each: true
    require_independent_data: true
  
  # 异步处理检查
  async:
    enabled: true
    require_async_await: true
    forbid_unhandled_promises: true
  
  # 性能检查
  performance:
    enabled: true
    max_unit_test_ms: 100
    max_integration_test_ms: 1000
    forbid_unnecessary_delays: true
  
  # 类型安全检查（TypeScript）
  type_safety:
    enabled: true
    require_type_check: true
    forbid_any_type: true
  
  # 错误处理检查
  error_handling:
    enabled: true
    require_error_tests: true
    require_error_assertions: true
  
  # 可维护性检查
  maintainability:
    enabled: true
    require_helper_functions: true
    require_constants: true
    max_code_duplication: 30  # 百分比
  
  # 测试点对应关系检查
  test_point_mapping:
    enabled: true
    require_m_tests: true
    require_f_tests: false  # 可选
    require_q_tests: false  # 可选
    test_point_naming_pattern: "{type}-{seq:03d}: {description}"
```

## 检查策略验证规则

### 严格模式（Strict Mode）
```yaml
strict_mode: true
# 所有检查项必须通过，否则测试脚本生成失败
```

### 宽松模式（Lenient Mode）
```yaml
strict_mode: false
# 检查项失败时发出警告，但不阻止测试脚本生成
warn_threshold: 3  # 最多允许3个警告
```

### 检查项优先级
1. **关键检查项**（必须通过）：
   - 结构完整性
   - 代码质量（语法错误）
   - 断言完整性
   - 测试点对应关系

2. **重要检查项**（建议通过）：
   - 命名规范
   - Mock使用
   - 隔离性
   - 错误处理

3. **一般检查项**（可选）：
   - 性能要求
   - 可维护性
   - 类型安全（TypeScript项目）


# ============================================
# 项目类型配置
# ============================================

Web 应用：
- 启用： [检查 1-14]
- 关键检查：结构完整性、断言完整性、Mock使用
- 测试点要求：M测试点必须，F/Q测试点可选
- 性能要求：单元测试 < 100ms，集成测试 < 1s

CLI 工具：
- 启用： [检查 1-13]（类型安全可选）
- 关键检查：结构完整性、错误处理、隔离性
- 测试点要求：M测试点必须，F测试点可选
- 性能要求：单元测试 < 100ms

库/SDK：
- 启用： [检查 1-14]
- 关键检查：结构完整性、类型安全、错误处理、测试点对应关系
- 测试点要求：M/F/Q测试点都建议包含
- 性能要求：单元测试 < 50ms，集成测试 < 500ms


# ============================================
# 与其他规范的集成
# ============================================

DEPENDENCIES:
  test-script-generation-check.zh-CN.md::检查 1 -> testing-spec.zh-CN.md::规则 1
    note: 测试脚本结构完整性遵循测试完整性要求
  test-script-generation-check.zh-CN.md::检查 3 -> testing-spec.zh-CN.md::规则 5
    note: 测试脚本命名规范遵循测试命名约定
  test-script-generation-check.zh-CN.md::检查 6 -> testing-spec.zh-CN.md::规则 4
    note: Mock使用遵循Mock和Stub使用规范
  test-script-generation-check.zh-CN.md::检查 14 -> test-case-generation-strategy.zh-CN.md::策略 1-3
    note: 测试脚本必须包含M/F/Q测试点


# ============================================
# 摘要 - 启用的检查项
# ============================================

✅ [检查 1]  测试脚本结构完整性 - 确保包含必要的结构和元素
✅ [检查 2]  测试脚本代码质量 - 确保代码符合质量标准
✅ [检查 3]  测试脚本命名规范 - 确保命名符合规范
✅ [检查 4]  测试脚本依赖和导入检查 - 确保正确导入依赖
✅ [检查 5]  测试脚本断言完整性 - 确保包含完整的断言
✅ [检查 6]  测试脚本Mock和Stub使用 - 确保正确使用Mock
✅ [检查 7]  测试脚本隔离性和清理 - 确保测试之间相互隔离
✅ [检查 8]  测试脚本异步处理 - 确保正确处理异步操作
✅ [检查 9]  测试脚本性能要求 - 确保执行性能符合要求
✅ [检查 10] 测试脚本类型安全 - 确保TypeScript类型安全
✅ [检查 11] 测试脚本错误处理 - 确保正确处理错误情况
✅ [检查 12] 测试脚本可维护性 - 确保易于维护和理解
✅ [检查 13] 测试脚本配置检查 - 确保配置正确
✅ [检查 14] 测试脚本与测试点对应关系 - 确保包含M/F/Q测试点


# ============================================
# 版本历史
# ============================================
# v1.0 (2025-01-XX) - 初始测试脚本生成检查策略规范，包含 14 个检查项
# ============================================
