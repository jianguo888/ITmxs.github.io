---
title: "如何写好flutter代码"
date: 2021-09-27T23:31:49+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍如何写好flutter代码

缘起

代码规范作为一个研发团队的核心基因，怎样在团队中高效传承是一个挑战。Flutter作为移动端持续火热的新型框架，正吸引越来越多的研发同学进入这个领域。但是面对全新的技术框架和之前几乎很少接触的dart语言，怎样写好Flutter代码困扰了无数Flutter初学者。文本就着重分享一下闲鱼Flutter团队在基础代码规范方面所做的一些实践。

Flutter 静态代码扫描

Native开发同学都知道Android/iOS都有配套的Linter检查机制。开发者可以通过Linter检查，统一代码风格，检查代码中的明显缺陷。Flutter同样具备这样的能力。通过Linter检查机制，是统一代码风格最简单高效的方式。 

#### **▐** **lutter Linter机制**

Flutter Linter检查的机制植根于Dart Linter检查，同时做了大量的功能拓展。Flutter的Linter规则记录在工程一级目录的analysis_options.yaml文件中。大家可以通过对该文件的定制来获得适合自己团队的代码规则。

Flutter Linter 规则都从属于三个主要的集合：

- ERROR

规则标识的是代码中可能出现的错误

- Style



规则标识的是代码风格的问题



- Pub



规则标识的是Flutter包管理相关的问题



需要注意的是，考虑到实际开发场景中的稳定性。调整Linter规则的时候需要关注规则对应的成熟度。目前主要的成熟度等级包括：



- Stable - 稳定
- Experimental - 实验性规则，可能会不太稳定
- Deprecated - 不再推荐使用的规则，建议废弃



#### **▐** **常见的Linter规则集**



- effective_dart



effective_dart是dart语言配套的Linter 规则。规则来源于Effective Dart文档。该文档中有对Dart语言高效使用的各种规范。effective_dart[2] 规则比较老，目前已经废弃。



- pedantic



google内部dart开发规范集合。目前也已经废弃。



- flutter_lints



是Flutter团队推荐的Flutter相关规则集。推荐大家在Flutter apps, packages, and plugins中广泛使用。本身是dart recommended 规则集合的拓展版本。该规范会影响到发布到 pub.dev[5] 中 package的得分。

- lints[6]

Dart团队推荐使用的规则集。核心包括两个子集：core & recommended。

静态代码扫描闲鱼规范的取舍

在众多Liner规则中，选择符合团队需要的规则是一个很大的挑战。闲鱼在做的过程中，遵循了一些基本原则。

#### **▐** **表达简洁**

在语义等效的情况下，充分利用dart语言的各种高级语法来降低表达的复杂度。简洁表达不仅能提升编码效率，同时也可以提升代码的阅读效率。举几个典型例子：

- 去除冗余new关键字【unnecessary_new】

闲鱼代码库中有大量的代码都是使用new关键字来进行类初始化的。这一方面是因为闲鱼切入Flutter比较早，有历史包袱；另一方面也是因为客户端同学将其他语言（例如java）的编码习惯带到了Flutter。虽然要修改的地方有很多，但是为了表达简洁，我们依然坚定的加入这条规则。

- 高效null处理【prefer_conditional_assignment， prefer_null_aware_operators】

dart语法中有很多null处理的语法糖，能大幅提升判空的效率，提升代码的健壮性。这里包括？的判断，??的判空，以及null safety。需要不断引导团队同学更多使用这样的语法来提升效率，而不是继续使用if else的冗杂表达。

#### **▐** **减少歧义**

代码表达需要准确，同学之间的理解尽可能一致。减少代码之外的“以为”，增加字里行间上的“确定”。典型列子如下：

- 显式变量类型【always_specify_types】

是否要明确变量类型，团队内部不同技术栈背景的同学有比较大的分歧。前端背景的同学倾向于通过推断的方式进行变量命名，好处是表达简洁。客户端背景同学倾向于直接明确定义，好处是直观，减少不必要的推断。最终经过激烈讨论，大家还是决定采用明确变量类型的方案。从实践角度出发，直接明确变量类型虽然从表达上略冗杂，但是歧义确实更少。

- 变量/函数状态坚持最小化表达 【annotate_overrides，prefer_const_declarations，prefer_final_fields】

一个变量或者方法如果重写了，请明确加上override注解。一个变量如果确定是const变量，请加上const关键字。一个变量如果没有再被赋值，请定义成final。坚持最小化表达，有如下几点收益：



- 最小化变量状态表达能精准刻画变量状态



最小化变量状态是指用最严格的属性描述变量。例如一个变量从之前的多处赋值，改为一处赋值。那么变量其实自带了final的隐形属性。最小化状态表达要求必须加上final关键字，而不是推断是final。因为一旦加上final关键字，后续在赋值就会报错。这能减少不必要的理解成本，最大限度避免变量状态的隐形改变。



- 提升代码性能



增加的final const关键字能让编译器做更多优化，提升代码的整体性能。



#### **▐** **风格一致**



多人开发的团队中，每个人都有自己的喜好。代码管理应该避免出现“破窗效应”。一个人把代码写烂了，后面的同学照着写都烂了。正所谓，无规矩不成方圆。基础代码的表达，不追求绝对的对与错，追求的是在风格上的尽可能统一。这里有太多典型例子：



1. 命名规范大体遵循驼峰的方式【camel_case_types，non_constant_identifier_names，constant_identifier_names 】
2. 控制流中尽量使用大括号【curly_braces_in_flow_control_structures】
3. import顺序按照先dart引用，再package引用，再相对引用方式分模块，模块内引用按字母表排序【directives_ordering】
4. required 关键字标识的变量请排到前面 【always_put_required_named_parameters_first】
5. flutter布局中优先使用SizedBox而不是Container【sized_box_for_whitespace】
6. flutter颜色定义使用8位16进制整数标识颜色值【use_full_hex_values_for_flutter_colors】
7. flutter widget在构造函数中加入key参数【use_key_in_widget_constructors】



这里的每一条都不具有非这样不可的理由。但是在闲鱼写代码就必须遵循这个规范。不为别的就为了风格的统一，为了提升团队同学业务轮转的效率。



#### **▐** **代码质量**



Linter检查的另一个重要的目标是发现潜在的代码缺陷。这对代码管理来说就更为重要了，因为这直接关系到稳定性的大局。稳定性是开发人员的底线，再怎么小心也不为过。同样举几个典型的例子：



1.避免给void赋值 【void_checks】

2.变量比较之前先判断类型 【unrelated_type_equality_checks】

3.避免catch 空实现【empty_catches】

4.避免使用隐形类型传递 【avoid_shadowing_type_parameters】

5.避免await 非future对象 【await_only_futures】

6.集合的remove需要传递符合集合的类型的参数【list_remove_unrelated_type】



如果这些问题代码带到生产环境，轻者出现exception，重则功能不可用。防患于未然，在代码最开始编写阶段，就及时处理这些问题，代码才能更健壮。



#### **▐** **维护成本**



效率对一个团队固然很重要，但是追求一时的效率，而不顾及代码生命周期内整体的效率是非常短视的行为。我们今天越发关注代码在后期的维护成本。基于此，文档&注释的重要性正越发凸显出来。一个好的注释，能省下很多的答疑，很多的试错，很多的猜疑。不断引导大家写并维护文档及注释，是闲鱼代码规范明确的目标。举几个明确的例子：

1.Deprecated函数需要给出明确的注释[provide_deprecation_message]

2.注释中的变量引用，符合引用的约束[comment_references]

#### **▐** **规则权威**

闲鱼团队Liner规则的制定，不是一家之言。邀请了团队所有同学参与到规则的制定过程。一个规则的权威性来源于所有执行者发自内心的认同。举一个典型例子：

在讨论标准格式化的时候，团队同学集中讨论了一个规则：

【- lines_longer_than_80_chars # 争议规则，每行长度不超过80字符】

大家认同统一格式化代码风格的重要性。大家争议的焦点是：80字符在很多高分辨率显示器上太短了。这会导致很多不必要的换行，降低代码阅读体验。我们听取了开发同学反馈，把该条规范去掉了。改为统一android studio配置来执行。最终标准是12号字，一行最多160字符。

写在最后

当然仅仅做到上面的部分，仅仅是万里长征的第一步。基础代码规范这件事，我们并不追求规则的多，而是更在意规则实际的效率收益。规则制定的过程中，我们听取了开发同学真实的反馈，删除了很多并不必要的规则。简单，高效，并不为了规范而规范是我们核心的目标。当然最终确定的规则，要尽最大努力保证执行。目前我们正在积极建设更完善的CI体系，将规范规则化。通过持续快速检测，实时给使用者反馈，降低规范落地的成本。代码是研发团队的核心资产，需要我们共同守护

附录

上面介绍的规则细节，如果有不清楚的，可以在下面的链接中找到更详细的解释。

**linter-rules[7]**

#### **▐** **闲鱼核心Liner规则**

附上闲鱼的核心Liner规则，给大家一点参考。

命名

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqP5SsyyFagjCHhzQLN8aM2ocWHTQ6Tzoq6scnHSJZupWQuF1LgBjNWrA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



排序

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPJH0V5rOvqDGwyVzuDZHicI1Rame6eX53iafiakVic1iaicChbibfEAa2YgTug/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



格式化

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPubwonPicEibr8qbiclfrT8eJYXkHU4VT8K6FIia2pmibSgicKUEMztcynVQw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



评论

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPWsmD8Oj83bRichibw9tTxibQN5Xb2ery6LSbBceZBCF8RvKayRCj7u9Aw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



库使用

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPic7skJFmTW4aCK8KERcJPwbOr6bF4Q6qdxdltiao5K86OH3sr7gJK12g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



NULL

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPco6t6uazFZXPnpUGpHtG9V1kUJ5WtuDUoU8O8AmjiaIeAou9o2Ww6xQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



字符串

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqP9wJqic52ygsXlo33ezXbXw5JY0G2kbtx3rpYtGT0v97ThNrkPmiax4Ww/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



集合

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPHj3ov0IEWfwZu0WhYLvxR5cwnU8gUy2nAPo5qoYhvEoEgpoJxKCDWw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



函数

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPGTCk4REHrfTpFTrsJnpGP7bn1icW7ianOS3KiadkU6PgCua06A61emicQA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



成员变量

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPibNfic1V7TFlXCldH4107JngTLhkia9ldCUsp4iaS8Tkod77MNPWJ8vcpg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



构造函数

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPN01P77PLILLaUBoIINL9sQ7JKNltxk6oP9AmHkbvwoBcAG5YR6iaVqg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



异常处理



![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPiaD9gzm1h6iaY8XS9zB3dibdJTiaFJtTHNt7G5zNxocagWuzFvU2EDDtGA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Mixin

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPG0NMShuv8hkq4Wz5r2IhBOL1T7J8FQQB4MbrxpejibtW18a1kzOibPYA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



类型

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPdgu1d7dyEP9rrz3KzBgO8N7cC7ZFRA7qBaHUz61Cicer9thCfyFicSZg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



参数

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqP7wSUBuycKbPDTs5a3bm3BVRcVXUubDWRFj9Z2ncu7MD1CenmvGRWmw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



质量

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPsZ4oVgoO1r8TCYkoAdSxOe2lxA2SaaC423GJUbR2JJqqbRKWgDXBibQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Core集合融合部分

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPZMJVbicNppeTadcdOLWKx1UB1RibEoflibXHAB0QBJ3p21kheEN5jSfCQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Recommended 集合融合部分

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqP9WzIiauY3CRTsMQAicbmh3DgPyhsfLabIsuupV3a7GFnCzF78I96VgKg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Flutter 集合融合部分

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPqZF0mowLLBETtZFgYdicsPLttVj8NXlgaibaeVyJibicLpTUJvia90uXI0g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



其他规则融合部分

![图片](https://mmbiz.qpic.cn/mmbiz_png/33P2FdAnjuib5wzzR2j35IHafBWn1BiaqPEqMSOQZV6ubNJxXn97yyPedYeg27W0HZCDFxSyicBpK9ZRMZpIVGziaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Reference

####  

[1] effective_dart: https://pub.dev/packages/effective_dart
[2] effective_dart: https://pub.dev/packages/effective_dart
[3] pedantic: https://pub.dev/packages/pedantic
[4] flutter_lints: https://pub.dev/packages/flutter_lints
[5] pub.dev: https://pub.dev/
[6] lints: https://pub.dev/packages/lints
[7] linter-rules: https://dart.dev/tools/linter-rules#lints

