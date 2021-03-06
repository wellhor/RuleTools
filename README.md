# RuleTools

[![RuleTools v0.0.15](https://img.shields.io/badge/RuleTools-v0.0.15-orange)](https://github.com/wellhor/RuleTools)
[![JitPack](https://www.jitpack.io/v/wellhor/rule-tools.svg)](https://www.jitpack.io/#wellhor/rule-tools)
[![Java 8+](https://img.shields.io/badge/java-8+-4c7e9f.svg)](http://java.oracle.com)
[![License](https://img.shields.io/badge/license-MIT-green)](https://github.com/wellhor/RuleTools/blob/master/LICENSE)
[![Antlr4](https://img.shields.io/badge/anltr-v4-yellow)](https://github.com/antlr/antlr4)
 
  中文文档 | [ENGLISH](https://github.com/wellhor/RuleTools/blob/master/README-en.md)

** RuleTools ** 是基于ANTLR4实现的一个强大地文本表达式匹配器，除此之外还能高亮出表达式在文本中命中的位置。


# 表达式

```
# 后运算符
  科研#机构#5 
    表示文本需要符合 科研之后存在机构这个字且最大距离是5
  科研#机构 等价于 科研#机构#10 默认距离是10 
| 或运算符
  科研|机构
    表示文本需要符合 存在 科研 或者 机构当中的一个词
& 且运算符
  科研&机构
     表示文本中 科研 或者 机构两个词都必须存在
() 括号运算符
    提高表达式优先级 和算术运算中的括号一个效果
! 非运算符
    否定掉符号后面的表达式的匹配结果 
    例如 !科研 则 文中不包含科研 则为命中
```

# 如何使用

#### 步骤 1. 添加JitPack仓库到你的pom.xml文件里

```
<repositories>
	<repository>
        <id>jitpack.io</id>
        <url>https://www.jitpack.io</url>
    </repository>
</repositories>
```

### 步骤 2. 添加依赖  [![RuleTools 0.0.15](https://img.shields.io/badge/LatestVersion-0.0.15-orange)](https://github.com/wellhor/RuleTools)

```
<dependency>
    <groupId>com.github.wellhor</groupId>
    <artifactId>rule-tools</artifactId>
    <version>${latest.version}</version>
</dependency>
```

### 步骤 3. 添加测试  [更多的例子](https://github.com/wellhor/RuleTools/blob/master/src/test/java/pers/wellhor/test/ParseTest.java)

```
     private final static String EXPRESS = "((科研#机构#5)|(科学#技术#3)|(科学#杂志#2))&(基因#编辑)";
     
     private final static String CONTEXT = "一个由多国科研机构组成的国际委员会3日发表报告说，可遗传基因组编辑技术当前还达不到安全、有效地应用于人类的相关标准。各国在决定是否批准这类技术开展临床应用前，应就这一问题展开广泛社会讨论。";

    /**
     * 表达式校验
     *
     * 输出: 校验失败: [Line 1,Position 2] no viable alternative at input '明天!'
     */
    @Test
    public void testFormulaVerification() {
        try {
            TextAnalysis.formulaVerification("明天!你好");
        } catch (TextGrammarException e) {
            System.out.println(String.format("校验失败: [Line %d,Position %d] %s", e.getLine(), e.getCharPositionInLine(), e.getMessage()));
        }
    }


    /**
     * 简单展示
     * 输出: ((科研#机构#5)|(科学#技术#3)|(科学#杂志#2))&(基因#编辑): 命中
     */
    @Test
    public void testParse() {
        HitResult hitResult = TextAnalysis.parse(CONTEXT, EXPRESS);
        System.out.println(String.format("%s:%s", EXPRESS, hitResult.isHit() ? "命中" : "未命中"));
    }

    /**
     * 高亮演示1
     * #################科研#机构#5#################
     *  一个由多国<font color='red'>科研机构</font>组成的国际委员会3日发表报告说，
     *  
     *  
     * #################基因#编辑#10#################
     * 说，可遗传<font color='red'>基因</font>组<font color='red'>编辑</font>技术当前还达不到安全、有效地应
     */
    @Test
    public void testHighLight1() {
        List<HighLight> highLights = TextAnalysis.highLight(CONTEXT, EXPRESS);
        highLights.forEach(this::showHighLight);
    }
```