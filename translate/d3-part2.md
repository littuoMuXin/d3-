# D3：数据驱动文档（2）

## 2.相关工作

D3 并不是一个传统的可视化框架，它并没有引入新的图形语法，而是解决了一个不同的，更小的问题：基于数据有效地操作文档。因此，D3 的核心贡献在于提供了一个可视化的“内核”，而不是一个框架，与它最相似的库是 jQuery，CSS 以及 XSLT，它们都能够对文档进行转换。因为 D3 的文档模型直接指定了图元，所以它和一些低层的图形库如 Processsing，Raphael  也存在相似之处。为了更好的兼容性，D3 里面包含了一系列的辅助模块，这些模块在基于选择的内核之上构建，在很大程度上受到了早先的可视化系统如 Protovis 的影响。

### 2.1 文档转换

虽然浏览器拥有操作 DOM 的内置 API，但这些接口是冗长和繁琐的，可能是因为标准组织强调明确的设计，以便供应商可以保持一致的实现并在未来做修订。因此，能够帮助开发者更方便地操作 DOM 的 JavaScript 库【19，23，29，33】很受欢迎。其中， jQuery  的成功更是毋庸置疑，很多 JavaScript 的新手会从使用它开始入门。

这些库拥有相同的选择的思想：使用简单的谓词识别一组元素，然后使用一系列的操作改变选取的元素。这种思想的普遍性不是偶然，它来源于层叠样式表 [21]（CSS），一种用来设置元素样式（如字体和颜色）的声明式语言。在 CSS 之上，基于 JavaScript 的选择提供了很大的灵活性，因为在有用户事件或数据变化时样式可以被动态计算得到。

```
// a
var ps = document.getElementsByTagName("p");                         

for (var i = 0; i < ps.length; i++) {  
   var p = ps.item(i);
   p.style.setProperty("color", "white", null);
}

// b
p { color: white; } 

// c
$("p").css("color", "white"); 

// d
d3.selectAll("p").style("color", "white");  
```
**图3.简单的文档转换：将段落颜色变为白色。(a) W3C DOM API (b) CSS (c) jQuery (d) D3**

数据可视化中，文档转换不仅要处理已有节点的样式，还必须处理元素的创建和删除。这用 CSS 是不可能实现的，而用 Query 实现又会很繁琐，因为 jQuery 缺乏跟随数据创建和删除元素的机制，数据必须与节点单独绑定（如果真用 jQuery 来实现的情况下），而不是通过高层的数据连接（参见 §3.2）。这使得 jQuery 不能实现数据驱动转换，也因此并不适用于涉及复杂转换的动态可视化。

扩展样式表转换语言 [41] (XSLT) 是文档转换的另外一种声明式方法。源数据以 XML 格式输入，然后使用包含模板规则的 XSLT 样式表将其转换成 HTML。XSLT 用每一条规则模式去匹配数据源，并通过递归程序指示相应的输出文档结构。XSLT  方法在处理简单的转换时是优雅的，但在处理复杂计算的可视化任务（如插入，地理映射或者统计方法）时会很繁琐，因为它没有高层视觉抽象，也没有命令式编程的灵活性。 

### 2.2 图形库

直接处理图形标志可以帮助我们让工具包的表现更接近期望结果，减少设计师在执行时的障碍，进而提高工具包的可用性【24】。然而，如前面所述【2】，Processsing【28】，Raphael【30】 等低层图形库因为缺乏便捷的抽象，因而在处理复杂的可视化任务时非常的繁琐。

另一方面，许多图形库没有提供可查看的视图用于调试。例如，Processsing 使用直接模式进行呈现，Processsing 对 SVG 和 微软的私有矢量标记语言（VML）进行了封装。工具包提供的视图抽象会降低兼容性和表现力：元素的样式不能使用外部样式表来定义，甚至一些原生支持的图形效果，如虚线和复合过滤等，也有可能无法使用。

图形抽象的小改动也会给新用户造成障碍。下面以画一个轮子为例。在 Processing 中，ellipse 运算符用来画圆，需要传入四个参数：圆心的 x，y 坐标，宽度和高度。Raphael  也提供了画圆的运算符，它接收三个参数：圆心的 x，y 坐标以及半径。Protovis  定义的 Dot 和 Wedge 标记类型都可以渲染圆，Line 类型则可以实现极坐标插值。上述抽象和标准的 SVG circle 元素的差异很小。标准受网络效应的推动：使用一项技术的人越多，对文档的需求就越大，技术输出也越大。虽然开发者花费了精力给他们的库写文档，但还是远不如关于标准的文献和学习资料多。

### 2.3 信息可视化系统

研究者开发了各种工具，以简化可视化设计。其中一类框架【8，38】提供了一系列的可视化组件。使用这类框架创建可视化可以使用现有的组件或者编写新的组件。第二类框架【9，14】对信息可视化参考模型进行了明确的实例化，通过一组运算符就可以实现数据管理，视觉编码以及交互。虽然使用这些新的运算符集可以实现自定义视图，但在实践中我们发现，大多数新颖的可视化实现都需要编程人员自己去编写全新的运算符。因此，只要创建可视化的编程人员有着专业的软件工程知识，两种类型的框架都能提供很好的效果，但对于的一般的受众如网页设计师而言，这些框架都难以使用。

鉴于我们之前在 Protovis【2，13】上的投入，我们不再主张使用声明式的，特定领域的语言（DSLs）实现可视化。通过将规范从执行细节中解耦出来，声明式系统允许语言使用者专注在他们应用领域的细节之中，同时语言开发者也可以很自由地去优化处理。与  Protovis 类似，D3 提供了将数据映射到可视化元素上的声明式框架。但与 Protovis及其它基于语法的声明式模型【39，40】不同的是，D3 并没有严格地限制使用其工具指定的图形标志词汇，它直接将数据特性映射到文档对象模型中的元素上。

不论这种设计是否是冒险的，甚至是否可能实现，这取决于环境：许多编程环境并不提供标准的视图抽象。工具特定的视图抽象有着引人注目的优势。有论文【13】证明，自定义抽象可以促进可移植性（跨平台部署）和性能优化。图形标志的语法设计也可以提高效率【2】。因此，本文认为工具集特定的呈现仍然是许多可视化模型的重要组件，在D3 中辅助模块扮演了这种角色（见 §3.4 ）。

由于技术的限制以及 web 标准缓慢的更新速度，基于浏览器实现的可视化往往需要我们采用不一样的方法。浏览器环境也不如编译程序语言，不能提供相同的最优条件。将内部呈现映射到 DOM 的开销反而还会引入性能瓶颈。中间呈现还可能致使调试变复杂，因为编码（以抽象图形标志编写）和可检查的输出（例如 DOM 中的 SVG 元素）之间的映射常常不太清晰。自定义的抽象可能还会限制表现力：当有新的浏览器特性出来后，它们必须回过头去修改，利用这些新特性，并且封装还可能导致它们没法利用一些技术，比如 CSS。至关重要的是，D3 还引入了可能会通知其他可视化框架的特征：查询驱动的选择器和绑定到视图元素的数据,作为原子操作的文档转换，和直接的属性计算语法。下一节将介绍 D3 系统的设计，然后继续详细回顾我们拥有的设计选择以及它们相关的权衡。
