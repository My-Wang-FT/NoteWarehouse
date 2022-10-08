# Latex语法与使用

教程来源包括：

* [从零开始的Latex使用教程](https://zhuanlan.zhihu.com/p/428694760)
* [一份其实很短的 LaTeX 入门文档](https://liam.page/2014/09/08/latex-introduction/)

LaTeX是一种“非所见即所得”的排版系统，用户需要输入特定的代码，保存在后缀为.tex的文件中，通过编译得到所需的pdf文件。

## Latex基本构成部分

### documentclass

当 latex 处理源文件时， 首先需要知道的就是作者所要创建的文档类型。 文档类型可由`\documentclass` 命令来指定。

```latex
\documentclass[option]{class}
```

`clsss` 指定想要的文档类型。表1给出了一些用到的文档类型。

| 文档类型 | 描述                                                         |
| :------: | :----------------------------------------------------------- |
| article  | 排版科学期刊、 演示文档、 短报告、 程序文档、 邀请函……       |
|   proc   | 一个基于 article 的会议文集类                                |
| minimal  | 非常小的文档类。 只设置了页面尺寸和基本字体。 主要用来查错。 |
|  report  | 排版多章节长报告、 短篇书籍、 博士论文……                     |
|   book   | 排版书籍。                                                   |
|  slides  | 排版幻灯片。 该文档类使用大号 sans serif 字体。 也可以选用 FoilTEXa 来得到相同的效果。 |

通过`options` 参数可以定制文档类的属性。 不同的选项之间须用逗号隔开。

|        文档常用选项         | 描述                                                         |
| :-------------------------: | :----------------------------------------------------------- |
|      10pt, 11pt, 12pt       | 设置文档中所使用的字体的大小。 如果该项没有指定， 默认使用10pt 字体。 |
| a4paper, letterpaper, . . . | 定义纸张的尺寸。 缺省设置为letterpaper。 此外， 还可以使用a5paper, b5paper, executivepaper 以及legalpaper。 |
|            fleqn            | 设置行间公式为左对齐， 而不是居中对齐。                      |
|            leqno            | 设置行间公式的编号为左对齐， 而不是右对齐。                  |
|   titlepage, notitlepage    | 指定是否在文档标题(document title) 后另起一页。 article 文档类缺省设置为不开始新页， report 和book 类则相反。 |
|    onecolumn, twocolumn     | latex 以单栏(one column) 或双栏(two column) 的方式来排版文档。 |
|      twoside, oneside       | 指定文档为双面或单面打印格式。 article 和report 类为单面(single sided) 格式， book 类缺省为双面(double sided) 格式。 注意该选项只是作用于文档样式， 而不会通知打印机以双面格式打印文档。 |
|          landscape          | 将文档的打印输出布局设置为 landscape 模式。                  |
|     openright, openany      | 决定新的一章仅在奇数页开始还是在下一页开始。 在文档类型为article 时该选项不起作用， 因为该类中没有定义“章” (chapter)。 report 类默认在下一页开始新一章而book 类的新一章总是在奇数页开始。 |

### usagepackage

排版文档时， 你可能会发现某些时候基本的LATEX 并不能解决你的问题。 如果想插入图形(graphics)、 彩色文本(coloured text) 或源代码到你的文档中， 你就需要使用宏包来增强LATEX 的功能。 可使用如下命令调用宏包。

```latex
\usepackage[options]{package}
```

这里package 是宏包的名称， options 是用来激活宏包特殊功能的一组关键词。很多宏包随LATEX 基本发行版一起发布。随Latex一起发布的宏包：

|   包名   | 说明                                                         |
| :------: | ------------------------------------------------------------ |
|   doc    | 排版LATEX 的说明文档。                                       |
| exscale  | 提供了按比例伸缩的数学扩展字体。                             |
| fontenc  | 指明使用哪种LATEX 字体编码(font encoding)。                  |
|  ifthen  | 提供如下形式的命令‘if . . . then do . . . otherwise do . . . .’ |
| latexsym | 提供LATEX 符号字体。                                         |
| makeidx  | 提供排版索引的命令                                           |
| syntonly | 编译文档而不生成 dvi 文件                                    |
| inputenc | 指明使用哪种输入编码， 如 ASCII, ISO Latin-1, ISO Latin-2, 437/850IBM code pages, Apple Macintosh, Next, ANSI-Windows 或用户自定义编码。 |

```latex
% 这里我们使用了UTF8的编码格式和ctex宏包来解决中文编码的问题
\usepackage[UTF8]{ctex}
% 由于现在LaTeX对中文的支持已经很完善，因此我们可以直接使用\documentclass[UTF8]{ctexart}，代表该文档是中文论文（ctex+article。推荐使用这种方式，因为对部分的宏包的支持较好。
\documentclass[UTF8,fontset=windows]{ctexart}
```

### pagestyle

LATEX 支持三种预定义的页眉/页脚(header/footer) 样式， 称为页面样式(pagestyle)。使用方式：

```latex
\pagestyle{style}
```

其中的 style 参数确定了使用哪一种页面样式 。Latex预定义的页面样式：

|  style   | 描述                                          |
| :------: | --------------------------------------------- |
|  plain   | 在页脚正中显示页码。 这是页面样式的缺省设置。 |
| headings | 在页眉中显示章节名及页码， 页脚空白。         |
|  empty   | 将页眉页脚都设为空白。                        |

### 文本区域













































------

# 结束行