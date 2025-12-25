---
trigger: manual
---

# RobotFramework 测试脚本生成检查策略规范 v1.0
# ============================================
# AI 辅助开发的 RobotFramework 测试脚本生成检查标准和要求
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

## [检查 1] RobotFramework 文件结构完整性 [ENABLED]
# 确保 .robot 文件包含必要的结构部分

STATUS: ENABLED
说明：
- RobotFramework 测试脚本必须包含完整的文件结构
- 必须包含 Settings、Test Cases、Keywords 等必要部分
- 文件格式必须符合 RobotFramework 语法规范

检查项：
- ✅ 文件扩展名为 .robot
- ✅ 包含 *** Settings *** 部分
- ✅ 包含 *** Test Cases *** 部分
- ✅ 包含 *** Keywords *** 部分（如需要）
- ✅ 包含 *** Variables *** 部分（如需要）
- ✅ 各部分顺序正确（Settings → Variables → Test Cases → Keywords）
- ✅ 使用正确的分隔符（*** 和 ***）

示例：
```robot
*** Settings ***
Documentation    用户登录功能测试
Library          SeleniumLibrary
Resource         ../resources/common.robot
Test Setup       Open Browser
Test Teardown    Close Browser

*** Variables ***
${LOGIN_URL}     https://example.com/login
${USERNAME}      testuser
${PASSWORD}      testpass

*** Test Cases ***
M-001: 用户使用有效凭据登录应成功
    [Documentation]    测试用户使用有效用户名和密码登录
    [Tags]    smoke    login
    Input Text    id=username    ${USERNAME}
    Input Text    id=password    ${PASSWORD}
    Click Button    id=login-btn
    Wait Until Page Contains    欢迎
    Page Should Contain    欢迎, ${USERNAME}

*** Keywords ***
打开登录页面
    Go To    ${LOGIN_URL}
    Wait Until Page Contains Element    id=username
```

```robot
# ❌ 错误：缺少必要的结构部分
*** Test Cases ***
测试登录
    Click Button    login
```

检查策略：
- ✅ Settings 部分必须包含
- ✅ Test Cases 部分必须包含
- ✅ 至少包含一个测试用例
- ✅ 各部分使用正确的分隔符格式


## [检查 2] Settings 部分配置完整性 [ENABLED]
# 确保 Settings 部分配置正确和完整

STATUS: ENABLED
说明：
- Settings 部分必须包含必要的配置
- 库导入必须正确
- 资源文件导入路径必须正确
- 测试设置和清理必须配置

检查项：
- ✅ 包含必要的库导入（如 SeleniumLibrary、RequestsLibrary 等）
- ✅ 库名称拼写正确
- ✅ 资源文件路径正确（如使用）
- ✅ Test Setup 和 Test Teardown 配置（如需要）
- ✅ Suite Setup 和 Suite Teardown 配置（如需要）
- ✅ Documentation 设置（建议）

示例：
```robot
*** Settings ***
Documentation    用户管理功能测试套件
Library          SeleniumLibrary
Library          Collections
Resource         ../resources/common.robot
Resource         ../resources/user_keywords.robot
Test Setup       打开浏览器
Test Teardown    关闭浏览器
Suite Setup      初始化测试环境
Suite Teardown   清理测试数据
Default Tags     regression
```

```robot
# ❌ 错误：库名称拼写错误，资源文件路径错误
*** Settings ***
Library          SeleniumLibary    # 拼写错误
Resource         ../wrong/path.robot    # 路径不存在
```

检查策略：
- ✅ 所有导入的库必须存在
- ✅ 所有资源文件路径必须正确
- ✅ Test Setup/Teardown 关键字必须存在


## [检查 3] 测试用例结构完整性 [ENABLED]
# 确保测试用例结构完整和规范

STATUS: ENABLED
说明：
- 测试用例必须包含必要的元数据
- 测试用例名称必须符合命名规范
- 测试步骤必须清晰和完整
- 测试用例必须包含断言

检查项：
- ✅ 测试用例名称符合规范（包含测试点标识 M/F/Q）
- ✅ 包含 [Documentation] 标签（建议）
- ✅ 包含 [Tags] 标签（建议）
- ✅ 测试步骤清晰，使用关键字
- ✅ 包含验证步骤（断言）
- ✅ 测试用例独立，不依赖其他测试用例

示例：
```robot
*** Test Cases ***
M-001: 用户使用有效凭据登录应成功
    [Documentation]    测试用户使用有效用户名和密码登录成功
    [Tags]    smoke    login    positive
    [Setup]    打开登录页面
    Input Text    id=username    ${VALID_USERNAME}
    Input Text    id=password    ${VALID_PASSWORD}
    Click Button    id=login-btn
    Wait Until Page Contains    欢迎
    Page Should Contain    欢迎, ${VALID_USERNAME}
    [Teardown]    退出登录

M-002: 用户使用无效密码登录应失败
    [Documentation]    测试用户使用无效密码登录失败
    [Tags]    login    negative
    [Setup]    打开登录页面
    Input Text    id=username    ${VALID_USERNAME}
    Input Text    id=password    ${INVALID_PASSWORD}
    Click Button    id=login-btn
    Wait Until Page Contains    用户名或密码错误
    Page Should Not Contain    欢迎
```

```robot
# ❌ 错误：测试用例缺少文档、标签和断言
*** Test Cases ***
测试登录
    Input Text    username    test
    Click Button    login
    # 缺少断言
```

检查策略：
- ✅ 测试用例名称必须包含测试点标识（M-001, F-001, Q-001）
- ✅ 每个测试用例必须包含至少一个断言
- ✅ 测试用例必须独立，不依赖执行顺序


## [检查 4] 关键字使用规范 [ENABLED]
# 确保关键字使用正确和规范

STATUS: ENABLED
说明：
- 关键字名称必须正确
- 关键字参数必须匹配
- 关键字必须来自已导入的库或资源文件
- 用户定义关键字必须存在

检查项：
- ✅ 关键字名称拼写正确
- ✅ 关键字参数数量正确
- ✅ 关键字参数类型正确
- ✅ 关键字来自已导入的库或资源文件
- ✅ 用户定义关键字在 Keywords 部分定义
- ✅ 关键字参数使用变量（如需要）

示例：
```robot
*** Test Cases ***
M-001: 用户登录测试
    [Documentation]    测试用户登录功能
    [Tags]    login
    打开登录页面
    输入用户名    ${USERNAME}
    输入密码    ${PASSWORD}
    点击登录按钮
    验证登录成功    ${USERNAME}

*** Keywords ***
打开登录页面
    Go To    ${LOGIN_URL}
    Wait Until Page Contains Element    id=username

输入用户名
    [Arguments]    ${username}
    Input Text    id=username    ${username}

输入密码
    [Arguments]    ${password}
    Input Text    id=password    ${password}

点击登录按钮
    Click Button    id=login-btn

验证登录成功
    [Arguments]    ${expected_username}
    Wait Until Page Contains    欢迎
    Page Should Contain    欢迎, ${expected_username}
```

```robot
# ❌ 错误：关键字不存在，参数不匹配
*** Test Cases ***
测试登录
    InputText    username    test    # 关键字名称错误（应该是 Input Text）
    ClickButton    login    # 缺少参数
    VerifyLogin    # 关键字不存在
```

检查策略：
- ✅ 所有关键字必须存在（库关键字或用户定义关键字）
- ✅ 关键字参数必须匹配定义
- ✅ 关键字参数数量必须正确


## [检查 5] 变量使用规范 [ENABLED]
# 确保变量使用正确和规范

STATUS: ENABLED
说明：
- 变量定义必须正确
- 变量作用域必须明确
- 变量命名必须规范
- 变量值必须合理

检查项：
- ✅ 变量定义格式正确（${variable} 或 @{list} 或 &{dict}）
- ✅ 变量名称使用有意义的名称
- ✅ 变量在正确的作用域定义
- ✅ 变量值合理（不是硬编码的魔法值）
- ✅ 列表和字典变量格式正确

示例：
```robot
*** Variables ***
${LOGIN_URL}         https://example.com/login
${VALID_USERNAME}    testuser
${VALID_PASSWORD}    testpass123
${INVALID_PASSWORD}    wrongpass
@{USER_ROLES}        admin    user    guest
&{USER_DATA}         username=testuser    password=testpass    email=test@example.com

*** Test Cases ***
M-001: 用户登录测试
    [Documentation]    使用变量进行登录测试
    Go To    ${LOGIN_URL}
    Input Text    id=username    ${VALID_USERNAME}
    Input Text    id=password    ${VALID_PASSWORD}
    Click Button    id=login-btn
    Page Should Contain    ${VALID_USERNAME}
```

```robot
# ❌ 错误：变量格式错误，使用魔法值
*** Test Cases ***
测试登录
    Go To    https://example.com/login    # 应该使用变量
    Input Text    username    test    # 应该使用变量
    Input Text    password    123    # 魔法值
```

检查策略：
- ✅ 避免硬编码的值，使用变量
- ✅ 变量命名使用大写字母和下划线
- ✅ 变量定义在 Variables 部分或使用 Set Variable


## [检查 6] 测试点命名规范 [ENABLED]
# 确保测试用例命名符合测试点生成策略

STATUS: ENABLED
说明：
- 测试用例名称必须包含测试点标识（M/F/Q）
- 测试用例名称必须描述预期行为
- 测试用例名称必须清晰和有意义

检查项：
- ✅ 测试用例名称格式：{类型}-{序号}: {描述}
- ✅ 测试点类型：M（单功能）、F（组合）、Q（非功能）
- ✅ 测试点序号：3位数字（001, 002, 003...）
- ✅ 测试用例描述使用中文或英文，清晰表达预期行为
- ✅ 测试用例描述长度：10-100字符

示例：
```robot
*** Test Cases ***
M-001: 用户使用有效凭据登录应成功
M-002: 用户使用无效密码登录应失败
M-003: 用户名为空时登录应显示错误提示
F-001: 用户名和密码组合验证应覆盖所有边界情况
Q-001: 登录接口响应时间应小于100毫秒
Q-002: 登录功能应能处理100个并发请求
```

```robot
# ❌ 错误：命名不符合规范
*** Test Cases ***
测试1
登录测试
test_login
用户登录    # 缺少测试点标识
```

检查策略：
- ✅ 所有测试用例必须包含测试点标识
- ✅ 测试点序号必须连续
- ✅ 测试用例描述必须清晰表达预期行为


## [检查 7] 断言和验证完整性 [ENABLED]
# 确保测试用例包含完整的断言和验证

STATUS: ENABLED
说明：
- 每个测试用例必须包含至少一个断言
- 断言必须验证预期的结果
- 断言必须使用正确的关键字

检查项：
- ✅ 每个测试用例至少包含一个断言关键字
- ✅ 断言验证正确的值或状态
- ✅ 使用合适的断言关键字（Should Be Equal, Should Contain, Page Should Contain 等）
- ✅ 断言消息清晰（如需要）

示例：
```robot
*** Test Cases ***
M-001: 用户登录应成功
    [Documentation]    验证用户登录成功后的页面内容
    Go To    ${LOGIN_URL}
    Input Text    id=username    ${USERNAME}
    Input Text    id=password    ${PASSWORD}
    Click Button    id=login-btn
    Wait Until Page Contains    欢迎
    Page Should Contain    欢迎, ${USERNAME}
    Location Should Be    ${HOME_URL}
    Title Should Be    首页

M-002: 用户登录失败应显示错误消息
    [Documentation]    验证登录失败时的错误提示
    Go To    ${LOGIN_URL}
    Input Text    id=username    ${USERNAME}
    Input Text    id=password    ${INVALID_PASSWORD}
    Click Button    id=login-btn
    Wait Until Page Contains    用户名或密码错误
    Page Should Contain    用户名或密码错误
    Location Should Be    ${LOGIN_URL}
```

```robot
# ❌ 错误：缺少断言
*** Test Cases ***
测试登录
    Go To    ${LOGIN_URL}
    Input Text    id=username    ${USERNAME}
    Input Text    id=password    ${PASSWORD}
    Click Button    id=login-btn
    # 缺少断言验证
```

检查策略：
- ✅ 每个测试用例必须包含至少一个断言
- ✅ 断言必须验证测试的预期结果
- ✅ 正向测试和负向测试都必须包含断言


## [检查 8] 测试数据和Fixture管理 [ENABLED]
# 确保测试数据和Fixture管理规范

STATUS: ENABLED
说明：
- 测试数据应该使用变量或资源文件
- 测试数据应该独立和可重复
- Setup 和 Teardown 应该正确配置

检查项：
- ✅ 测试数据使用变量定义
- ✅ 测试数据在 Variables 部分或资源文件中定义
- ✅ Setup 和 Teardown 关键字存在
- ✅ 测试数据独立，不依赖其他测试用例
- ✅ 测试后清理数据（如需要）

示例：
```robot
*** Settings ***
Resource    ../resources/test_data.robot
Resource    ../resources/common_keywords.robot

*** Variables ***
${VALID_USERNAME}    testuser@example.com
${VALID_PASSWORD}    TestPass123!
${INVALID_USERNAME}    invalid@example.com
${INVALID_PASSWORD}    wrongpass

*** Test Cases ***
M-001: 用户登录应成功
    [Setup]    打开浏览器并导航到登录页
    [Teardown]    关闭浏览器
    输入用户名    ${VALID_USERNAME}
    输入密码    ${VALID_PASSWORD}
    点击登录按钮
    验证登录成功    ${VALID_USERNAME}

*** Keywords ***
打开浏览器并导航到登录页
    Open Browser    ${BROWSER}    ${LOGIN_URL}
    Maximize Browser Window

关闭浏览器
    Close All Browsers
```

```robot
# ❌ 错误：硬编码测试数据，缺少清理
*** Test Cases ***
测试登录
    Open Browser    chrome    https://example.com/login
    Input Text    username    testuser@example.com    # 硬编码
    Input Text    password    TestPass123!    # 硬编码
    Click Button    login
    # 缺少 Teardown 清理
```

检查策略：
- ✅ 避免硬编码测试数据
- ✅ 使用 Setup 和 Teardown 管理测试环境
- ✅ 测试数据应该可配置和可重复


## [检查 9] 标签使用规范 [ENABLED]
# 确保测试用例标签使用规范

STATUS: ENABLED
说明：
- 测试用例应该包含有意义的标签
- 标签应该用于分类和筛选
- 标签命名应该规范

检查项：
- ✅ 测试用例包含 [Tags] 标签（建议）
- ✅ 标签名称有意义（smoke, regression, positive, negative 等）
- ✅ 标签命名规范（小写字母，使用下划线）
- ✅ Suite 级别标签设置（如需要）

示例：
```robot
*** Settings ***
Default Tags    regression

*** Test Cases ***
M-001: 用户登录应成功
    [Documentation]    测试用户登录功能
    [Tags]    smoke    login    positive
    打开登录页面
    输入用户名    ${VALID_USERNAME}
    输入密码    ${VALID_PASSWORD}
    点击登录按钮
    验证登录成功

M-002: 用户登录失败应显示错误
    [Documentation]    测试登录失败场景
    [Tags]    login    negative
    打开登录页面
    输入用户名    ${VALID_USERNAME}
    输入密码    ${INVALID_PASSWORD}
    点击登录按钮
    验证登录失败

Q-001: 登录接口性能测试
    [Documentation]    测试登录接口响应时间
    [Tags]    performance    api
    验证接口响应时间    ${LOGIN_API}    ${VALID_CREDENTIALS}    100
```

检查策略：
- ✅ 建议为每个测试用例添加标签
- ✅ 标签应该反映测试类型和优先级
- ✅ 使用一致的标签命名规范


## [检查 10] 文档完整性 [ENABLED]
# 确保测试用例和关键字包含文档

STATUS: ENABLED
说明：
- 测试用例应该包含 [Documentation]
- 用户定义关键字应该包含文档
- 文档应该清晰描述测试目的和步骤

检查项：
- ✅ 测试用例包含 [Documentation]（建议）
- ✅ 用户定义关键字包含 [Documentation]（建议）
- ✅ 文档内容清晰，描述测试目的
- ✅ 文档使用中文或英文，表达清晰

示例：
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
    验证登录成功    ${VALID_USERNAME}

*** Keywords ***
打开登录页面
    [Documentation]    打开浏览器并导航到登录页面
    ...                等待页面加载完成
    Open Browser    ${BROWSER}    ${LOGIN_URL}
    Maximize Browser Window
    Wait Until Page Contains Element    id=username

验证登录成功
    [Arguments]    ${expected_username}
    [Documentation]    验证登录成功后的页面状态
    ...                检查页面包含欢迎信息和用户名
    Wait Until Page Contains    欢迎
    Page Should Contain    欢迎, ${expected_username}
    Location Should Be    ${HOME_URL}
```

检查策略：
- ✅ 建议为所有测试用例添加文档
- ✅ 建议为所有用户定义关键字添加文档
- ✅ 文档应该清晰描述测试目的和验证点


## [检查 11] 资源文件和库导入 [ENABLED]
# 确保资源文件和库导入正确

STATUS: ENABLED
说明：
- 资源文件路径必须正确
- 库导入必须正确
- 导入的库和资源文件必须存在

检查项：
- ✅ 资源文件路径正确（相对路径或绝对路径）
- ✅ 库名称拼写正确
- ✅ 库版本指定（如需要）
- ✅ 导入的库和资源文件存在
- ✅ 避免循环依赖

示例：
```robot
*** Settings ***
Documentation    用户管理功能测试
Library          SeleniumLibrary
Library          Collections
Library          RequestsLibrary
Resource         ../resources/common.robot
Resource         ../resources/user_keywords.robot
Resource         ../resources/test_data.robot
Variables        ../variables/config.py
```

```robot
# ❌ 错误：资源文件路径错误，库不存在
*** Settings ***
Library          SeleniumLibary    # 拼写错误
Resource         ../wrong/path.robot    # 路径不存在
Library          NonExistentLibrary    # 库不存在
```

检查策略：
- ✅ 所有导入的库必须存在
- ✅ 所有资源文件路径必须正确
- ✅ 避免循环依赖


## [检查 12] 测试用例独立性 [ENABLED]
# 确保测试用例之间相互独立

STATUS: ENABLED
说明：
- 测试用例应该独立运行
- 测试用例之间不应该有依赖关系
- 测试用例执行顺序不应该影响结果

检查项：
- ✅ 每个测试用例独立，不依赖其他测试用例
- ✅ 测试用例使用独立的测试数据
- ✅ Setup 和 Teardown 确保测试环境隔离
- ✅ 测试用例可以单独运行
- ✅ 测试用例可以并行运行（如支持）

示例：
```robot
*** Test Cases ***
M-001: 用户登录应成功
    [Setup]    打开浏览器并导航到登录页
    [Teardown]    关闭浏览器
    输入用户名    ${VALID_USERNAME}
    输入密码    ${VALID_PASSWORD}
    点击登录按钮
    验证登录成功

M-002: 用户登录失败应显示错误
    [Setup]    打开浏览器并导航到登录页
    [Teardown]    关闭浏览器
    输入用户名    ${VALID_USERNAME}
    输入密码    ${INVALID_PASSWORD}
    点击登录按钮
    验证登录失败
```

```robot
# ❌ 错误：测试用例之间有依赖
*** Test Cases ***
M-001: 创建用户
    [Teardown]    # 不清理，依赖下一个测试
    创建用户    ${USERNAME}

M-002: 删除用户
    # 依赖 M-001 创建的用户
    删除用户    ${USERNAME}
```

检查策略：
- ✅ 每个测试用例必须独立
- ✅ 使用 Setup 和 Teardown 确保环境隔离
- ✅ 测试用例不应该依赖执行顺序


## [检查 13] 错误处理和异常场景 [ENABLED]
# 确保测试用例包含错误处理和异常场景测试

STATUS: ENABLED
说明：
- 测试用例应该包含错误处理测试
- 应该测试异常输入和边界条件
- 应该验证错误消息和状态

检查项：
- ✅ 包含错误处理测试用例（负向测试）
- ✅ 测试边界条件和异常输入
- ✅ 验证错误消息和状态
- ✅ 使用 [Template] 测试多个错误场景（如适用）

示例：
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

M-003: 密码为空时登录应显示错误提示
    [Documentation]    测试密码为空时的错误处理
    [Tags]    login    negative    validation
    打开登录页面
    输入用户名    ${VALID_USERNAME}
    点击登录按钮
    等待错误提示显示
    验证错误消息    密码不能为空

M-004: 无效凭据登录应显示错误提示
    [Documentation]    测试使用无效凭据登录的错误处理
    [Tags]    login    negative
    打开登录页面
    输入用户名    ${INVALID_USERNAME}
    输入密码    ${INVALID_PASSWORD}
    点击登录按钮
    等待错误提示显示
    验证错误消息    用户名或密码错误
```

检查策略：
- ✅ 必须包含错误处理测试用例
- ✅ 测试边界条件和异常输入
- ✅ 验证错误消息和状态


## [检查 14] 测试点对应关系 [ENABLED]
# 确保测试脚本包含M/F/Q测试点

STATUS: ENABLED
说明：
- 测试脚本必须包含M测试点（单功能测试）
- 测试脚本可以包含F测试点（组合测试）
- 测试脚本可以包含Q测试点（非功能测试）

检查项：
- ✅ 包含M测试点（单功能测试，必须）
- ✅ 包含F测试点（组合测试，可选）
- ✅ 包含Q测试点（非功能测试，可选）
- ✅ 测试点命名格式正确（M-001, F-001, Q-001）
- ✅ 测试点数量符合要求

示例：
```robot
*** Test Cases ***
# M-单功能测试点
M-001: 用户使用有效凭据登录应成功
    [Tags]    login    positive
    打开登录页面
    输入用户名    ${VALID_USERNAME}
    输入密码    ${VALID_PASSWORD}
    点击登录按钮
    验证登录成功

M-002: 用户使用无效密码登录应失败
    [Tags]    login    negative
    打开登录页面
    输入用户名    ${VALID_USERNAME}
    输入密码    ${INVALID_PASSWORD}
    点击登录按钮
    验证登录失败

# F-组合测试点
F-001: 用户名和密码组合验证应覆盖所有边界情况
    [Tags]    login    combination
    [Template]    验证登录组合
    ${VALID_USERNAME}    ${VALID_PASSWORD}    ${TRUE}
    ${VALID_USERNAME}    ${INVALID_PASSWORD}    ${FALSE}
    ${INVALID_USERNAME}    ${VALID_PASSWORD}    ${FALSE}
    ${EMPTY}    ${VALID_PASSWORD}    ${FALSE}
    ${VALID_USERNAME}    ${EMPTY}    ${FALSE}

# Q-非功能测试点
Q-001: 登录接口响应时间应小于100毫秒
    [Tags]    login    performance
    验证接口响应时间    ${LOGIN_API}    ${VALID_CREDENTIALS}    100

Q-002: 登录功能应能处理100个并发请求
    [Tags]    login    performance    concurrency
    执行并发登录测试    ${LOGIN_API}    ${VALID_CREDENTIALS}    100
```

检查策略：
- ✅ M测试点必须包含（每个功能至少3个）
- ✅ F测试点可选（多参数组合时建议包含）
- ✅ Q测试点可选（关键功能建议包含）


# ============================================
# 检查策略配置
# ============================================

## 检查策略配置文件

检查策略可以通过配置文件进行设置：

```yaml
# .test-script-check.yaml
robotframework_test_script:
  # 文件结构检查
  structure:
    enabled: true
    require_settings: true
    require_test_cases: true
    require_keywords: false  # 可选
    require_variables: false  # 可选
  
  # Settings 部分检查
  settings:
    enabled: true
    require_library: true
    require_resource: false  # 可选
    require_documentation: false  # 建议
    require_test_setup: false  # 可选
    require_test_teardown: false  # 可选
  
  # 测试用例结构检查
  test_case_structure:
    enabled: true
    require_documentation: false  # 建议
    require_tags: false  # 建议
    require_setup: false  # 可选
    require_teardown: false  # 可选
    min_steps: 1  # 最少步骤数
    require_assertion: true  # 必须包含断言
  
  # 关键字使用检查
  keywords:
    enabled: true
    require_keyword_exists: true
    require_correct_arguments: true
    forbid_undefined_keywords: true
  
  # 变量使用检查
  variables:
    enabled: true
    require_variable_usage: false  # 建议使用变量
    forbid_hardcoded_values: true  # 禁止硬编码
    require_meaningful_names: true
  
  # 测试点命名检查
  test_point_naming:
    enabled: true
    require_test_point_prefix: true
    pattern: "{type}-{seq:03d}: {description}"
    types: ["M", "F", "Q"]
    min_description_length: 10
    max_description_length: 100
  
  # 断言检查
  assertions:
    enabled: true
    min_assertions_per_test: 1
    require_meaningful_assertions: true
  
  # 测试数据和Fixture检查
  test_data:
    enabled: true
    require_variables: false  # 建议
    require_setup_teardown: false  # 建议
    forbid_hardcoded_data: true
  
  # 标签检查
  tags:
    enabled: true
    require_tags: false  # 建议
    require_meaningful_tags: true
  
  # 文档检查
  documentation:
    enabled: true
    require_test_documentation: false  # 建议
    require_keyword_documentation: false  # 建议
  
  # 资源文件和库导入检查
  imports:
    enabled: true
    require_library_exists: true
    require_resource_exists: true
    forbid_circular_dependencies: true
  
  # 测试用例独立性检查
  isolation:
    enabled: true
    require_independent_tests: true
    require_setup_teardown: true  # 建议
    forbid_test_dependencies: true
  
  # 错误处理检查
  error_handling:
    enabled: true
    require_error_tests: true
    require_boundary_tests: true
  
  # 测试点对应关系检查
  test_point_mapping:
    enabled: true
    require_m_tests: true
    require_f_tests: false
    require_q_tests: false
    m_test_requirements:
      min_count: 3
      require_positive: true
      require_boundary: true
      require_negative: true
```

## 检查项优先级

### 关键检查项（必须通过）
- 文件结构完整性（Settings、Test Cases）
- 关键字存在性（所有关键字必须存在）
- 断言完整性（每个测试用例至少一个断言）
- 测试点命名规范（必须包含M/F/Q前缀）
- 资源文件和库导入（所有导入必须存在）

### 重要检查项（建议通过）
- 测试用例文档（Documentation）
- 标签使用（Tags）
- 变量使用（避免硬编码）
- 测试用例独立性（Setup/Teardown）
- 错误处理测试（负向测试）

### 一般检查项（可选）
- 关键字文档（Documentation）
- Suite级别设置（Suite Setup/Teardown）
- 高级标签使用


# ============================================
# 项目类型配置
# ============================================

Web UI 测试：
- 启用： [检查 1-14]
- 关键检查：文件结构、关键字使用、断言完整性
- 测试点要求：M测试点必须，F/Q测试点可选
- 库要求：SeleniumLibrary

API 测试：
- 启用： [检查 1-14]
- 关键检查：文件结构、关键字使用、断言完整性
- 测试点要求：M测试点必须，F/Q测试点可选
- 库要求：RequestsLibrary

数据库测试：
- 启用： [检查 1-14]
- 关键检查：文件结构、关键字使用、数据清理
- 测试点要求：M测试点必须，F测试点可选
- 库要求：DatabaseLibrary


# ============================================
# 与其他规范的集成
# ============================================

DEPENDENCIES:
  test-script-generation-check.zh-CN.md::检查 1 -> testing-spec.zh-CN.md::规则 1
    note: RobotFramework测试脚本结构完整性遵循测试完整性要求
  test-script-generation-check.zh-CN.md::检查 6 -> test-case-generation-strategy.zh-CN.md::策略 1-3
    note: 测试点命名规范遵循测试点生成策略
  test-script-generation-check.zh-CN.md::检查 14 -> test-case-generation-strategy.zh-CN.md::策略 1-3
    note: 测试脚本必须包含M/F/Q测试点


# ============================================
# 摘要 - 启用的检查项
# ============================================

✅ [检查 1]  RobotFramework 文件结构完整性 - 确保包含必要的结构部分
✅ [检查 2]  Settings 部分配置完整性 - 确保Settings配置正确
✅ [检查 3]  测试用例结构完整性 - 确保测试用例结构完整
✅ [检查 4]  关键字使用规范 - 确保关键字使用正确
✅ [检查 5]  变量使用规范 - 确保变量使用正确
✅ [检查 6]  测试点命名规范 - 确保测试用例命名符合规范
✅ [检查 7]  断言和验证完整性 - 确保包含完整的断言
✅ [检查 8]  测试数据和Fixture管理 - 确保测试数据管理规范
✅ [检查 9]  标签使用规范 - 确保标签使用规范
✅ [检查 10] 文档完整性 - 确保测试用例和关键字包含文档
✅ [检查 11] 资源文件和库导入 - 确保导入正确
✅ [检查 12] 测试用例独立性 - 确保测试用例相互独立
✅ [检查 13] 错误处理和异常场景 - 确保包含错误处理测试
✅ [检查 14] 测试点对应关系 - 确保包含M/F/Q测试点


# ============================================
# 版本历史
# ============================================
# v1.0 (2025-01-XX) - RobotFramework专用测试脚本生成检查策略规范，包含 14 个检查项
# ============================================
