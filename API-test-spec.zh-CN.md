---
trigger: manual
---

# 接口测试规范 v1.0
# ============================================
# 基于接口定义生成测试数据和RobotFramework自动化测试脚本的规则和要求
# 严格遵循OpenAPI V3.0规范和RobotFramework语法标准
#
# 使用方法：
# 1. 将此文件放在项目根目录
# 2. 在 AI 对话中使用 @API-test-spec.zh-CN.md 引用
# 3. AI 将自动遵循这些规则生成接口测试数据和测试脚本
#
# 输入来源：
# - 接口规格.md（从需求方案页面的组件接口说明章节提取）
# - OpenAPI V3.0 YAML格式的接口定义
#
# 输出格式：
# - 测试数据（YAML格式）
# - RobotFramework测试脚本（.robot文件）
# 最后更新：2025-01-XX
# ============================================

## [规则 1] 从接口规格.md读取接口定义（优先级：关键）
# 必须从接口规格.md文件中读取接口定义信息

说明：
- 读取当前目录下的 `接口规格.md` 文件
- 解析文件中的"组件接口说明"章节
- 识别OpenAPI V3.0规范的YAML格式接口定义
- 提取每个接口的完整信息：接口概述、入参、返回等
- 如果文件不存在，提示用户先执行需求提取流程

工作流程：
1. 检查 `接口规格.md` 文件是否存在
2. 如果不存在，提示："接口规格.md 文件不存在，请先执行需求提取流程"
3. 如果存在，解析文件中的"组件接口说明"章节
4. 识别OpenAPI V3.0格式的接口定义（YAML格式）
5. 提取每个接口的完整信息：
   - 接口概述（接口描述、路径、HTTP方法等）
   - 入参（请求参数、请求体结构等）
   - 返回（响应结构、状态码等）
6. 验证接口定义是否完整

后果：
- 未从正确来源读取会导致测试数据与接口定义不匹配
- 遗漏接口会导致测试覆盖率不足
- YAML格式错误会导致无法解析接口定义

示例：
✅ 正确：
  - 读取：接口规格.md
  - 解析：组件接口说明章节
  - 识别：OpenAPI V3.0 YAML格式
  - 提取：接口1、接口2...的所有信息

❌ 错误：
  - 从其他文件读取接口定义
  - 手动输入接口定义
  - 遗漏部分接口
  - YAML格式错误


## [规则 2] OpenAPI V3.0接口定义解析（优先级：关键）
# 准确解析OpenAPI V3.0规范的接口定义

说明：
- 接口定义遵循OpenAPI V3.0规范
- 按照YAML格式提取和解析
- 必须识别接口的所有关键信息

OpenAPI V3.0关键字段：

1. **接口概述**
   - `openapi`: OpenAPI版本（必须是3.0.0）
   - `info`: 接口信息（标题、版本、描述等）
   - `servers`: 服务器地址列表
   - `paths`: 接口路径定义
   - `components`: 组件定义（schemas、parameters等）

2. **路径和操作**
   - `paths/{path}`: 接口路径
   - `get/post/put/delete`: HTTP方法
   - `summary`: 接口摘要
   - `description`: 接口描述
   - `operationId`: 操作ID
   - `tags`: 标签

3. **入参定义**
   - `parameters`: 路径参数、查询参数
   - `requestBody`: 请求体
   - `content`: 内容类型（application/json等）
   - `schema`: 数据模式定义
   - `required`: 必填字段
   - `type`: 数据类型（string、integer、number、boolean、array、object）
   - `format`: 数据格式（date、date-time、email等）
   - `enum`: 枚举值
   - `minLength/maxLength`: 字符串长度限制
   - `minimum/maximum`: 数值范围限制
   - `pattern`: 正则表达式模式
   - `items`: 数组元素定义
   - `properties`: 对象属性定义

4. **返回定义**
   - `responses`: 响应定义
   - `{statusCode}`: HTTP状态码（200、400、500等）
   - `description`: 响应描述
   - `content`: 响应内容
   - `schema`: 响应数据模式

解析要求：
- 必须解析所有接口路径和方法
- 必须提取每个接口的完整入参定义
- 必须提取每个接口的完整返回定义
- 必须识别嵌套的对象和数组结构
- 必须识别引用（$ref）并解析引用的schema

示例：
```yaml
openapi: 3.0.0
info:
  title: 用户服务API
  version: 1.0.0
paths:
  /api/login:
    post:
      summary: 用户登录接口
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - username
                - password
              properties:
                username:
                  type: string
                  minLength: 3
                  maxLength: 20
                password:
                  type: string
                  minLength: 6
                  maxLength: 50
      responses:
        '200':
          description: 登录成功
          content:
            application/json:
              schema:
                type: object
                properties:
                  code:
                    type: integer
                  message:
                    type: string
                  data:
                    type: object
```

解析结果：
- 接口路径：/api/login
- HTTP方法：POST
- 接口描述：用户登录接口
- 入参：
  - username: string, 必填, 长度3-20
  - password: string, 必填, 长度6-50
- 返回：
  - 200: code(integer), message(string), data(object)


## [规则 3] 测试数据生成规范（优先级：关键）
# 根据接口定义，生成完备的测试数据

说明：
- 严格遵循接口定义描述生成测试数据
- 必须准确地覆盖接口文档中的入参类型
- 生成完备的测试数据，覆盖各种测试场景

### 3.1 指导原则

#### 3.1.1 严格遵循接口定义描述

- ✅ 仅针对接口描述中明确制定的测试目标字段属性生成测试数据
- ✅ 必须准确地覆盖接口文档中的入参类型，避免遗漏或错误
- ✅ 不生成接口定义中未描述的字段数据

#### 3.1.2 数据完备性约束

测试数据必须覆盖以下场景：

1. **类型测试**
   - ✅ 对于测试目标字段类型测试，请生成一组不是此类型的测试数据，即负面测试数据
   - 示例：字段类型为string，生成integer类型的测试数据

2. **长度测试**
   - ✅ 如果测试目标描述了字段长度范围在(m,n)，请为每一个长度范围在(m,n)范围内的字段生成以下测试数据：
     - 最大值n
     - 最小值m
     - 最大值n+1（边界外）
     - 最小值m-1（边界外）
   - 示例：minLength=3, maxLength=20 → 生成长度3、20、21、2的测试数据

3. **边界测试**
   - ✅ 如果测试目标描述了字段边界值，请为每一个边界值生成以下测试数据：
     - 边界值
     - 边界值+1
     - 边界值-1
   - 示例：minimum=10, maximum=100 → 生成10、11、9、100、101、99的测试数据

4. **必填项测试**
   - ✅ 如果测试目标字段描述了字段为必填项字段，请生成必填项为空字符串的测试数据
   - ✅ 如果测试目标字段描述了字段为必填项字段，请生成必填项为null的测试数据
   - 示例：required字段 → 生成空字符串和null的测试数据

5. **枚举测试**
   - ✅ 如果测试目标字段描述了字段的枚举值，请为每一个枚举值生成测试数据
   - 示例：enum: ["A", "B", "C"] → 生成A、B、C的测试数据
   - ✅ 如果字段有枚举值，请生成不在枚举值中的测试数据（负面测试）
   - 示例：enum: ["A", "B", "C"] → 生成"D"的测试数据

6. **空字符测试**
   - ✅ 如果测试目标字段描述了字段为非必填项字段，请生成非必填项为空字符串的测试数据
   - ✅ 如果测试目标字段描述了字段为非必填项字段，请生成非必填项为空格、制表符的测试数据
   - ✅ 如果测试目标字段描述了字段为非必填项字段，请生成非必填项为null的测试数据

7. **特殊字符测试**
   - ✅ 如果测试目标字段描述了字段为非必填项字段，请生成非必填项为特殊字符的测试数据
   - ✅ **转义字符测试**: 如反斜杠\、双引号"、单引号'、换行符\n、回车符\r、制表符\t、空格\s
   - ✅ **HTML特殊字符测试**: 如"<"、">"、"&"、"["、"]"
   - ✅ **Unicode字符测试**: 如中文字符、emoji等

8. **安全注入测试**
   - ✅ **SQL注入测试**: 生成SQL注入攻击字符串
     - 示例：`' OR '1'='1`, `'; DROP TABLE users; --`
   - ✅ **XSS注入测试**: 生成XSS攻击字符串
     - 示例：`<script>alert('XSS')</script>`, `<img src=x onerror=alert(1)>`
   - ✅ **命令注入测试**: 生成命令注入攻击字符串
     - 示例：`; ls -la`, `| cat /etc/passwd`

9. **格式测试**
   - ✅ 如果字段有format定义，生成符合格式和不符合格式的测试数据
   - 示例：format: email → 生成正确邮箱和错误邮箱格式
   - 示例：format: date → 生成正确日期和错误日期格式

10. **嵌套结构测试**
    - ✅ 如果字段类型为object，生成完整的对象结构测试数据
    - ✅ 如果字段类型为array，生成数组测试数据（空数组、单元素数组、多元素数组）
    - ✅ 如果字段有嵌套的required字段，生成嵌套必填项为空的测试数据

测试数据生成规则：

```markdown
对于每个接口的每个入参字段，生成以下测试数据：

1. 正常值测试数据（正例）
   - 符合所有约束条件的正常值

2. 类型测试数据（负例）
   - 类型不匹配的值

3. 长度测试数据（边界）
   - minLength、maxLength、minLength-1、maxLength+1

4. 数值范围测试数据（边界）
   - minimum、maximum、minimum-1、maximum+1

5. 必填项测试数据（负例）
   - 空字符串、null

6. 枚举测试数据
   - 每个枚举值（正例）
   - 不在枚举中的值（负例）

7. 格式测试数据
   - 符合格式的值（正例）
   - 不符合格式的值（负例）

8. 特殊字符测试数据
   - 转义字符、HTML字符、Unicode字符

9. 安全注入测试数据
   - SQL注入、XSS注入、命令注入

10. 嵌套结构测试数据
    - 完整的嵌套对象和数组
```

测试数据文件格式（YAML）：
```yaml
接口名称:
  测试用例编号:
    用例名称: "描述"
    用例类型: "正例/负例/边界"
    测试数据:
      字段1: "值1"
      字段2: "值2"
    预期结果:
      statusCode: 200
      response: {}
```

YAML格式要求：
- 使用2个空格缩进（不使用Tab）
- 字符串值使用引号包裹（如果包含特殊字符）
- 数字、布尔值不需要引号
- 列表使用 `-` 开头
- 字典使用 `key: value` 格式
- 保持层级结构清晰


## [规则 4] RobotFramework测试脚本生成规范（优先级：关键）
# 根据测试数据组合，生成最终的RobotFramework测试用例

说明：
- 参照提供的脚本示例生成RobotFramework测试脚本
- 严格遵循RobotFramework语法规范
- 确保脚本可执行且结构清晰

### 4.1 文件结构规范

#### 4.1.1 导入和设置部分

```robot
*** Settings ***
Documentation      测试套件描述
Metadata           元数据键    值
Library            库名    # 简单导入
Library            库名    WITH NAME    别名    # 带别名导入
Resource           ../resources/common.robot
Suite Setup        套件初始化关键字
Suite Teardown     套件清理关键字
Test Setup         测试用例初始化关键字
Test Teardown      测试用例清理关键字
Test Template      测试模板关键字
Test Timeout        超时时间
Default Tags       默认标签
Force Tags         强制标签
```

必需导入：
- `RequestsLibrary`：用于HTTP请求
- `Collections`：用于字典和列表操作
- `json`：用于JSON处理（通过Evaluate）

#### 4.1.2 变量定义部分

```robot
*** Variables ***
${SCALAR}          标量值
@{LIST}            列表值1    列表值2    列表值3
&{DICT}            key1=value1    key2=value2
```

必需变量：
- `${BASE_URL}`：接口基础URL
- `&{DEFAULT_HEADERS}`：默认请求头
- `${SUCCESS_STATUS_CODE}`：成功状态码（通常为200）
- `${EXPECTED_JSON_FIELD}`：预期响应字段名
- `${EXPECTED_JSON_VALUE_PE}`：正例预期值
- `${EXPECTED_JSON_VALUE_NE}`：负例预期值

#### 4.1.3 测试用例部分

```robot
*** Test Cases ***
测试用例名称
    [Documentation]
    ...    【用例编号】
    ...
            001
    ...    【用例名称】
    ...
    ...    【用例等级】
    ...
    ...    【预置条件】
    ...
    ...    【测试步骤】
    ...     1、边界值校验，参数：loginpwd，取值：${loginpwd_MAX_LENGTH}
    ...    【预期结果】
    ...    【作者】
    [Tags]    Auto    Boundary
    [Setup]           初始化关键字
    [Teardown]        清理关键字
    [Template]        模板关键字
    [Timeout]         超时时间
    # 测试步骤
    关键字    参数1    参数2
    ${variable}=    关键字    参数
    FOR    ${item}    IN    @{LIST}
        关键字    ${item}
    END
```

测试用例命名规范：
- 格式：`【正例/负例/边界】{编号}_{字段名}_{测试类型}`
- 示例：`【正例】001_loginpwd_max_length`、`【负例】002_username_empty`

#### 4.1.4 用户关键字部分

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

### 4.2 语法细节规范

#### 4.2.1 缩进和空格

- ✅ **正确的缩进**: 使用4个空格缩进
- ❌ **错误的缩进**: 只有2个空格、没有缩进、使用Tab

#### 4.2.2 变量使用规范

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

#### 4.2.3 控制流规范

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
    FOR    ${browser}    IN    @{BROWSERS}
        Open Browser    ${URL}    ${browser}
        Perform Test
        Close Browser
    END
```

### 4.3 测试脚本生成模板

标准测试用例模板：
```robot
*** Settings ***
Documentation     接口自动化测试用例。
Suite Setup       
Suite Teardown    
Force Tags        TDMAuto
Library           RequestsLibrary
Library           Collections
Resource          ../../config.robot

*** Variables ***
${BASE_URL}       https://127.0.0.1:443
&{DEFAULT_HEADERS}    Content-Type=application/json
${SUCCESS_STATUS_CODE}    200
${EXPECTED_JSON_FIELD}    returncode
${EXPECTED_JSON_VALUE_PE}    0
${EXPECTED_JSON_VALUE_NE}    1

*** Test Cases ***
【正例】001_{字段名}_{测试类型}
    [Documentation]
    ...    【用例编号】001
    ...    【用例名称】{用例名称}
    ...    【用例等级】
    ...    【预置条件】
    ...    【测试步骤】
    ...    {测试步骤描述}
    ...    【预期结果】
    ...    {预期结果描述}
    ...    【作者】
    [Tags]    Auto    {标签}
    [Setup]    
    # 将本次入参透出，方便在步骤中复用
    ${param1}=    Set Variable    ${value1}
    ${param2}=    Set Variable    ${value2}
    # 拼装接口请求
    #    参数转化
    ${headers}    Evaluate    json.loads('''{}''')    modules=json
    ${len}    Get Length    ${headers} 
    ${headers}=    Set Variable If    ${len} == 0    ${DEFAULT_HEADERS}    ${headers}
    ${params}=    Evaluate    json.loads('''{"param1":"${value1}","param2":"${value2}"}''')    modules=json
    ${body}    Evaluate    json.loads('''{}''')    modules=json
    ${url}=    Catenate    SEPARATOR=    ${BASE_URL}    {接口路径}
    #    消息发送
    ${response}=    RequestsLibrary.{HTTP方法}    ${url}    headers=${headers}    params=${params}    json=${body}    verify=${False}    expected_status=${SUCCESS_STATUS_CODE}
    #    消息校验
    ${field_value}=    Get From Dictionary    ${response.json()}    ${EXPECTED_JSON_FIELD}
    Run Keyword If    "{用例类型}"=="正例"    Should Be Equal As Strings    ${field_value}    ${EXPECTED_JSON_VALUE_PE}    Expected field ${EXPECTED_JSON_FIELD} to have value ${EXPECTED_JSON_VALUE_PE}, but got ${field_value}    ELSE    Should Be Equal As Strings    ${field_value}    ${EXPECTED_JSON_VALUE_NE}    Expected field ${EXPECTED_JSON_FIELD} to have value ${EXPECTED_JSON_VALUE_NE}, but got ${field_value}
    [Teardown]    
```

### 4.4 脚本生成规则

1. **从测试数据生成测试用例**
   - 每个测试数据对应一个测试用例
   - 测试用例编号与测试数据编号对应
   - 测试用例名称清晰描述测试场景

2. **参数处理**
   - 路径参数：拼接到URL中
   - 查询参数：使用params参数
   - 请求体参数：使用json参数
   - 请求头：使用headers参数

3. **HTTP方法映射**
   - GET → RequestsLibrary.GET
   - POST → RequestsLibrary.POST
   - PUT → RequestsLibrary.PUT
   - DELETE → RequestsLibrary.DELETE

4. **响应验证**
   - 验证HTTP状态码
   - 验证响应JSON结构
   - 验证关键字段值
   - 根据用例类型（正例/负例）使用不同的预期值

5. **错误处理**
   - 使用expected_status处理预期状态码
   - 对于负例，可能需要验证错误响应


## [规则 5] 测试数据到脚本的映射规则（优先级：高）
# 将测试数据准确映射为RobotFramework测试脚本

说明：
- 从测试数据中提取信息，映射到RobotFramework脚本格式
- 保持信息的完整性和准确性
- 确保脚本可执行

映射规则：

1. **测试用例编号**
   - 测试数据编号 → 测试用例编号
   - 格式：`【正例/负例/边界】{编号}_{字段名}_{测试类型}`

2. **测试用例名称**
   - 测试数据用例名称 → 测试用例名称

3. **测试数据映射**
   - 测试数据字段 → RobotFramework变量
   - 使用Set Variable设置变量值

4. **请求参数映射**
   - 路径参数 → URL路径
   - 查询参数 → params字典
   - 请求体参数 → json字典
   - 请求头 → headers字典

5. **响应验证映射**
   - 预期结果 → 响应验证逻辑
   - 根据用例类型选择验证方式

示例映射：
```yaml
输入测试数据（YAML格式）：
用户登录接口:
  "001":
    用例名称: "001_loginpwd_max_length"
    用例类型: "边界"
    测试数据:
      username: "testuser"
      password: "123456789012345678901234567890"
    预期结果:
      statusCode: 200
      returncode: 0

输出RobotFramework脚本：
```robot
【边界】001_loginpwd_max_length
    [Documentation]
    ...    【用例编号】001
    ...    【用例名称】001_loginpwd_max_length
    ...    【用例等级】
    ...    【预置条件】
    ...    【测试步骤】
    ...    边界值校验，参数：password，取值：最大值
    ...    【预期结果】
    ...    【作者】
    [Tags]    Auto    Boundary
    [Setup]    
    ${username}=    Set Variable    testuser
    ${password}=    Set Variable    123456789012345678901234567890
    ${headers}    Evaluate    json.loads('''{}''')    modules=json
    ${len}    Get Length    ${headers} 
    ${headers}=    Set Variable If    ${len} == 0    ${DEFAULT_HEADERS}    ${headers}
    ${params}=    Evaluate    json.loads('''{}''')    modules=json
    ${body}    Evaluate    json.loads('''{"username":"testuser","password":"123456789012345678901234567890"}''')    modules=json
    ${url}=    Catenate    SEPARATOR=    ${BASE_URL}    /api/login
    ${response}=    RequestsLibrary.POST    ${url}    headers=${headers}    params=${params}    json=${body}    verify=${False}    expected_status=${SUCCESS_STATUS_CODE}
    ${field_value}=    Get From Dictionary    ${response.json()}    ${EXPECTED_JSON_FIELD}
    Run Keyword If    "边界"=="正例"    Should Be Equal As Strings    ${field_value}    ${EXPECTED_JSON_VALUE_PE}    Expected field ${EXPECTED_JSON_FIELD} to have value ${EXPECTED_JSON_VALUE_PE}, but got ${field_value}    ELSE    Should Be Equal As Strings    ${field_value}    ${EXPECTED_JSON_VALUE_NE}    Expected field ${EXPECTED_JSON_FIELD} to have value ${EXPECTED_JSON_VALUE_NE}, but got ${field_value}
    [Teardown]    
```


## [规则 6] 文件生成规则（优先级：中）
# 生成标准化的测试数据和测试脚本文件

说明：
- 测试数据输出文件：`接口测试数据.yaml`（必须使用YAML格式）
- 测试脚本输出文件：`接口测试脚本.robot`
- 文件结构清晰，便于维护

测试数据文件结构（YAML格式）：
```yaml
接口名称1:
  "001":
    用例名称: "001_字段名_测试类型"
    用例类型: "正例"
    测试数据:
      字段1: "值1"
      字段2: "值2"
    预期结果:
      statusCode: 200
      returncode: 0
      message: "成功"
  "002":
    用例名称: "002_字段名_测试类型"
    用例类型: "负例"
    测试数据:
      字段1: ""
      字段2: "值2"
    预期结果:
      statusCode: 400
      returncode: 1
      message: "参数错误"

接口名称2:
  "001":
    用例名称: "001_字段名_测试类型"
    用例类型: "边界"
    测试数据:
      字段1: "边界值"
    预期结果:
      statusCode: 200
```

测试脚本文件结构：
```robot
*** Settings ***
...

*** Variables ***
...

*** Test Cases ***
# 接口1的测试用例
【正例】001_接口1_字段1_正常值
...

【负例】002_接口1_字段1_类型错误
...

# 接口2的测试用例
【正例】001_接口2_字段1_正常值
...
```

文件命名规则：
- 测试数据：`接口测试数据.yaml`（必须使用YAML格式）或按接口拆分：`{接口名称}-测试数据.yaml`
- 测试脚本：`接口测试脚本.robot` 或按接口拆分：`{接口名称}-测试脚本.robot`


## [规则 7] 验证和完整性检查（优先级：高）
# 生成后验证测试数据和测试脚本的完整性

说明：
- 生成测试数据和测试脚本后，必须进行完整性验证
- 确保所有接口都有对应的测试数据和测试脚本
- 确保格式正确且可执行

验证项：
- [ ] 是否所有接口都生成了测试数据
- [ ] 是否所有测试数据都覆盖了完备性约束（类型、长度、边界、必填、枚举等）
- [ ] 是否所有测试数据都生成了对应的测试用例
- [ ] 测试脚本语法是否正确（RobotFramework语法）
- [ ] 测试脚本是否可执行（变量定义、关键字调用等）
- [ ] YAML格式是否正确（缩进、语法等）
- [ ] 文件是否成功写入到指定位置

验证失败处理：
- 如果验证失败，输出详细的错误信息
- 列出缺失或错误的测试数据和测试用例
- 提供修复建议


## [规则 8] 输出反馈和统计（优先级：低）
# 提供清晰的生成结果反馈

说明：
- 生成完成后，提供统计信息
- 告知用户生成的测试数据和测试脚本数量和文件位置
- 提供测试覆盖率信息

反馈格式：
- 成功：`成功生成 [N] 个接口的测试数据，共 [M] 个测试用例，已保存到 接口测试数据.yaml 和 接口测试脚本.robot`
- 统计：`接口数量：[X]，测试用例总数：[Y]，正例：[A]个，负例：[B]个，边界：[C]个`
- 警告：`检测到 [问题描述]，已处理：[处理方式]`
- 错误：`生成失败：[具体错误原因]`

统计信息：
- 输入接口数量
- 生成的测试用例数量
- 测试用例类型分布（正例、负例、边界）
- 测试数据覆盖情况（类型、长度、边界等）
- 文件位置和格式


# ============================================
# 摘要 - 关键规则（最高优先级）
# ============================================

1. ✅ [规则 1] 从接口规格.md读取接口定义（关键）
2. ✅ [规则 2] OpenAPI V3.0接口定义解析（关键）
3. ✅ [规则 3] 测试数据生成规范（关键）
4. ✅ [规则 4] RobotFramework测试脚本生成规范（关键）
5. ✅ [规则 5] 测试数据到脚本的映射规则（高）
6. ✅ [规则 6] 文件生成规则（中）
7. ✅ [规则 7] 验证和完整性检查（高）
8. ✅ [规则 8] 输出反馈和统计（低）


# ============================================
# 使用示例
# ============================================

示例 1：从接口规格.md生成测试数据和测试脚本
```
用户输入：
"请根据接口规格.md生成接口测试数据和RobotFramework测试脚本"

AI 处理：
1. 读取 接口规格.md 文件
2. 解析"组件接口说明"章节
3. 识别OpenAPI V3.0 YAML格式的接口定义
4. 解析每个接口：
   - 接口概述
   - 入参定义
   - 返回定义
5. 为每个接口的每个字段生成测试数据：
   - 正常值（正例）
   - 类型错误（负例）
   - 长度边界（边界）
   - 必填项为空（负例）
   - 枚举值（正例和负例）
   - 特殊字符（负例）
   - 安全注入（负例）
6. 生成测试数据文件：接口测试数据.yaml（YAML格式）
7. 为每个测试数据生成RobotFramework测试用例
8. 生成测试脚本文件：接口测试脚本.robot
9. 验证完整性
10. 输出："成功生成 3 个接口的测试数据，共 45 个测试用例，已保存到 接口测试数据.yaml 和 接口测试脚本.robot"
```

示例 2：测试数据生成示例
```
输入接口定义（OpenAPI V3.0）：
paths:
  /api/login:
    post:
      requestBody:
        content:
          application/json:
            schema:
              type: object
              required:
                - username
                - password
              properties:
                username:
                  type: string
                  minLength: 3
                  maxLength: 20
                password:
                  type: string
                  minLength: 6
                  maxLength: 50

生成测试数据（YAML格式）：
```yaml
用户登录接口:
  "001":
    用例名称: "001_username_normal"
    用例类型: "正例"
    测试数据:
      username: "testuser"
      password: "password123"
    预期结果:
      statusCode: 200
      returncode: 0
  "002":
    用例名称: "002_username_min_length"
    用例类型: "边界"
    测试数据:
      username: "tes"
      password: "password123"
    预期结果:
      statusCode: 200
      returncode: 0
  "003":
    用例名称: "003_username_max_length"
    用例类型: "边界"
    测试数据:
      username: "testusertestusertest"
      password: "password123"
    预期结果:
      statusCode: 200
      returncode: 0
  "004":
    用例名称: "004_username_empty"
    用例类型: "负例"
    测试数据:
      username: ""
      password: "password123"
    预期结果:
      statusCode: 400
      returncode: 1
  "005":
    用例名称: "005_username_type_error"
    用例类型: "负例"
    测试数据:
      username: 123
      password: "password123"
    预期结果:
      statusCode: 400
      returncode: 1
```
```


# ============================================
# 版本历史
# ============================================
# v1.0 (2025-01-XX) - 初始版本，定义接口测试数据和RobotFramework测试脚本生成规范
# ============================================
