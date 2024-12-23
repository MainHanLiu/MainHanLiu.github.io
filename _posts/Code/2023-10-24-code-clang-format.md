---
title: "Doxygen与代码格式化工具Clang-Format"
description: 文件头/函数头/代码 格式化
date: 2023-10-24 10:00:00 +0800
categories: [Code, Program style]
tags: [Code, Program style, Doxygen, Clang-Format]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## Doxygen 文件头和函数头自动注释  

**VS插件：Doxygen Documentation Generator**

- 配置setting.json
```json
{
    // Doxygen documentation generator set
    "doxdocgen.c.triggerSequence": "/", // 触发自动注释的生成
    "doxdocgen.c.commentPrefix": " * ", // 注释行的前缀
    "doxdocgen.c.firstLine": "/**", // 注释行的首行
    "doxdocgen.c.lastLine": "*/", // 注释行的尾行
    // Smart text snippet for factory methods/functions.
    "doxdocgen.c.factoryMethodText": "Create a {name} object",
    // Smart text snippet for getters.
    "doxdocgen.c.getterText": "Get the {name} object",
    // Smart text snippet for setters.
    "doxdocgen.c.setterText": "Set the {name} object",
    // Smart text snippet for constructors.
    "doxdocgen.cpp.ctorText": "Construct a new {name} object",
    // Smart text snippet for destructors.
    "doxdocgen.cpp.dtorText": "Destroy the {name} object",
    // The template of the template parameter Doxygen line(s) that are generated. If empty it won't get generated at all.
    "doxdocgen.cpp.tparamTemplate": "@tparam {param} ",
    // 文件注释的组成及其排序
    "doxdocgen.file.fileOrder": [
        "custom",
        "file", // @file
        "brief", // @brief 简介
        "author", // 作者
        "version", // 版本
        "date", // 日期
        "empty", // 空行
        "copyright", // 版权
        "empty",
        "custom" // 自定义
    ],
    // 下面时设置上面标签tag的具体信息
    "doxdocgen.file.fileTemplate": "@file {name}",
    "doxdocgen.file.versionTag": "@version 0.0.1",
    "doxdocgen.generic.authorEmail": "xx@xx.com",
    "doxdocgen.generic.authorName": "authorName",
    "doxdocgen.generic.authorTag": "@author {author} ({email})",
    // // 日期格式与模板
    // "doxdocgen.generic.dateFormat": "YYYY-MM-DD",
    // "doxdocgen.generic.dateTemplate": "@date {date}",
    // 文件注释：版权信息模板
    "doxdocgen.file.copyrightTag": [
        "@copyright Copyright (c) {2023}  ****** Company"
    ],
    "doxdocgen.file.customTag": [
        "************************************************************************",
    ],
    // 根据自动生成的注释模板（目前主要体现在函数注释上）
    "doxdocgen.generic.order": [
        "brief",
        "tparam",
        "param",
        "return",
        // "author",
        // "date"
    ],
    "doxdocgen.generic.paramTemplate": "@param  [in] {indent:8}{param}{indent:8}",
    "doxdocgen.generic.returnTemplate": "@return {indent:8}{type} ",
    "doxdocgen.generic.splitCasingSmartText": true,
    "doxdocgen.generic.includeTypeAtReturn": true, // return 中包含类型信息
    "doxdocgen.generic.boolReturnsTrueFalse": false, // bool 返回值拆分成 true 和 false 两种情况
    "doxdocgen.generic.linesToGet": 20, // 回车后最多向下多少行去找函数声明
    "doxdocgen.generic.useGitUserName": false, // {author} 是都根据 git config --get user.name 替换
    "doxdocgen.generic.useGitUserEmail": false,
}
```

## Clang-Format

添加C/C++插件，会自动潜在安装clang-format，可不需额外安装  

>若配置好却提示格式化失败  
>原因：.clang-format文件一创建就被立即加密，导致读取文件失败  
>解决：新建文件后缀.log，并更改vscode读取文件的名称

### clang-format 批量格式化插件：Format Files
- [# 使用vs code 和 clang-format 批量格式化文件夹下的文件](https://blog.csdn.net/koukouwuwu/article/details/111879677)  

### VS Code 配置
- "editor.defaultFormatter": `"ms-vscode.cpptools"`,  
- "C_Cpp.formatting": `"clangFormat"`,  
- clang-format.exe的绝对路径  
`"C_Cpp.clang_format_path":C:\Users\admin\.vscode\extensions\ms-vscode.cpptools-1.17.5-win32-x64\LLVM\bin\clang-format.exe`  
- .clang-format配置文件路径  
若为file，则调用在workspace中的 .clang-format；   
若指定 file路径，则按照指定路径来执行。  
`"C_Cpp.clang_format_style":file:D:\Learn/.clang-format.log`
>Clang_format_style会先从根目录读取.clong-format配置文件，如果没有读取到，则会使用Clang_format_fallback style。  
>不建议使用`file:C:\Users\admin\.vscode\extensions\ms-vscode.cpptools-1.17.5-win32-x64\LLVM\bin/.clang-format.log`，当插件自动更新时文件夹内容会被删除,需要重新配置。  

### 格式化规则设置文件
配置文件模板  
- [# windows端clang-format自定义格式化文件](https://blog.csdn.net/weixin_43717839/article/details/131548473?spm=1001.2014.3001.5502)  
- [# Linux端clang-format自定义格式化文件](https://blog.csdn.net/weixin_43717839/article/details/131556063?spm=1001.2014.3001.5502)  

**.clang-format.log**
```yaml
---
# 语言: None, Cpp, Java, JavaScript, ObjC, Proto, TableGen, TextProto
Language:        Cpp
BasedOnStyle:  LLVM
# 访问说明符(public、private等)的额外缩进或缩出
AccessModifierOffset: -4
# 开括号(开圆括号、开尖括号、开方括号)后的对齐: Align, DontAlign, AlwaysBreak(总是在开括号后换行)
AlignAfterOpenBracket: Align
# 当数组的列数相等时，会对齐每行的文本
AlignArrayOfStructures: Left
# 连续赋值时，对齐所有等号
# AlignConsecutiveAssignments: AcrossComments
AlignConsecutiveAssignments:
  Enabled: true
  AcrossEmptyLines: false
  AcrossComments: true
  AlignCompound: true #混合运算符+=会对齐于=（仅限于连续赋值)
  PadOperators: true  #混合运算符填补对齐到所有左值的最右边边界。(仅限于连续赋值)
# 对齐连续位字段，主要用于结构体
AlignConsecutiveBitFields: AcrossEmptyLinesAndComments
# 连续声明时，对齐所有声明的变量名
AlignConsecutiveDeclarations: AcrossComments
# 对齐连续宏定义
AlignConsecutiveMacros: AcrossEmptyLinesAndComments
# 对齐逃脱换行(使用反斜杠换行)的反斜杠 \
AlignEscapedNewlines: Right
# 竖直对齐二元表达式或者三元表达式操作数
AlignOperands:   Align
# 对齐连续的尾随的注释
AlignTrailingComments:
  Kind: Always
  OverEmptyLines: 1

# 如果函数调用或花括号的初始化列表不能放在一行中，允许将所有参数放到下一行
AllowAllArgumentsOnNextLine : false
# 允许函数声明的所有参数在放在下一行
AllowAllParametersOfDeclarationOnNextLine: false

# 允许短的语法块放在单行上
AllowShortBlocksOnASingleLine: Never
# 允许短的case标签放在同一行
AllowShortCaseLabelsOnASingleLine: false
# 允许短枚举在单行上
AllowShortEnumsOnASingleLine: false
# 允许短的函数放在同一行
AllowShortFunctionsOnASingleLine: None
# 允许短的if语句保持在同一行
AllowShortIfStatementsOnASingleLine : Never
# 允许短的匿名函数放在同一行,依赖于值，auto lambda []() { return 0; } 可以放在一行上
AllowShortLambdasOnASingleLine: All
# 允许合并短循环到单行上。如果为 true ，则 while (true) continue; 可以放在一行上
AllowShortLoopsOnASingleLine : false
# 函数声明的返回类型换行风格。总是在返回类型后换行
AlwaysBreakAfterReturnType: None
# 多行字符串断行。总是在多行string字面量前换行
AlwaysBreakBeforeMultilineStrings: false
# 模板声明断行。总是在template声明后换行
AlwaysBreakTemplateDeclarations: MultiLine
# AttributeMacros 属性宏
# 应该被解释为属性/限定符而不是标识符的字符串向量。这对于语言扩展或静态分析器注释非常有用。
#一个例子
#x = (char *__capability)&y;
#int function(void) __ununsed;
#void only_writes_to_buffer(char *__output buffer);
#.clang-format配置文件，可以被配置成下面这样：
AttributeMacros: 
  - __capability
  - __output
  - __ununsed

# 装箱变量；false表示函数实参要么都在同一行，要么都各自一行
BinPackArguments: true
# 装箱声明参数； false表示所有形参要么都在同一行，要么都各自一行
BinPackParameters: true

####

# 位段列的空白风格；在:号两边都增加一个空白
BitFieldColonSpacing : Both

# 大括号换行风格；在大括号前换行: Attach(始终将大括号附加到周围的上下文)
BreakBeforeBraces: Custom #Allman
# 大括号换行，只有当BreakBeforeBraces设置为Custom时才有效
BraceWrapping:
  AfterCaseLabel:  true
  AfterClass:      false
  AfterControlStatement: Always
  AfterEnum:       true
  AfterFunction:   true
  AfterNamespace:  true
  AfterObjCDeclaration: false
  AfterStruct:     true
  AfterUnion:      true
  AfterExternBlock: false
  BeforeCatch:     false
  BeforeElse:      true
  BeforeLambdaBody: false
  BeforeWhile:     false
  IndentBraces:    false #括号是否包含缩进
  SplitEmptyFunction: false
  SplitEmptyRecord: false
  SplitEmptyNamespace: false

# 在修饰器之后断行。在java字段的注释后换行
BreakAfterJavaFieldAnnotations: false

# 二元操作符断行。在二元运算符前换行
BreakBeforeBinaryOperators: All
# 概念声明断行风格。在concept前换行
BreakBeforeConceptDeclarations: true
# 在三元运算符前换行
BreakBeforeTernaryOperators: true

# cpp
# 构造初始化断行风格。构造函数初始值设定项换行样式
BreakConstructorInitializers: AfterColon
# 继承列表样式
BreakInheritanceList: AfterColon

# 字符串常量断行。允许在格式化时中断字符串文字
BreakStringLiterals : false

# 每行字符的限制，0表示没有限制
ColumnLimit:     0

# 描述具有特殊意义的注释的正则表达式，它不应该被分割为多行或以其它方式改变 (正则 不进行格式化的注释行)
CommentPragmas:  '^ FOOBAR pragma:'
# // 举例
# // Will leave the following line unaffected
# #include <vector> // FOOBAR pragma: keep

# 紧凑命名空间。在新行上声明每个命名空间
CompactNamespaces: false
# 构造函数的初始化列表的缩进宽度
ConstructorInitializerIndentWidth: 4
# 延续的行的缩进宽度
ContinuationIndentWidth: 4
# 去除C++11的列表初始化的大括号{后和}前的空格
# 区别:
# 括号列表中没有空格。
# 在结束大括号前不能换行。
# 用连续缩进，而不是用块缩进。
# 分析格式化文件中最常用的行结束符(\r\n或\n)
Cpp11BracedListStyle: true
# 分析格式化文件中最常用的行结束符(\r\n或\n)
DeriveLineEnding: true
# 如果为 true，分析格式化文件，以确定最常见的 & 和 * 对齐方式
# 指针和引用对齐样式将根据文件中找到的首选项进行更新。然后，PointerAlignment 仅用作后备
DerivePointerAlignment: false
# 关闭格式化
DisableFormat:   false
# 删除访问修饰符后的所有空行
EmptyLineAfterAccessModifier: Never
# 总是在访问修饰符之前添加空行，除非访问修饰符位于结构或类定义的开头。
EmptyLineBeforeAccessModifier: Always
# 自动检测函数的调用和定义是否被格式为每行一个参数(Experimental)
# 注意:这是一个实验标志，它可能会消失或被重命名。不要在配置文件中使用它，等等。使用风险自负。
ExperimentalAutoDetectBinPacking: false
# 自动补充namespace注释
FixNamespaceComments: true
# 需要被解读为foreach循环而不是函数调用的宏
ForEachMacros: ['RANGES_FOR', 'FOREACH']
#一组宏应该被解释为条件语句而不是函数调用
IfMacros: ['IF']
# 多个#include块合并在一起并排序为一个
# Regroup合并多个#include块，并且整体排序，然后根据类别优先级分组，可查询IncludeCategories
IncludeBlocks:   Regroup
# 可以定义负数优先级从而保证某些#include永远在最前面
IncludeCategories:
  - Regex:           '^"(llvm|llvm-c|clang|clang-c)/'
    Priority:        2
    SortPriority:    2
    CaseSensitive:   true
  - Regex:           '^(<|"(gtest|gmock|isl|json)/)'
    Priority:        3
  - Regex:           '<[[:alnum:].]+>'
    Priority:        4
  - Regex:           '.*'
    Priority:        1
    SortPriority:    0
# 判断主包含的正则表达式。指定一个正则表达式，其表明了文件到主包含映射中被允许的后缀名
IncludeIsMainRegex: '(Test)?$' #？
#  判断源文件的正则表达式。为被格式化的文件指定正则表达式，匹配正则表达式的这些文件允许在文件到main-include映射中被视为“main”。
IncludeIsMainSourceRegex: ''
# 缩进访问修饰符
IndentAccessModifiers: false

# case 标签后面的块使用与 case 标签相同的缩进级别
IndentCaseBlocks: true
# 缩进case标签
IndentCaseLabels: true

# Extern扩展块缩进。按BraceWrapping.AfterExternBlock设置来进行格式化
IndentExternBlock: AfterExternBlock
# 缩进goto标签。当为 false 时，goto 标签被推到最左侧
IndentGotoLabels: false
# 缩进预处理器指令
IndentPPDirectives: BeforeHash

# 缩进模板中的requires子句
IndentRequires:  false
# 缩进宽度
IndentWidth:     4
# 函数返回类型换行时，缩进函数声明或函数定义的函数名
IndentWrappedFunctionNames: true
#插入括号 在c++中，在控制语句(if、else、for、do和while)后面插入大括号，除非控制语句在宏定义中，或者大括号包含预处理程序指令。
# 将此选项设置为true可能导致错误的代码格式，因为clang-format缺乏完整的语义信息。因此，在检查此选项所做的代码更改时应格外小心。
InsertBraces : true
# 插入尾随逗号，在包装在多行中的括号字面值中插入尾随逗号。
# 如果设置为Wrapped，则会在跨多行换行的容器字面量(数组和对象)中插入尾随逗号。它目前只对JavaScript可用，默认禁用。请注意，这在概念上与装箱不兼容，因为末尾的逗号被用作指示容器应该每行格式化(即不包含)。因此，插入末尾逗号会抵消括号的作用。
InsertTrailingCommas: None

# 保留JavaScript字符串引号
JavaScriptQuotes: Leave
# 包装 JavaScript 导入/导出语句
JavaScriptWrapImports: true

# 保留在块开始处的空行
KeepEmptyLinesAtTheStartOfBlocks: false

# 相对于 lambda 签名对齐 lambda 主体
LambdaBodyIndentation: Signature

# 开始一个块的宏的正则表达式
MacroBlockBegin: ''
# 结束一个块的宏的正则表达式
MacroBlockEnd:   ''

# 连续空行的最大数量
MaxEmptyLinesToKeep: 1
# 命名空间的缩进
NamespaceIndentation: All

ObjCBinPackProtocolList: Auto
# 使用ObjC块时缩进宽度
ObjCBlockIndentWidth: 4
ObjCBreakBeforeNestedBlockParam: true
# 在ObjC的@property后添加一个空格
ObjCSpaceAfterProperty: false
# 在ObjC的protocol列表前添加一个空格
ObjCSpaceBeforeProtocolList: true

# 缩进预处理器语句的列数
# PPIndentWidth 用于缩进预处理器语句的列数。
PPIndentWidth:   2

PenaltyBreakAssignment: 2
PenaltyBreakBeforeFirstCallParameter: 19
PenaltyBreakComment: 300
PenaltyBreakFirstLessLess: 120
PenaltyBreakString: 1000
PenaltyBreakTemplateDeclaration: 10
PenaltyExcessCharacter: 1000000
PenaltyReturnTypeOnItsOwnLine: 60
PenaltyIndentedWhitespace: 0

# 指针的对齐: Left, Right, Middle
PointerAlignment: Right
# 限定符对齐。排列说明符和限定符的不同方式（例如 const/volatile）
#将QualifierAlignment设置为Leave以外的值，可能会导致由于clang-formats缺乏完整的语义信息而做出的错误决策而导致错误的代码格式。因此，在检查使用此选项所做的代码更改时应格外小心。
QualifierAlignment : Leave

# 引用的对齐
ReferenceAlignment: Pointer
# 允许重新排版注释
ReflowComments:  true
# 指定使用空行分隔定义块，包括类、结构、枚举和函数。
SeparateDefinitionBlocks: Leave
# 短命名空间跨越的最大展开行数
ShortNamespaceLines: 1

# 允许排序#include
SortIncludes:   Never #CaseSensitive

# java静态导入放在非静态导入之前
SortJavaStaticImport: Before

# 对using声明排序
SortUsingDeclarations: true

# 在C风格类型转换后添加空格 (int) i; (int)i;
SpaceAfterCStyleCast: false
# 在!后添加空格
SpaceAfterLogicalNot: false

# 在Template关键字后添加空格
SpaceAfterTemplateKeyword: true

# SpaceAroundPointerQualifiers 定义在何种情况下在指针限定符之前或之后放置空格

# 在赋值运算符之前添加空格
SpaceBeforeAssignmentOperators: true
# 在case冒号之前添加空格
SpaceBeforeCaseColon: true

# 在C++11大括号列表之前添加空格
SpaceBeforeCpp11BracedList: false
# 在构造函数初始化器冒号之前添加空格
SpaceBeforeCtorInitializerColon: true
# 在继承冒号前添加空格
SpaceBeforeInheritanceColon: true
# 开圆括号之前添加一个空格: Never, ControlStatements, ControlStatementsExceptControlMacros, Always
SpaceBeforeParens: ControlStatementsExceptControlMacros
# 如果SpaceBeforeParens被设置为自定义，那么使用SpaceBeforeParensOptions来指定如何处理括号前的每个空格。否则，它将被忽略。
# SpaceBeforeParensOptions 圆括号前空格控制 
# 不要确保指针限定符周围有空格，而是使用 PointerAlignment
SpaceAroundPointerQualifiers: Default
# 在基于范围的for循环冒号之前添加空格
SpaceBeforeRangeBasedForLoopColon: true
# 如果为 true，空格将在 [ 之前。不受影响。只有第一个 [ 会被加一个空格。
SpaceBeforeSquareBrackets : false
# 空块中的空格, 空{}中间添加空格
SpaceInEmptyBlock: false
# 在空的圆括号中添加空格
SpaceInEmptyParentheses: false
# 在尾随的评论前添加的空格数(这并不影响尾随块注释(/* -注释)，因为它们通常具有不同的使用模式和许多特殊情况。)(只适用于//)
SpacesBeforeTrailingComments: 4
# 在尖括号的<后和>前添加空格
SpacesInAngles:  Never
# 在C风格类型转换的括号中添加空格
SpacesInCStyleCastParentheses: false
# 不在if/for/switch/while条件周围插入空格
SpacesInConditionalStatement: false
# 在容器(ObjC和JavaScript的数组和字典等)字面量中添加空格
SpacesInContainerLiterals: false

# 行注释开头允许有多少个空格。要禁用最大值，请将其设置为-1，除此之外，最大值优先于最小值
SpacesInLineCommentPrefix:
  Minimum:         1
  Maximum:         -1
# 在圆括号()的 '(' 后和 ')' 前添加空格
SpacesInParentheses: false
# 在方括号[]的[后和]前添加空格，lamda表达式和未指明大小的数组的声明不受影响
SpacesInSquareBrackets: false

# 标准
Standard:        Auto
# 在语句前面被忽略的宏定义，就好像它们是一个属性一样
StatementAttributeLikeMacros:
  - Q_EMIT
# 应该被解释为完整语句的宏定义
StatementMacros:
  - Q_UNUSED
  - QT_REQUIRE_VERSION

# tab宽度
TabWidth:        4
# 使用\n换行
UseCRLF:         false
# 使用tab字符：ForIndentation——仅将制表符用于缩进
UseTab:          Never
# 对空格敏感的宏定义
WhitespaceSensitiveMacros:
  - STRINGIZE
  - PP_STRINGIZE
  - BOOST_PP_STRINGIZE
  - NS_SWIFT_NAME
  - CF_SWIFT_NAME
...
```

## Reference
- [# vscode设置C++代码格式化（Clang-Format）](https://juejin.cn/post/7071871587674243102)  
- [# Clang 18.0.0git documentation](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)  
- [# Clang 配置说明-稍官方](https://clang-format.netlify.app/BasedOnStyle/)  
- [# C++ Clang-Format：代码自动格式化-详细](https://blog.csdn.net/weixin_43717839/article/details/129382657?spm=1001.2014.3001.5502)  
- [# Clang-format格式化及配置参数-详细（疑似搬运上篇）](https://blog.csdn.net/Once_day/article/details/127761573)  
- [# clang-format 配置文件翻译-简略](https://blog.csdn.net/a405197809/article/details/117634686)  
