---
trigger: manual
---

# 文本测试用例转RobotFramework自动化脚本规范 v1.0
# ============================================
# 基于文本测试用例生成RobotFramework自动化测试脚本的规则和要求
# 严格遵循RobotFramework语法规范，生成标准化的自动化测试脚本
#
# 重要说明：
# - 本规范用于从文本测试用例生成RobotFramework自动化测试脚本
# - 文本测试用例：可执行的测试步骤，包含详细的执行信息和结果记录区域
# - RobotFramework脚本：可执行的自动化测试脚本，遵循RobotFramework语法规范
#
# 使用方法：
# 1. 将此文件放在项目根目录
# 2. 在 AI 对话中使用 @text-test-case-to-robot-spec.zh-CN.md 引用
# 3. AI 将自动遵循这些规则从文本测试用例生成RobotFramework脚本
#
# 输入来源：
# - 文本测试用例.txt 或 文本测试用例.md
# - 或按类型拆分的文件：文本测试用例-单功能.txt、文本测试用例-功能交互.txt、文本测试用例-质量属性.txt
#
# 输出格式：RobotFramework测试脚本（.robot）
# 最后更新：2025-01-XX
# ============================================

## [规则 0] 输出目录管理（优先级：关键）
# 用户必须明确指定输出物的存放路径

说明：
- 当本规范作为规则被@使用时，必须要求用户明确指定输出目录
- 用户指定的目录将作为所有生成物的根目录
- 后续所有生成物均以该目录为根目录存放
- 如果用户未指定，提示用户："请指定输出目录，格式：输出目录=/path/to/output"

工作流程：
1. 检查用户是否指定了输出目录
2. 如果未指定，提示用户："请指定输出目录，格式：输出目录=/path/to/output"
3. 如果指定了，验证目录是否存在
4. 如果目录不存在，创建目录
5. 将输出目录路径保存为全局变量，供后续所有规则使用

输出目录格式：
- 绝对路径：`/path/to/output`
- 相对路径：`./output` 或 `output`
- 默认路径：如果用户未指定，使用当前目录 `.`

示例：
✅ 正确：
  - 用户输入："输出目录=/workspace/test-output"
  - 所有文件生成到：/workspace/test-output/文本测试用例-单功能.robot

❌ 错误：
  - 未要求用户指定输出目录
  - 使用硬编码的目录路径
  - 不同规则使用不同的目录


## [规则 1] 从文本测试用例文件读取测试用例（优先级：关键）
# 必须从文本测试用例文件中读取测试用例信息

说明：
- 读取 `{输出目录}/文本测试用例.txt` 或 `{输出目录}/文本测试用例.md` 文件（如果用户指定了输出目录）
- 或读取按类型拆分的文件：
  - `{输出目录}/文本测试用例-单功能.txt`
  - `{输出目录}/文本测试用例-功能交互.txt`
  - `{输出目录}/文本测试用例-质量属性.txt`
- 或读取当前目录下的对应文件（如果用户未指定输出目录）
- 解析文件中的所有测试用例（格式：`测试用例编号：MFQ-M-XXX`、`测试用例编号：MFQ-F-XXX`、`测试用例编号：MFQ-Q-XXX`）
- 提取每个测试用例的完整信息（编号、标题、前置条件、测试步骤、预期结果等）
- 如果文件不存在，提示用户先执行文本测试用例生成流程

工作流程：
1. 确定输入文件路径：
   - 如果用户指定了输出目录，优先读取 `{输出目录}/文本测试用例.txt` 或 `{输出目录}/文本测试用例.md`
   - 如果不存在，尝试读取按类型拆分的文件：
     - `{输出目录}/文本测试用例-单功能.txt`
     - `{输出目录}/文本测试用例-功能交互.txt`
     - `{输出目录}/文本测试用例-质量属性.txt`
   - 如果用户未指定，读取当前目录下的对应文件
2. 检查文本测试用例文件是否存在
3. 如果不存在，提示："文本测试用例文件不存在，请先执行文本测试用例生成流程"
4. 如果存在，解析文件中的所有测试用例
5. 提取每个测试用例的完整信息：
   - 测试用例编号（如：MFQ-M-001）
   - 测试用例标题
   - 测试类型（单功能测试/功能交互测试/质量属性测试）
   - 前置条件
   - 测试步骤（步骤1、步骤2...）
   - 预期结果
   - 验收准则（Given/When/Then）
   - 其他相关信息（测试对象、功能交互、质量属性等）

后果：
- 未从正确来源读取会导致RobotFramework脚本与文本测试用例不匹配
- 遗漏测试用例会导致测试覆盖率不足
- 解析错误会导致生成的脚本无法正确执行

示例：
✅ 正确：
  - 读取：文本测试用例.txt
  - 解析：测试用例编号：MFQ-M-001、MFQ-M-002...
  - 提取：每个测试用例的完整信息（前置条件、测试步骤、预期结果等）

❌ 错误：
  - 从其他文件读取测试用例信息
  - 手动输入测试用例信息
  - 遗漏部分测试用例
  - 解析不完整（遗漏前置条件或测试步骤）


## [规则 2] RobotFramework脚本格式标准（优先级：关键）
# 严格按照RobotFramework语法规范生成脚本

说明：
- 严格遵循RobotFramework语法规范
- 确保脚本可执行且结构清晰
- 保持文本测试用例的所有关键信息
- 脚本应该具有良好的可读性和可维护性

RobotFramework脚本标准结构：

### 2.1 文件结构规范

#### 2.1.1 Settings部分（必需）

```robot
*** Settings ***
Documentation      测试套件描述（从文本测试用例类型提取）
Metadata            Suite    文本测试用例转RobotFramework自动化脚本
Metadata            Source    文本测试用例文件路径
Library            库名    # 根据测试类型导入相应的库
Resource           ../resources/common.robot    # 公共资源文件（如果存在）
Suite Setup        套件初始化关键字
Suite Teardown     套件清理关键字
Test Setup         测试用例初始化关键字
Test Teardown      测试用例清理关键字
Test Timeout        超时时间（默认：30秒）
Default Tags       默认标签（Auto、Text2Robot）
Force Tags         强制标签（根据测试类型：SingleFunction、FunctionInteraction、Quality）
```

必需导入的库（根据测试类型）：
- **通用库**：
  - `Collections`：用于字典和列表操作
  - `String`：用于字符串操作
  - `BuiltIn`：RobotFramework内置库（默认导入）
- **Web UI测试**（如果测试步骤涉及Web操作）：
  - `SeleniumLibrary`：用于Web自动化测试
- **API测试**（如果测试步骤涉及API调用）：
  - `RequestsLibrary`：用于HTTP请求
- **数据库测试**（如果测试步骤涉及数据库操作）：
  - `DatabaseLibrary`：用于数据库操作
- **文件操作**（如果测试步骤涉及文件操作）：
  - `OperatingSystem`：用于文件系统操作

#### 2.1.2 Variables部分（可选但推荐）

```robot
*** Variables ***
${SCALAR}          标量值
@{LIST}            列表值1    列表值2    列表值3
&{DICT}            key1=value1    key2=value2
```

常用变量：
- `${BASE_URL}`：基础URL（Web或API测试）
- `&{DEFAULT_HEADERS}`：默认请求头（API测试）
- `${TIMEOUT}`：默认超时时间
- `${RETRY_COUNT}`：重试次数

#### 2.1.3 Test Cases部分（必需）

```robot
*** Test Cases ***
测试用例名称
    [Documentation]
    ...    【用例编号】
    ...    {测试用例编号}
    ...    【用例名称】
    ...    {测试用例标题}
    ...    【用例等级】
    ...    {从文本测试用例提取或默认}
    ...    【测试类型】
    ...    {单功能测试/功能交互测试/质量属性测试}
    ...    【预置条件】
    ...    {前置条件1}
    ...    {前置条件2}
    ...    【测试步骤】
    ...    {步骤1描述}
    ...    {步骤2描述}
    ...    【预期结果】
    ...    {预期结果1}
    ...    {预期结果2}
    ...    【验收准则】
    ...    Given: {Given内容}
    ...    When: {When内容}
    ...    Then: {Then内容}
    ...    【作者】
    ...    Auto Generated
    [Tags]    Auto    {测试类型标签}    {其他标签}
    [Setup]           初始化关键字（如果需要）
    [Teardown]         清理关键字（如果需要）
    [Timeout]          超时时间（如果需要）
    # 测试步骤实现
    ${variable}=    关键字    参数1    参数2
    Should Be Equal    ${actual}    ${expected}    错误消息
```

测试用例命名规范：
- 格式：`{测试用例编号}_{简化标题}`
- 示例：`MFQ-M-001_用户登录`、`MFQ-F-002_登录后查看个人信息`
- 如果标题过长，使用关键词或简化版本
- 避免特殊字符，使用下划线连接

#### 2.1.4 Keywords部分（可选但推荐）

```robot
*** Keywords ***
关键字名称
    [Documentation]    关键字描述
    [Arguments]        ${arg1}    ${arg2}=默认值
    [Return]          返回值
    [Teardown]        清理关键字
    [Timeout]         超时时间
    # 关键字步骤
    关键字    参数
    [Return]    值
```

### 2.2 语法细节规范

#### 2.2.1 缩进和空格

- ✅ **正确的缩进**: 使用4个空格缩进（RobotFramework标准）
- ❌ **错误的缩进**: 只有2个空格、没有缩进、使用Tab

#### 2.2.2 变量使用规范

```robot
*** Variables ***
# 标量变量
${USERNAME}        testuser
${PASSWORD}        secret123
${BASE_URL}        https://api.example.com

# 列表变量
@{VALID_USERS}    user1    user2    user3

# 字典变量
&{DEFAULT_HEADERS}    Content-Type=application/json    Accept=application/json
```

#### 2.2.3 控制流规范

```robot
*** Test Cases ***
条件判断示例
    ${status}=    Get Status
    IF    ${status} == "SUCCESS"
        Log    操作成功
    ELSE IF    ${status} == "FAILED"
        Log    操作失败
    ELSE
        Log    未知状态: ${status}
    END

循环示例
    FOR    ${item}    IN    @{LIST}
        关键字    ${item}
    END
```

### 2.3 测试用例生成模板

#### 2.3.1 单功能测试用例模板

```robot
*** Settings ***
Documentation      单功能自动化测试用例（从文本测试用例生成）
Metadata            Suite    单功能测试
Metadata            Source    文本测试用例-单功能.txt
Library            SeleniumLibrary
Library            Collections
Resource           ../resources/common.robot
Test Setup         打开浏览器
Test Teardown      关闭浏览器
Test Timeout        30秒
Default Tags        Auto    SingleFunction
Force Tags          TDMAuto

*** Variables ***
${BASE_URL}        https://example.com
${TIMEOUT}         10秒

*** Test Cases ***
MFQ-M-001_用户登录
    [Documentation]
    ...    【用例编号】
    ...    MFQ-M-001
    ...    【用例名称】
    ...    用户登录
    ...    【用例等级】
    ...    P1
    ...    【测试类型】
    ...    单功能测试
    ...    【测试对象】
    ...    用户登录功能模块
    ...    【预置条件】
    ...    1. 系统已启动
    ...    2. 用户账号已创建
    ...    【测试步骤】
    ...    步骤1：打开登录页面
    ...    步骤2：输入用户名和密码
    ...    步骤3：点击登录按钮
    ...    【预期结果】
    ...    1. 登录成功
    ...    2. 跳转到首页
    ...    【验收准则】
    ...    Given: 用户已注册
    ...    When: 用户输入正确的用户名和密码并点击登录
    ...    Then: 系统验证通过并跳转到首页
    ...    【作者】
    ...    Auto Generated
    [Tags]    Auto    SingleFunction    Login
    [Setup]    
    # 前置条件处理
    # 打开登录页面
    Go To    ${BASE_URL}/login
    # 输入用户名和密码
    Input Text    id=username    ${USERNAME}
    Input Text    id=password    ${PASSWORD}
    # 点击登录按钮
    Click Button    id=login-btn
    # 验证预期结果
    Wait Until Location Contains    /home    timeout=${TIMEOUT}
    Page Should Contain    欢迎
    [Teardown]    
```

#### 2.3.2 功能交互测试用例模板

```robot
*** Settings ***
Documentation      功能交互自动化测试用例（从文本测试用例生成）
Metadata            Suite    功能交互测试
Metadata            Source    文本测试用例-功能交互.txt
Library            SeleniumLibrary
Library            Collections
Resource           ../resources/common.robot
Test Setup         打开浏览器并登录
Test Teardown      关闭浏览器
Test Timeout        60秒
Default Tags        Auto    FunctionInteraction
Force Tags          TDMAuto

*** Test Cases ***
MFQ-F-001_登录后查看个人信息
    [Documentation]
    ...    【用例编号】
    ...    MFQ-F-001
    ...    【用例名称】
    ...    登录后查看个人信息
    ...    【用例等级】
    ...    P1
    ...    【测试类型】
    ...    功能交互测试
    ...    【功能交互】
    ...    用户登录功能与个人信息查看功能的交互
    ...    【交互类型】
    ...    顺序交互
    ...    【预置条件】
    ...    1. 用户已登录
    ...    【测试步骤】
    ...    步骤1：登录系统
    ...    步骤2：点击个人信息菜单
    ...    步骤3：验证个人信息显示
    ...    【预期结果】
    ...    1. 登录成功
    ...    2. 个人信息页面正确显示
    ...    【验收准则】
    ...    Given: 用户已登录
    ...    When: 用户点击个人信息菜单
    ...    Then: 系统显示用户的个人信息
    ...    【作者】
    ...    Auto Generated
    [Tags]    Auto    FunctionInteraction    Login    Profile
    [Setup]    
    # 步骤1：登录系统（已在Setup中完成）
    # 步骤2：点击个人信息菜单
    Click Element    id=profile-menu
    # 步骤3：验证个人信息显示
    Wait Until Page Contains    个人信息    timeout=${TIMEOUT}
    Page Should Contain Element    id=user-name
    Page Should Contain Element    id=user-email
    [Teardown]    
```

#### 2.3.3 质量属性测试用例模板

```robot
*** Settings ***
Documentation      质量属性自动化测试用例（从文本测试用例生成）
Metadata            Suite    质量属性测试
Metadata            Source    文本测试用例-质量属性.txt
Library            SeleniumLibrary
Library            Collections
Resource           ../resources/common.robot
Test Setup         打开浏览器
Test Teardown      关闭浏览器
Test Timeout        30秒
Default Tags        Auto    Quality
Force Tags          TDMAuto

*** Test Cases ***
MFQ-Q-001_登录性能测试
    [Documentation]
    ...    【用例编号】
    ...    MFQ-Q-001
    ...    【用例名称】
    ...    登录性能测试
    ...    【用例等级】
    ...    P2
    ...    【测试类型】
    ...    质量属性测试
    ...    【质量属性】
    ...    性能
    ...    【质量属性说明】
    ...    验证登录功能的响应时间是否符合性能要求
    ...    【测试维度】
    ...    响应时间
    ...    【预置条件】
    ...    1. 系统已启动
    ...    【测试步骤】
    ...    步骤1：记录开始时间
    ...    步骤2：执行登录操作
    ...    步骤3：记录结束时间
    ...    【预期结果】
    ...    1. 登录响应时间小于2秒
    ...    【质量验证标准】
    ...    响应时间应小于2秒
    ...    【验收准则】
    ...    Given: 系统正常运行
    ...    When: 用户执行登录操作
    ...    Then: 登录响应时间在可接受范围内
    ...    【作者】
    ...    Auto Generated
    [Tags]    Auto    Quality    Performance    Login
    [Setup]    
    # 记录开始时间
    ${start_time}=    Get Time    epoch
    # 执行登录操作
    Go To    ${BASE_URL}/login
    Input Text    id=username    ${USERNAME}
    Input Text    id=password    ${PASSWORD}
    Click Button    id=login-btn
    Wait Until Location Contains    /home    timeout=${TIMEOUT}
    # 记录结束时间
    ${end_time}=    Get Time    epoch
    # 计算响应时间
    ${response_time}=    Evaluate    ${end_time} - ${start_time}
    # 验证性能要求
    Should Be True    ${response_time} < 2    登录响应时间${response_time}秒超过2秒要求
    [Teardown]    
```


## [规则 3] 文本测试用例到RobotFramework脚本的映射规则（优先级：高）
# 将文本测试用例准确映射为RobotFramework脚本

说明：
- 从文本测试用例中提取信息，映射到RobotFramework脚本格式
- 保持信息的完整性和准确性
- 确保脚本可执行且符合RobotFramework语法规范
- 根据测试类型选择合适的库和关键字

映射规则：

### 3.1 测试用例基本信息映射

| 文本测试用例字段 | RobotFramework映射位置 | 说明 |
|----------------|---------------------|------|
| 测试用例编号 | Test Case名称 + Documentation中的【用例编号】 | 如：MFQ-M-001 → MFQ-M-001_用户登录 |
| 测试用例标题 | Test Case名称 + Documentation中的【用例名称】 | 简化标题用于Test Case名称 |
| 测试类型 | Documentation中的【测试类型】+ Tags + Force Tags | 单功能测试/功能交互测试/质量属性测试 |
| 测试对象（MFQ-M） | Documentation中的【测试对象】 | 单功能测试用例特有 |
| 功能交互（MFQ-F） | Documentation中的【功能交互】 | 功能交互测试用例特有 |
| 交互类型（MFQ-F） | Documentation中的【交互类型】 | 功能交互测试用例特有 |
| 质量属性（MFQ-Q） | Documentation中的【质量属性】 | 质量属性测试用例特有 |
| 质量属性说明（MFQ-Q） | Documentation中的【质量属性说明】 | 质量属性测试用例特有 |
| 测试维度（MFQ-Q） | Documentation中的【测试维度】 | 质量属性测试用例特有 |
| 前置条件 | Documentation中的【预置条件】 | 逐条列出，每行一个条件 |
| 测试步骤 | Documentation中的【测试步骤】+ 实际关键字调用 | 步骤描述 + 实现代码 |
| 预期结果 | Documentation中的【预期结果】+ 验证关键字 | 结果描述 + 断言关键字 |
| 质量验证标准（MFQ-Q） | Documentation中的【质量验证标准】 | 质量属性测试用例特有 |
| 验收准则 | Documentation中的【验收准则】 | Given/When/Then格式 |
| 作者 | Documentation中的【作者】 | 固定为"Auto Generated" |

Documentation格式要求：
- 每个字段标签（如【用例编号】）单独一行，使用 `...` 连接
- 字段值紧跟在下行，使用 `...` 连接
- 格式必须与API-test-spec.zh-CN.md中的格式完全一致
- 示例格式：
  ```robot
  [Documentation]
      ...    【用例编号】
      ...    MFQ-M-001
      ...    【用例名称】
      ...    用户登录
      ...    【用例等级】
      ...    P1
      ...
  ```
- 注意：字段标签和字段值必须分行，不能在同一行

### 3.2 测试步骤映射规则

#### 3.2.1 通用步骤映射

文本测试用例中的步骤描述需要转换为RobotFramework关键字调用：

| 文本步骤描述模式 | RobotFramework关键字 | 示例 |
|----------------|-------------------|------|
| 打开/访问/进入 [页面/URL] | `Go To` 或 `Navigate To` | "打开登录页面" → `Go To    ${BASE_URL}/login` |
| 输入 [值] 到 [元素] | `Input Text` | "输入用户名" → `Input Text    id=username    ${USERNAME}` |
| 点击 [按钮/链接] | `Click Button` 或 `Click Element` | "点击登录按钮" → `Click Button    id=login-btn` |
| 选择 [选项] | `Select From List By Value` | "选择城市" → `Select From List By Value    id=city    Beijing` |
| 等待 [条件] | `Wait Until ...` | "等待页面加载" → `Wait Until Page Contains    欢迎    timeout=${TIMEOUT}` |
| 验证/检查 [条件] | `Should Be Equal` / `Page Should Contain` | "验证登录成功" → `Page Should Contain    欢迎` |
| 获取 [值] | `Get Text` / `Get Value` | "获取用户名" → `${username}=    Get Text    id=username` |
| 设置 [变量] | `Set Variable` | "设置用户名" → `${username}=    Set Variable    testuser` |

#### 3.2.2 Web UI测试步骤映射

如果测试步骤涉及Web操作，使用SeleniumLibrary关键字：

```robot
# 打开页面
Go To    ${BASE_URL}/page

# 输入文本
Input Text    id=field-id    ${value}
Input Text    name=field-name    ${value}
Input Text    xpath=//input[@type='text']    ${value}

# 点击元素
Click Button    id=button-id
Click Element    id=element-id
Click Link    link=链接文本

# 选择下拉框
Select From List By Value    id=select-id    value
Select From List By Label    id=select-id    标签文本

# 等待元素
Wait Until Page Contains    文本内容    timeout=${TIMEOUT}
Wait Until Element Is Visible    id=element-id    timeout=${TIMEOUT}
Wait Until Location Contains    /path    timeout=${TIMEOUT}

# 验证元素
Page Should Contain    文本内容
Page Should Contain Element    id=element-id
Element Should Be Visible    id=element-id
Element Should Contain    id=element-id    预期文本
```

#### 3.2.3 API测试步骤映射

如果测试步骤涉及API调用，使用RequestsLibrary关键字：

```robot
# 发送GET请求
${response}=    GET    ${url}    headers=${headers}    params=${params}

# 发送POST请求
${response}=    POST    ${url}    headers=${headers}    json=${body}

# 验证响应状态码
Status Should Be    200    ${response}

# 验证响应内容
Should Be Equal As Strings    ${response.json()['field']}    ${expected_value}
Dictionary Should Contain Key    ${response.json()}    field_name
```

#### 3.2.4 数据库测试步骤映射

如果测试步骤涉及数据库操作，使用DatabaseLibrary关键字：

```robot
# 连接数据库
Connect To Database    pymysql    ${db_name}    ${db_user}    ${db_password}    ${db_host}    ${db_port}

# 执行查询
${result}=    Query    SELECT * FROM users WHERE id=1

# 验证查询结果
Should Be Equal As Strings    ${result[0][1]}    ${expected_value}

# 断开连接
Disconnect From Database
```

### 3.3 预期结果映射规则

文本测试用例中的预期结果需要转换为RobotFramework断言：

| 文本预期结果模式 | RobotFramework断言关键字 | 示例 |
|----------------|----------------------|------|
| [值] 等于 [值] | `Should Be Equal` | "用户名等于testuser" → `Should Be Equal    ${username}    testuser` |
| [值] 包含 [文本] | `Should Contain` | "页面包含欢迎" → `Page Should Contain    欢迎` |
| [元素] 存在 | `Page Should Contain Element` | "登录按钮存在" → `Page Should Contain Element    id=login-btn` |
| [值] 大于/小于 [值] | `Should Be True` | "响应时间小于2秒" → `Should Be True    ${time} < 2` |
| [状态] 为 [值] | `Should Be Equal As Strings` | "状态码为200" → `Status Should Be    200    ${response}` |

### 3.4 前置条件映射规则

文本测试用例中的前置条件需要转换为Setup或测试用例开头的初始化代码：

```robot
# 方式1：使用Test Setup（适用于所有测试用例）
*** Settings ***
Test Setup    初始化环境

*** Keywords ***
初始化环境
    # 前置条件1：系统已启动
    Go To    ${BASE_URL}
    # 前置条件2：用户账号已创建
    # 可以通过数据库操作或API调用创建测试数据
    Create Test User    ${USERNAME}    ${PASSWORD}

# 方式2：在测试用例中直接处理（适用于特定前置条件）
*** Test Cases ***
MFQ-M-001_用户登录
    # 前置条件1：系统已启动
    Go To    ${BASE_URL}
    # 前置条件2：用户账号已创建
    Create Test User    ${USERNAME}    ${PASSWORD}
    # 开始测试步骤
    ...
```

### 3.5 测试类型识别和库选择

根据文本测试用例的测试类型，选择合适的库：

| 测试类型 | 主要库 | 说明 |
|---------|--------|------|
| 单功能测试（Web UI） | SeleniumLibrary | Web页面操作 |
| 单功能测试（API） | RequestsLibrary | API调用 |
| 功能交互测试 | SeleniumLibrary + Collections | Web操作 + 数据验证 |
| 质量属性测试（性能） | BuiltIn（时间测量） | 性能测试 |
| 质量属性测试（安全性） | RequestsLibrary + String | 安全测试 |

### 3.6 特殊处理规则

#### 3.6.1 步骤描述解析

- 如果步骤描述不明确，需要根据上下文推断关键字
- 如果步骤描述包含多个操作，拆分为多个关键字调用
- 如果步骤描述包含条件判断，使用IF语句

#### 3.6.2 变量提取

- 从步骤描述中提取变量名和值
- 使用RobotFramework变量格式：`${variable_name}`
- 在Variables部分定义常量，在测试用例中使用变量

#### 3.6.3 错误处理

- 添加适当的超时设置
- 使用Try/Except处理可能的异常
- 添加日志输出便于调试


## [规则 4] 文件生成规则（优先级：中）
# 按照规范生成RobotFramework脚本文件

说明：
- **所有文件必须写入到用户指定的输出目录**
- 输出文件：`{输出目录}/文本测试用例-单功能.robot`、`{输出目录}/文本测试用例-功能交互.robot`、`{输出目录}/文本测试用例-质量属性.robot`
- 或统一输出到：`{输出目录}/文本测试用例-自动化脚本.robot`
- 文件格式：RobotFramework脚本（.robot）
- 文件结构清晰，便于维护

文件命名规则：
- 主文件：`{输出目录}/文本测试用例-自动化脚本.robot`（包含所有测试用例）
- 如果测试用例数量较多，可以按类型拆分文件：
  - `{输出目录}/文本测试用例-单功能.robot`
  - `{输出目录}/文本测试用例-功能交互.robot`
  - `{输出目录}/文本测试用例-质量属性.robot`
- 如果单个文件测试用例过多，可以按模块拆分：
  - `{输出目录}/文本测试用例-单功能-登录模块.robot`
  - `{输出目录}/文本测试用例-单功能-用户管理模块.robot`

文件组织规则：
- 每个.robot文件包含一个完整的测试套件
- 相同类型的测试用例放在同一个文件中
- 保持文件大小适中（建议每个文件不超过50个测试用例）

后果：
- 文件命名不规范会导致难以管理和维护
- 文件过大或过小会影响执行效率
- 文件结构混乱会导致难以定位问题

示例：
✅ 正确：
  - 输出文件：文本测试用例-单功能.robot
  - 文件结构清晰，包含Settings、Variables、Test Cases、Keywords部分
  - 每个测试用例格式规范

❌ 错误：
  - 输出文件：test.robot（命名不规范）
  - 文件结构混乱，缺少必要的部分
  - 测试用例格式不符合RobotFramework规范


## [规则 5] 脚本优化和最佳实践（优先级：中）
# 生成高质量、可维护的RobotFramework脚本

说明：
- 生成的脚本应该遵循RobotFramework最佳实践
- 提高脚本的可读性、可维护性和可重用性
- 减少代码重复，提高执行效率

### 5.1 代码重用

- 将公共操作提取为用户关键字（Keywords部分）
- 将公共变量定义在Variables部分
- 使用Resource导入公共资源文件

示例：
```robot
*** Keywords ***
登录系统
    [Documentation]    执行登录操作
    [Arguments]    ${username}    ${password}
    Go To    ${BASE_URL}/login
    Input Text    id=username    ${username}
    Input Text    id=password    ${password}
    Click Button    id=login-btn
    Wait Until Location Contains    /home    timeout=${TIMEOUT}

*** Test Cases ***
MFQ-M-001_用户登录
    ...
    登录系统    ${USERNAME}    ${PASSWORD}
    ...
```

### 5.2 错误处理

- 添加适当的超时设置
- 使用Try/Except处理可能的异常
- 添加详细的错误消息

示例：
```robot
*** Test Cases ***
MFQ-M-001_用户登录
    ...
    [Timeout]    30秒
    Run Keyword And Continue On Failure    登录系统    ${USERNAME}    ${PASSWORD}
    Run Keyword If    '${TEST_STATUS}' == 'FAIL'    Log    登录失败，请检查用户名和密码
    ...
```

### 5.3 日志和调试

- 添加适当的日志输出
- 使用Log关键字记录关键步骤
- 在关键验证点添加日志

示例：
```robot
*** Test Cases ***
MFQ-M-001_用户登录
    ...
    Log    开始执行登录操作
    登录系统    ${USERNAME}    ${PASSWORD}
    Log    登录操作完成，开始验证结果
    Page Should Contain    欢迎
    Log    验证通过，测试用例执行成功
    ...
```

### 5.4 数据驱动

- 对于相似的测试用例，考虑使用数据驱动
- 使用Test Template和循环减少代码重复

示例：
```robot
*** Settings ***
Test Template    登录测试模板

*** Test Cases ***
MFQ-M-001_用户登录_有效账号    ${VALID_USERNAME}    ${VALID_PASSWORD}    ${True}
MFQ-M-002_用户登录_无效账号    ${INVALID_USERNAME}    ${INVALID_PASSWORD}    ${False}

*** Keywords ***
登录测试模板
    [Arguments]    ${username}    ${password}    ${should_succeed}
    Go To    ${BASE_URL}/login
    Input Text    id=username    ${username}
    Input Text    id=password    ${password}
    Click Button    id=login-btn
    IF    ${should_succeed}
        Wait Until Location Contains    /home    timeout=${TIMEOUT}
        Page Should Contain    欢迎
    ELSE
        Page Should Contain    登录失败
    END
```


## [规则 6] 验证和完整性检查（优先级：高）
# 确保生成的RobotFramework脚本完整且可执行

说明：
- 验证生成的脚本是否符合RobotFramework语法规范
- 确保所有测试用例都已正确转换
- 检查脚本的完整性和可执行性

验证项：

1. **语法检查**
   - ✅ RobotFramework语法是否正确
   - ✅ 缩进是否正确（4个空格）
   - ✅ 关键字是否正确拼写
   - ✅ 变量使用是否正确

2. **完整性检查**
   - ✅ 是否包含Settings部分
   - ✅ 是否包含Test Cases部分
   - ✅ 每个测试用例是否包含Documentation
   - ✅ Documentation格式是否正确（字段标签和值分行，使用...连接）
   - ✅ Documentation中的信息是否与文本测试用例对应
   - ✅ 每个测试用例是否包含测试步骤实现
   - ✅ 每个测试用例是否包含预期结果验证
   - ✅ 是否包含[Setup]和[Teardown]（如果需要）

3. **信息完整性**
   - ✅ 测试用例编号是否与文本测试用例一致
   - ✅ 测试用例标题是否完整
   - ✅ 前置条件是否完整
   - ✅ 测试步骤是否完整转换
   - ✅ 预期结果是否完整转换
   - ✅ 验收准则是否包含

4. **可执行性检查**
   - ✅ 使用的库是否已导入
   - ✅ 使用的关键字是否存在
   - ✅ 变量是否已定义
   - ✅ 资源文件路径是否正确

5. **最佳实践检查**
   - ✅ 是否使用了适当的超时设置
   - ✅ 是否添加了错误处理
   - ✅ 是否添加了日志输出
   - ✅ 代码是否可重用

后果：
- 语法错误会导致脚本无法执行
- 信息不完整会导致测试用例无法正确执行
- 缺少验证会导致测试结果不可靠

示例：
✅ 正确：
  - 脚本语法正确，可以执行
  - 所有测试用例信息完整
  - 包含适当的验证和错误处理

❌ 错误：
  - 脚本语法错误，无法执行
  - 测试用例信息不完整
  - 缺少验证或错误处理


## [规则 7] 输出反馈和统计（优先级：低）
# 提供清晰的反馈信息和统计

说明：
- 生成完成后，提供详细的反馈信息
- 统计转换的测试用例数量
- 报告可能的问题或警告

反馈格式：
- 成功：`成功从 [N] 个文本测试用例生成 [M] 个RobotFramework测试用例，已保存到 {输出目录}/文本测试用例-自动化脚本.robot`
- 警告：`警告：[警告信息]`
- 错误：`错误：[错误信息]`

统计信息：
- 总测试用例数量
- 按类型分类的测试用例数量：
  - 单功能测试用例：X个
  - 功能交互测试用例：Y个
  - 质量属性测试用例：Z个
- 生成的.robot文件数量
- 转换成功率

示例：
```
成功从 15 个文本测试用例生成 15 个RobotFramework测试用例：
- 单功能测试用例：10个
- 功能交互测试用例：3个
- 质量属性测试用例：2个

已保存到以下文件：
- /workspace/output/文本测试用例-单功能.robot
- /workspace/output/文本测试用例-功能交互.robot
- /workspace/output/文本测试用例-质量属性.robot

警告：
- 2个测试用例的步骤描述不够明确，已根据上下文推断关键字
- 1个测试用例缺少预期结果，已添加默认验证
```


# ============================================
# 规则检查清单
# ============================================

使用本规范时，请确保以下规则都已遵循：

1. ✅ [规则 0] 输出目录管理，要求用户明确指定输出目录（关键）
2. ✅ [规则 1] 从文本测试用例文件读取测试用例（关键）
3. ✅ [规则 2] RobotFramework脚本格式标准（关键）
4. ✅ [规则 3] 文本测试用例到RobotFramework脚本的映射规则（高）
5. ✅ [规则 4] 文件生成规则（中）
6. ✅ [规则 5] 脚本优化和最佳实践（中）
7. ✅ [规则 6] 验证和完整性检查（高）
8. ✅ [规则 7] 输出反馈和统计（低）


# ============================================
# 使用示例
# ============================================

## 示例 1：完整流程

```
用户输入：
"请根据文本测试用例生成RobotFramework自动化脚本，输出目录=/workspace/output"

AI 处理：
1. 检查用户是否指定了输出目录
2. 用户指定：输出目录=/workspace/output
3. 验证目录是否存在，如不存在则创建
4. 读取 {输出目录}/文本测试用例.txt 文件
5. 解析所有测试用例：
   - MFQ-M-001：用户登录 → 单功能测试
   - MFQ-F-002：登录后查看个人信息 → 功能交互测试
   - MFQ-Q-003：登录性能测试 → 质量属性测试
6. 根据测试类型选择合适的库：
   - 单功能测试 → SeleniumLibrary
   - 功能交互测试 → SeleniumLibrary + Collections
   - 质量属性测试 → BuiltIn（时间测量）
7. 将每个测试用例转换为RobotFramework脚本：
   - 提取测试用例信息
   - 映射测试步骤到RobotFramework关键字
   - 添加验证和错误处理
8. 生成RobotFramework脚本文件：
   - {输出目录}/文本测试用例-单功能.robot
   - {输出目录}/文本测试用例-功能交互.robot
   - {输出目录}/文本测试用例-质量属性.robot
9. 验证脚本语法和完整性
10. 输出统计信息
11. 输出："成功从 15 个文本测试用例生成 15 个RobotFramework测试用例，已保存到 {输出目录}/文本测试用例-自动化脚本.robot"
```

## 示例 2：测试步骤映射

```
文本测试用例步骤：
步骤1：打开登录页面
步骤2：输入用户名"testuser"
步骤3：输入密码"password123"
步骤4：点击登录按钮
步骤5：验证页面跳转到首页

转换为RobotFramework脚本：
*** Test Cases ***
MFQ-M-001_用户登录
    [Documentation]
    ...    【用例编号】
    ...    MFQ-M-001
    ...    【用例名称】
    ...    用户登录
    ...    【用例等级】
    ...    P1
    ...    【测试类型】
    ...    单功能测试
    ...    【测试对象】
    ...    用户登录功能模块
    ...    【预置条件】
    ...    1. 系统已启动
    ...    2. 用户账号已创建
    ...    【测试步骤】
    ...    步骤1：打开登录页面
    ...    步骤2：输入用户名"testuser"
    ...    步骤3：输入密码"password123"
    ...    步骤4：点击登录按钮
    ...    步骤5：验证页面跳转到首页
    ...    【预期结果】
    ...    1. 登录成功
    ...    2. 跳转到首页
    ...    【验收准则】
    ...    Given: 用户已注册
    ...    When: 用户输入正确的用户名和密码并点击登录
    ...    Then: 系统验证通过并跳转到首页
    ...    【作者】
    ...    Auto Generated
    [Tags]    Auto    SingleFunction    Login
    [Setup]    
    # 步骤1：打开登录页面
    Go To    ${BASE_URL}/login
    # 步骤2：输入用户名"testuser"
    Input Text    id=username    testuser
    # 步骤3：输入密码"password123"
    Input Text    id=password    password123
    # 步骤4：点击登录按钮
    Click Button    id=login-btn
    # 步骤5：验证页面跳转到首页
    Wait Until Location Contains    /home    timeout=${TIMEOUT}
    Page Should Contain    欢迎
    [Teardown]    
```

## 示例 3：预期结果映射

```
文本测试用例预期结果：
1. 登录成功
2. 跳转到首页
3. 显示欢迎信息

转换为RobotFramework验证：
*** Test Cases ***
MFQ-M-001_用户登录
    [Documentation]
    ...    【用例编号】
    ...    MFQ-M-001
    ...    【用例名称】
    ...    用户登录
    ...    【用例等级】
    ...    P1
    ...    【测试类型】
    ...    单功能测试
    ...    【预置条件】
    ...    1. 系统已启动
    ...    【测试步骤】
    ...    步骤1：打开登录页面
    ...    步骤2：输入用户名和密码
    ...    步骤3：点击登录按钮
    ...    【预期结果】
    ...    1. 登录成功
    ...    2. 跳转到首页
    ...    3. 显示欢迎信息
    ...    【验收准则】
    ...    Given: 用户已注册
    ...    When: 用户输入正确的用户名和密码并点击登录
    ...    Then: 系统验证通过并跳转到首页
    ...    【作者】
    ...    Auto Generated
    [Tags]    Auto    SingleFunction    Login
    [Setup]    
    # 执行测试步骤
    Go To    ${BASE_URL}/login
    Input Text    id=username    ${USERNAME}
    Input Text    id=password    ${PASSWORD}
    Click Button    id=login-btn
    # 验证预期结果1：登录成功（通过URL判断）
    Wait Until Location Contains    /home    timeout=${TIMEOUT}
    # 验证预期结果2：跳转到首页（通过URL判断）
    Location Should Be    ${BASE_URL}/home
    # 验证预期结果3：显示欢迎信息
    Page Should Contain    欢迎
    [Teardown]    
```


# ============================================
# 版本历史
# ============================================

# v1.0 (2025-01-XX) - 初始版本，定义从文本测试用例生成RobotFramework自动化脚本的规范
