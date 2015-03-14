
### 2.1.1   文件与目录
 
测试例层次结构如下：

 - 测试例文件中创建测试例
 - 测试例文件会自动产生测试集，测试集中包含了文件中的测试例
 - 测试例文件所在目录形成高级测试集，该目录包含了其中测试文件产生的测试集
 - 测试集目录也可含有其他的测试集目录
 - 测试集目录可以拥有一个特殊的初始化文件
 
除去上述，还有：

 - 测试库，包含最低级别的关键字
 - 资源文件，包含变量及高级别的用户关键字
 - 变量文件，在创建变量方面提供更大的灵活性
 

### 2.1.2   文件格式支持
 
Robot Framework测试数据使用表格样式定义，可使用HTML、TSV、纯文本或者reST格式。下面将详细介绍上述格式。具体格式使用有赖于上下文，如无特殊需要推荐使用纯文本。
 
Robot Framwork根据文件后缀名识别测试数据。

为了便于掌握测试的编写，Robot提供了针对HTML、TSV格式的不同的测试模板。 

> **注意**
> 从2.7.6版本起Robot支持.robot

**纯文本**
 
由于容易编辑及良好的版本兼容性，纯文本格式是robot使用最多的格式

从技术角度讲，纯文本与TSV格式类似，但使用不同分隔符。TSV使用"tab"，纯文本可以使用2个或2个以上空格、亦或者管道字符" | "进行分割。 

类似TSV，表命名前需要使用1个或多个"\*"，robot会忽略多余的"\*"与空格，例如，\*\*\* Settings与\*Settings的含义是一致的。此外，robot也会忽略第一张表前的内容。

纯文本中"tab"为2个空格长度，允许使用单个"tab"作为分隔符。

**空格分隔样式**
 
使用空格分隔时，应至少为2个空格，robot不对空格的上限数量不作限制，因此可以使用多余的空格实现语句的对齐。

```
*** Settings ***
Library       OperatingSystem

*** Variables ***
${MESSAGE}    Hello, world!

*** Test Cases ***
My Test
    [Documentation]    Example test
    Log    ${MESSAGE}
    My Keyword    /tmp

Another Test
    Should Be Equal    ${MESSAGE}    Hello, world!

*** Keywords ***
My Keyword
    [Arguments]    ${path}
    Directory Should Exist    ${path}

```

使用空格分隔时，空缺单元需使用${EMPTY}或"\"表示。

> **提示**
> 建议使用4个空格分隔关键字与参数

**管道及空格分隔样式**
 
使用空格分隔容易出现视觉混淆，尤其当多个参数包含空格时。使用" | "作为分隔就能够较好地起到消除混淆的作用。

```
| *Setting*  |     *Value*     |
| Library    | OperatingSystem |

| *Variable* |     *Value*     |
| ${MESSAGE} | Hello, world!   |

| *Test Case*  | *Action*        | *Argument*   |
| My Test      | [Documentation] | Example test |
|              | Log             | ${MESSAGE}   |
|              | My Keyword      | /tmp         |
| Another Test | Should Be Equal | ${MESSAGE}   | Hello, world!

| *Keyword*  |
| My Keyword | [Arguments] | ${path}
|            | Directory Should Exist | ${path}
```

行的开头必须有" | "，但行尾可以选择不使用，使用" | "时可以不对齐。

使用" | "分隔时，当测试数据中包含空格时则需要使用"\"。

```
| *** Test Cases *** |                 |                 |                      |
| Escaping Pipe      | ${file count} = | Execute Command | ls -1 *.txt \| wc -l |
|                    | Should Be Equal | ${file count}   | 42                   |
```

**编辑与编码**

RIDE支持纯文本格式。
 
纯文本格式使用UTF-8编码。 

**可识别后缀**
 
从2.7.6版本起，除.txt外可以使用.robot保存纯文本。

### 2.1.3   测试数据表
 
测试数据如下有四种表类型。测试数据表按表的一个单元识别，下表中最后列中示出了表名的别名。

| 表名        | 用途   |  别名  |
| --------   | :-----  | :----  |
| Setting table     | 1) 导入测试库、资源文件和变量文件 |   Setting，Settings，Metadata     |
|                   | 2）定义测试集和测试用例的元数据   |
| Variable table        |   定义测试数据中可以使用的变量   |   Variable，Variables   |
| Test case table        |    从keyword中创建测试例    |  Test Case，Test Cases  |
| Keyword table     | 从现有低级关键词中创建用户关键词 |  Keyword，Keywords，User Keyword，User Keywords  |

### 2.2.1   测试例语法
 

**基本语法**
 
测试例由关键字构建，关键字由测试库或资源文件导入，亦或通过在keyword表中创建。
 
表的第一列包含测试例名，测试例由此列开始至下一测试例或表尾结束。
 
表的第二列通常为关键字，但也可以是由关键字返回值设置的变量，上述两种情况下，关键字后的列都是关键字的参数

| Test Case  | Action     |  Argument  |  Argument  |
| --------   | :--------  | :----  | :----  |
| Valid Login  | Open Login Page |        |        |
|              | Input Name   |   demo     |        |
|              | Input Password   |   mode     |        |
|              | Submit Credentials   |        |        |
|              | Welcome Page Should Be Open   |        |        |
| Setting Variables        |   Do Something   |   first Argument   |   second Argument   |
|         |   ${value}=   |   Got Some Value   |      |
|         |   Should Be Equal   |   ${value}   |  Excepted value    |

**测试例表中的设置**
 
测试例的设置关键字位于第二列，后跟设置值，设置名由"[]"修饰，如下示出可用的设置。

*[Documentation]* 用于测试用例的说明

*[Tags]* 用于标记测试用例

*[Setup]*，*[Teardown]* 测试的安装和清除，也可以使用*[Precondition]*和*[Postcondition]*

*[Template]* 指定模板关键字 

*[Timeout]* 用于设置测试例超时

| Test Case  | Action     |  Argument  |  Argument  |
| --------   | :--------  | :----  | :----  |
| Test With Settings  | [Documentation]] |   Another dummy test     |        |
|              | [Tags]]   |   dummy     |    owner-johndoe    |
|              | Log   |   Hello,world!     |        |

**Test case related settings in the Setting table**

Setting表具有下列测试例设置，主要是测试例的缺省值。

*Force Tags*, *Default Tags*
强制标签及缺省标签
*Test Setup*, *Test Teardown*
测试缺省建立与拆除，与*Test Precondition*及*Test Postcondition*含义相同
*Test Template* 
关键字缺省模板
*Test Timeout* 
测试例缺省超时值

### 2.2.2   参数使用

关键字接收0个或多个参数，某些参数具有缺省值。关键字接收何种参数取决于关键字的实现，了解这些信息的方法是检索关键字文档。本章中关键字文档通过libdoc工具产生，类似地其他同样的文档工具，如javadoc，也可以做到这一点。

**强制参数**

大多数关键字具有若干个参数，这些参数都必须给定，参数可以任意命名，但应表达参数具有的意义，参数数量必须与文档中指明的一致，数量不一致将导致错误。

下列测试使用关键字Create Directory和Copy File，它们的参数分别是path以及source、destination。最后一个关键字No Operation，不带参数。

| Test Case  | Action     |  Argument  |  Argument  |
| --------   | :--------  | :----  | :----  |
| Example  | Create Directory |   ${TEMPDIR}/stuff     |        |
|              | Copy File  |   ${CURDIR}/file.txt     |    ${TEMPDIR}/stuff    |
|              | No Operation   |        |        |

**缺省值**

参数通常具有缺省值，在文档中缺省值通常用"="与参数分割。

下例示出如何使用缺省值，Create File具有path，content=，encoding=UTF-8参数，如果在使用该关键字时未带参数或者参数数量错误，则关键字将不能正常工作。

| Test Case | Action    | Argument | Argument | Argument |
| --------- | :-------- | :------- | :------- | :------- |
| Example   | Create File | ${TEMPDIR}/empty.txt |  |  |
|           | Create File | ${CURDIR}/file.txt | Hyvä esimerkki |  |    
|           | Create File | ${TEMPDIR}/iso-8859-1.txt | Hyvä esimerkki | ISO-8859-1 |

**不定长参数**

某些关键字可以接收任意数量的参数，可变参数必须是最后一个参数，文档中通常以"\*"来表示，如 \*varargs。

下例中，Remove Files与Join Paths各自具有参数\*paths和base，*parts。前者可以使用任意数量参数，而后者则必须至少包含一个参数。 

| Test Case | Action    | Argument | Argument | Argument |
| --------- | :-------- | :------- | :------- | :------- |
| Example   | Remove Files | ${TEMPDIR}/f1.txt | ${TEMPDIR}/f2.txt | ${TEMPDIR}/f3.txt |
|           | @{paths}= | Join Paths | ${TEMPDIR} | f1.txt |    
|           | ... | f2.txt | f3.txt | f4.txt |

**指定参数**

指定参数语法使得具有缺省值的参数使用更加灵活，同时能精确地标注某一个参数值。从技术上讲，指定参数类似python中的关键字参数。

**基本语法**

指定参数是通过在给定的值前面加上参数名来实现的，即arg=value。当多个参数具有缺省值时尤其有用，因为可以指定几个参数而其他参数继续使用缺省值。例如，如果关键字接收参数arg1=a，arg2=b，arg3=c，可以用arg3=override仅重写arg3的值，而其他两个参数仍然使用缺省值。

指定参数语法对大小写及空格敏感。

当在用户关键字中使用指定参数时，则参数名无需使用${}修饰，例如，用户关键字参数为${arg1}=first，${arg2}=second，则应使用arg2=override。

在指定参数后使用普通位置参数，例如，| Keyword | arg=value | positional |，不会起作用。

> 注意
> Robot Framework 2.8版本前无法对无缺省值参数指定参数。

**利用变量指定参数**

指定参数值时可以使用变量，此时，参数值将传递给关键字as-is，因而不单是字符串，也可以使用对象作为参数值。例如，关键字参数arg=${object}将${object}变量传递给关键字，而不用转化为字符串。

当使用的变量中包含name=value，并不会触发指定参数，因为指定参数语法要求参数名写入关键字调用中。这一点在将关键字封装进其他关键字中尤为重要，例如，关键字携带了可变参数@{args}，并将传入其他关键字，则参数值不会认为发生了指定。

| Test Case | Action    | Argument | Argument |
| --------- | :-------- | :------- | :------- |
| Example   | wrapper | shell=True | # This will not come as a named argument to Start process | 

| Keyword | Action | Argument | Argument | Argument |    
| --------- | :-------- | :------- | :------- |
| Wrapper | [Arguments] | @{args} |  |  |
|  | Start process | MyProcess | @{args} | # named arguments are not recognized from inside @{args} |


**指定参数语法转义**

只有当等号前的参数与关键字中参数匹配，指定参数语法才能发挥作用，但如果有位置参数的字面值为foo=quux，且存在一个参数名为foo时就有可能发生错误。

为了应对这种情况，使用"\"转义，如foo=\quux。需要注意的是当不存在名为foo的参数时，可以不使用转义。

大多数用户关键字和测试类库都支持参数指定，唯一的例外是Java中使用静态库API。可以查阅文档获得是否支持指定参数的信息。

**指定参数范例**

下例示出使用指定参数

| Setting | Value    | Value | Value |
| --------- | :-------- | :------- | :------- |
| Library   | Telnet | prompt=$ | default_log_level=DEBUG | 

| Test Case | Action | Argument | Argument | Argument |    
| --------- | :-------- | :------- | :------- |
| Example | Open connection | 10.0.0.42 | port=${PORT} | alias=example |
|  | List files | options=-lh |  |  |
|  | List files | path=/tmp | options=-lh |  |

| Keyword | Action | Argument | Argument | Argument |    
| --------- | :-------- | :------- | :------- |
| List files | [Arguments] | ${path}=. | ${options}= |  |
|  | Execute command | ls ${options} ${path} |  |  |

### 2.2.4   测试例名称与文档

用例名称直接来源与用例表，用例在测试集中具有唯一的名称。也可以用automatic variable的${Test_Name}来指明其测试名称。

[Documentation]设置允许使用自由文档说明测试用例，文本会在命令行、测试日志及报告中显示。

如果文档较长，可以划分为多个单元格，并且可以使用简单的HTML格式与变量使得文本更加生动。

用例名表意清晰非常重要，可以省去不必要的文档。对于元数据，如环境和用户信息应该使用标签(tags)来进行定义更加妥当。
