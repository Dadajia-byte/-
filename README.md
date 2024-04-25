前端性能优化

一、前端性能优化体系与关键指标

1.1 性能优化体系概览

前端性能优化体系主要分为三个部分：性能优化流程、性能指标采集及上报、性能监控预警平台

1.1.1 性能优化流程

主要有指标设定、性能标准、收益评估、诊断清单、优化手段、性能立项、性能实践。

其中指标设定说的是我们要选择什么样的指标，例如：页面打开慢，我们需要从什么方面入手，怎么知道问题解决了。

在设定指标后，我们就要确定性能标准，优化到什么程度合适，例如：打开h5页面，确定的指标是秒开率，那么一秒内可以打开的请求比例就是性能标准。

但同时，所有的服务最后都要体现在产品上，所以我们还要将关联产品进行收益评估，例如：列表页到详情页的转化能不能提升，用户跳出率可不可以降低。

接下来，就是将业务代码接入性能监控预警平台，根据性能标准给出诊断清单。

这时诊断出性能问题，就可以结合性能标准和诊断清单确定对应的优化手段。

在落地实践之前，我们还需要对性能项目立项，赢得产品和后端的支持，以便你的优化可以顺利执行下去。

最后，项目上线，跟踪并对效果进行评估，结合场景或代码用文档记录沉淀下来。

1.1.2 性能指标采集及上报

主要内容是把前面提到的性能指标以代码的形式分解落地确保可以采集，然后在SDK封装后集合统计埋点，最后根据实际情况，指定上报策略。

在上报之前，还需要将明显异常的数据去除，以免占用网络带宽。

1.1.3 性能监控预警平台

当指标超过某一监控阈值时，性能监控预警平台会通过邮件或者短信的方式发送预警信息。

而性能预警平台主要分为两部分：性能数据处理后台和性能可视化展示前台两部分

性能数据处理平台

主要是在性能采集数据上报到性能平台后，对数据进行预处理、数据清洗和数据计算，然后生成前台可视化所需的数据。

性能可视化展示前台

主要是对核心数据指标进行可视化展现，对性能数据波动进行监控，对超出阈值的数值给出短信或者邮件报警。

最后，在满足上述三流程后，还需要做性能专项测试，检查采取的措施是否与优化预期是否一致。例如：能否正确发出请求，请求处理的流程是否正确，性能数据能否正确展现。

1.2 性能指标

出现了一个 活动页加载数据卡顿 的性能问题，优化它该怎么做呢？

按照上述流程，显然我们需要先确定它的性能指标和标准是什么。

那么我们怎么确定指标呢？

一个指标应有以下特征：

- 可衡量，可通过代码度量
- 关注以用户为中心的关键结果和真实体验

而在以用户角度出发，我列下以下三点方向：

1. 加载
2. 交互性
3. 视觉稳定性

1.2.1 加载

加载就是在进入页面时，页面内容的载入过程。

我们经常发现有的首页上文字、图片出现的很慢，而有的出现很快，这个出现的过程就是加载。

目前，业界目前在性能指标优化关键方面，就主要集中在加载方面（白屏时间、首屏时间）。

因为相较于另外两个指标，它更容易自动化采集数据，而非手动化。

白屏时间

指的是从输入内容回车（包括刷新、跳转等方式）后到页面出现第一个字符的时间。它的标准时间是 300 ms

这个过程详细包括：

dns查询->建立TCP连接->发送首个Http请求->返回html文档

根据上述过程我们可以推测以下因素可能导致白屏时间过长：

1. DNS 查询时间过长
2. 建立TCP链接太慢
3. 服务器处理请求速度太慢
4. 客户端下载、解析、渲染时长过长
5. 没有做Gzip压缩
6. 缺乏本地离线化处理
7. ……

首屏时间

首屏时间 = 白屏时间 + 渲染时间

从浏览器输入地址并回车后，到首屏内容渲染完毕的时间，这期间不需要滚动鼠标或者下拉页面，否则无效



其中 0.2s就是白屏时间，0.7s就是首屏时间。

首屏时间相较于白屏时间更重要。因为白屏时间尽代表页面开始加载，而首屏时间才代表页面真正开始使用。

首屏时间的标准，最初只是根据这个页面对时间是否敏感来判定的。

主要以用户平均首屏加载时间来计算，并没有详细区分 2G/3G/4G/WiFi 这些网络环境。

但是涵盖弱网环境，平均值显得又不准确，所以又开始采用中位数，做正态分布，看分位值统计，一般有以下分位值：P50、P90、P99（把所有首屏时间排序得到第n位）

但这样处理还是比较麻烦，所以又有了秒开率的概念，即1s内打开用户的占比。

但首屏时间毕竟粒度太粗了，当出现首屏时间长，白屏时间短的情况，又是哪里的问题呢？

所以又将首屏时间拆分为 白屏时间、数据接口响应时间、图片加载资源等。

白屏时间不再赘述，数据接口响应时间可以直接从后端服务获取，图片加载资源时间需要单独获取。

1.2.2 交互

交互就是用户点击网页或APP的某个功能页面时给出的回应。

例如：点赞功能，我们点击点赞后，点赞数立即加一，这就是交互体验好。

一般有以下指标：

1. FID 指标（First Input Delay，首次输入延迟）：一般指标尽量小于 100ms
2. PSI 指标（Perceptual Speed Index，视觉变化率）：一般衡量标准小于 20%

1.2.3 视觉稳定性指标

CLS（Cumulative Layout Shift），也就是布局偏移量，它是指页面从一帧切换到另一帧时，视线中不稳定元素的偏移情况。

例如：在页面链接点击购买时，原来的位置突然插入了一条广告，你点击了那条广告，显然作为用户你这时的体验是极差的。

CLS比较前沿，在2020年5月，Google才发布一篇文章对CLS做出相关介绍，而它的采集方法，除了依赖 Google 的 Lighthouse 做本地采集，还没有比较好的方案。

二、从页面加载聊性能优化

你的团队对首屏时间的要求是1200ms，但目前的首屏时间长达 2s，即使精简了首屏内容，合并了请求资源，对图片尺寸也进行了压缩优化，但最后首屏时间还是没有降下来

发生上述描述情况又是因为什么呢？

除了了解性能体系、关键性能指标之外，我们还需要对页面加载全过程有所了解才能更精确的对Web前端进行性能优化，那么下面让我简单聊聊页面加载过程。

2.1 页面加载概述

假设一个人正在上网，当他在浏览器中输入 URL 并回车后为了把 URL 解析为 IP 地址，浏览器会向 DNS 服务器发起 DNS 查询，获取 IP 地址。

获得 IP 地址后，浏览器向目标服务器发送三次握手原理和 TLS协商从而建立TCP连接。

而建立连接后，浏览器就可以发送 HTTP 请求，而服务端收到后，对请求进行响应。浏览器从响应结果拿出数据，并解析和渲染，最后便生成了一个网页。 

以上过程，我们可以大致分为三个阶段：

1. 客户端发起请求阶段
2. 服务端数据处理请求阶段
3. 客户端页面渲染阶段

2.1.1 客户端发起请求阶段

该阶段是指：用户在浏览器输入 URL 经过本地缓存确认是否已经存在这个网站

若没有，接着会由 DNS 查询从域名服务器获取这个 IP 地址，随后客户端通过 TCP 的三次握手和 TLS 协商向服务器发起 HTTP 请求建立连接的过程

在上述过程里：

- 本地缓存
- DNS 查询
- HTTP请求

很容易成为性能的瓶颈之一。

本地缓存瓶颈点

为什么本地缓存会成为前端性能优化的瓶颈之一呢？

我们知道，本地缓存可以让静态资源加载更快，当客户端发起一个请求时，静态资源可以直接从客户端中获取，不需要再向服务器请求，显然不用再请求速度一下就快起来了。

那么我们如何实现本地缓存呢？

本地缓存一般包括 强缓存 和 协商缓存 两种形式

强缓存是指浏览器在加载资源时，根据请求头的 expires 和 cache-control 判断是否命中客户端缓存

协商缓存是指浏览器会先发送一个请求到服务器，通过 last-modified 和 etag 验证资源是否命中客户端缓存

DNS 查询瓶颈点

讲完了本地缓存，我们再讲讲 DNS 查询。

DNS 之所以会成为前端性能优化的瓶颈点，是因为每次进行 DNS 查询都要经历从手机到移动信号塔，再到认证 DNS 服务器的过程。

因此想要节省时间，就要让 DNS 查询走缓存。而同时浏览器也提供了 DNS 预获取的接口，我们可以在打开浏览器或者 WebView 的同时就进行配置。

这样一旦缓存命中，就可以避免发送请求到 DNS 服务器从而节省时间了。

HTTP请求瓶颈点

最后，关于 HTTP 请求，它最大的瓶颈点来自请求阻塞。

所谓请求阻塞，就是浏览器为了保证每个资源的访问速度，会对同一域下的资源保持一定的连接数，当连接数过多就会进行阻塞来保证每个资源的访问速度。

通常来说，浏览器对于同一域名的连接数限制在6个左右。这意味着如果一个页面需要加载多个资源（比如CSS文件、JavaScript文件、图片等），并且这些资源都位于同一个域名下，那么只有少量的连接可以同时进行，其余的请求需要等待前面的请求完成才能进行。

因此做一些域名规划很重要，我们可以先观察当前页面需要用到哪些域名，最关键的首屏中需要哪些域名，规划下域名的发送顺序。

除此之外，还可以做域名扩散，通过不同的域名，增加请求并行连接数。

例如：将静态服务器得知 pic.google.com，做成支持 pic0-5 的六个域名，每次请求时随机选一个域名地址进行请求，因为有六个域名同时可用，最多可以并行36个连接。

当然域名不是越多越好，太多又会涉及到多域名无法缓存的问题。

2.1.2 服务端处理请求阶段

服务端数据处理阶段是指 WebServer 接受到请求后，从数据存储层取到数据，再返回到前端的过程。

服务端程序在接收到 HTTP 请求后，会做一些请求参数处理及权限校验，完成后，它会把请求参数发送到存储服务，然后服务端程序会从数据存储中取到数据进行加工聚合处理，最后再通过jsonp或者ajax接口返回给前端。

在上述过程的瓶颈点就在于是否做了数据缓存处理，是否做了Gzip压缩，以及是否有重定向。

Gzip压缩是一种压缩技术，通过 Gzip 压缩，资源的下载速度会快很多，能大大提升页面的展示速度。服务端通过 Gzip 压缩传输到浏览器端的大小可以变成原来的1/3 左右。大多情况下运维都会开启 Gzip压缩，所以本文就不再详细展开。

解下来，我会详细介绍数据缓存和重定向这两个瓶颈点。

数据缓存瓶颈点

为什么数据缓存会成为性能瓶颈点呢？

因为每请求一次数据接口，需要从客户端到后端服务器再到更后端的数据存储层，一层层的返回数据，最后给到客户端，这个时间一看就很长吧。如果能减少请求次数，显示时间就会减少。

数据缓存分为三种：

1. 借助 Service Worker 的数据接口缓存
2. 借助本地的接口缓存
3. CDN

Service Worker 是浏览器的一个高级属性，本质上是一个请求代理层，它存在的目的就是拦截和处理网络数据请求。如果没有该属性，需要走一次后端数据存取链路的流程，这会延迟页面打开的时间。

本地存储的接口缓存 是指在一些对数据时效性要求不高的页面，在第一次请求到数据后，将数据存储到本地存储，下一次请求时，先去缓存中取到数据，如果没有的话，再像服务器发起请求。

CDN 的基本思路是通过在网络各处放置节点服务器构造一个智能虚拟网络，将用户的请求导向离用户最近的服务节点上。例如：一个深圳的用户想要访问京东电商首图的资源，如果没有 CDN 他必须访问来自首都的服务器，但如果有，则选择离深圳最近的节点返回首图数据。

重定向瓶颈点

重定向是指将网站资源迁移到其他位置后，用户访问站点时，程序自动将用户请求从一个页面转移到另一个页面的过程。

在服务端处理时，主要分为三类：

1. 服务端发挥的 302 重定向
2. META 标签实现的重定向
3. 前端 JavaScript 通过 window.location 实现的重定向

在重定向的时候，都会引发新的 DNS 查询，导致新的 TCP 三次握手和 TLS 协商以及产生新的 HTTP 请求，也就是我们之前讲的，这些请求又会浪费更多时间进而影响前端性能。

2.1.3 页面解析和渲染阶段

当服务端对数据加工聚合处理后，客户端拿到数据，接下来就会进入解析和渲染阶段。

所谓解析就是 HTML 解析器把页面内容转换成 DOM 树 和 CSSOM 树的过程。

DOM 树全称为 Document Object Model 即文档对象模型。它描述了标签之间的层次和结构

CSSOM 树即 CSS 对象模型，主要描述样式集的层次和结构。html 遇到内联式style标签时，会触发css解析器对样式内容进行解析，与 DOM 树解析类似，按CSS 规则解析浏览器可解析和处理的样式集合，最终结合浏览器里面的默认样式汇总形成具有父子关系的 CSSOM 树。

显然如果使用外部式css，可以被浏览器缓存从而提高性能，若内联式则每次都要重新加载 CSSOM 树，但外部式又需要单独请求css文件，所以还要具体问题具体分析

所谓渲染就是主线程计算 DOM 节点的最终样式，生成布局树。布局树会记录参与页面布局的节点和样式。

接下来就是绘制，绘制就是把各个节点绘制到屏幕上的过程，绘制结果以层的方式保持。当文档中各个部分以不同的层绘制时，相互重叠，就必须合成以确保他们以正确的顺序绘制和展示。

以上就是解析和渲染，在这个过程中流程环节多，逻辑复杂，瓶颈点也多。比如：DOM 树构建过程，CSSOM 树生成阶段，重排和重绘过程等。

这里先介绍 DOM 树构建和布局两个环节的瓶颈点。

DOM 树瓶颈点

在构建 DOM 树的过程中，有三点会影响前端性能：

1. 当 HTML 标签不满足 WEB 语义化时。
   当 HTML 标签不满足语义化时，浏览器就需要更多时间去解析 DOM 标签的含义。特别是解析器对标签的容错，比如：br 标签少了作为结束的 /、表格嵌套不标准、标签层次结构复杂。遇到上述情况，浏览器会进行语法纠错，这就会导致页面总的解析和渲染阶段需要更长时间，从而影响页面展示性能。
2. DOM 节点数量过多
   当节点数过多，构建 DOM 树的时间就会变长，从而延长解析时间，拖慢页面展示速度。
3. 文档中包含 <script> 标签时
   无论是 DOM 或者是 CSSOM 都可以被 JavaScript 所访问并修改，所以一旦在页面解析遇到 <script> 标签，DOM 的构造过程就会暂停，等待服务器请求脚本。随后再构建 CSSOM 树之后再继续执行。而这段时间页面呈白色，所以外部的 <script> 常被称为 “解析” 阶段的拦路虎。有时就因为解析过程多了个 <script> 造成页面解析阶段从 200ms 变成了 1s。
   所以外部脚本的加载时机一定要确定好，能够延迟加载就选用延迟加载。（所以一般不放在head中，放到body的底部以避免对页面渲染的阻塞）
   另外我们可以通过 defer 和 async 告知浏览器在等待脚本下载期间不阻止解析过程。

布局中的瓶颈点（重绘重排）

浏览器会根据样式解析器给出的样式规则来计算某个元素需要占据的空间大小和屏幕中的位置，借助计算结果来进行布局。

而主线程布局时使用的是流模型的布局方式（从左到右、从上到下）。

假设我们在页面渲染过程中修改了一个元素的属性，例如突然加入了一条广告，这时布局阶段收到了影响。浏览器此时必须检查所有其他区域的元素，然后自动重排页面，重绘收到影响的元素，在这个过程中相当于整个渲染流程再来了一遍。

除此之外，因为浏览器每次布局计算都要计算整个 DOM ，如果元素量大，计算出所有元素的位置和尺寸会花很长时间，所以布局阶段容易称为性能瓶颈点。

例如：在某列表页性能优化项目时，一开始布局时没有确定列表页图片的初始大小，只给定了一个基础的占位尺寸。

上述情况中，在图片加载完毕后，主线程才知道了图片的大小，这时不得不重新进行布局计算，然后再进行页面渲染，也就是重排或重绘，这大大导致了页面展示时间。

下面我们根据所讲得布局来具体描述下重绘和重排：

1. 重排（Reflow）：
   - 当页面的布局或几何属性发生变化时，浏览器会触发重排操作。这些变化包括但不限于：
     - 添加、删除、修改 DOM 元素。
     - 修改 DOM 元素的位置、大小、内容。
     - 修改元素的样式，包括修改 CSS 属性（如 width、height、margin、padding 等）。
     - 页面的初始化渲染。
   - 重排会导致浏览器重新计算页面的布局和样式，然后重新绘制整个页面，是一种较为昂贵的操作。
2. 重绘（Repaint）：
   - 当页面的样式发生变化但不影响布局时，浏览器会触发重绘操作。这些变化包括但不限于：
     - 修改元素的颜色、背景色、文本样式等。
     - 添加或删除 CSS 类。
     - 修改元素的 visibility、opacity、transform 等属性。
   - 重绘会触发浏览器重新绘制页面的部分内容，但不会触发页面的重新布局，因此比重排的成本低。

在实际开发中，应该尽量避免频繁触发重绘和重排操作，因为它们会影响页面的性能。为了减少重绘和重排的次数，可以采取以下措施：

- 避免在循环中频繁修改样式。
- 使用 CSS3 动画和过渡代替 JavaScript 操作样式。
- 使用 CSS3 的 transform 和 opacity 属性进行动画，因为它们可以利用 GPU 加速，减少重排和重绘的开销。
- 使用 requestAnimationFrame() 方法代替 setTimeout() 或 setInterval() 方法来执行动画，以便浏览器在下一次重绘前执行动画操作。
- 将修改样式的操作集中到一起，减少多次修改样式的次数。
- 避免频繁操作 DOM，尽量减少对 DOM 的读写操作。

2.2 后话

页面加载全过程很复杂，内容也很多，这里只是简单介绍了常见几种瓶颈点。

除此之外，在偏硬件领域，像 GPU 绘图、操作系统 GUI 和 LCD 显示等瓶颈点；网络层和服务层，如拥塞预防、负载均衡和慢启动；还有一些页面解析和渲染的算法，如解析算法、标记化算法和树构建算法，等等

三、指标采集

3.1 首屏指标采集

在采集首屏时间时，如果在内网通过 DevTools 观察首屏时间，显然因为内外网环境不同、调试工具访问导致的访问方式不同，导致和正常用户所实际得到的首屏时间不同。

但是现实中，用户的网络环境不同、使用的手机型号不同，如此复杂的环境我们该如何了解用户的首屏时间？如何得到大部分用户首屏时间的时间分布？如何得到性能差的用户的首屏时间呢？

以下将讲述几种方法来采集首屏指标。

3.1.1 手动采集

所谓手动采集，一般是通过埋点的方式进行。

比如，在页面开始的地方打上 FMP.Start() ，在首屏结束的地方打上 FMP.End() ，通过两者相减得到首屏时间。

FMP(First Meaningful Paint) 是指页面的主要内容出现在屏幕所需的时间

优点：

- 兼容性强，可以随情况变动
- 去中心化，各个业务负责自己的打点代码，有问题业务人员自己去排查即可

缺点：

- 手动采集会和业务代码严重耦合
- 覆盖率不足，业务一旦忙起来性能优化方案就会延迟排后

3.1.2 自动化采集

自动化采集即引入一段通用的代码来做首屏时间自动化采集，引入过程中，除了必要的配置不需要做其他事情。目前业界还是采用自动化采集为主。

优点：

- 独立性更强，接入过程更自动化

缺点：

- 个性化需求无法满足

首屏自动化采集，需要考虑是服务端模板业务，还是单页面（SPA）应用开发业务，业务场景不同，对应的采集方法也不同。

服务端模板业务的采集方法

虽然目前主要还是以 Vue 和 React 这些单页面应用为主，但是在一些 B端业务公司，如 Velocity、Smarty等还是使用的服务端模板，同时 SSR 用的也是服务端模板。

服务端的页面加载的流程又是什么样的呢？

大致是：HTTP 请求 - > HTML 文档加载解析完毕 - > 加载样式和脚本方法 - > 完成页面渲染



上述过程中，HTML 文档加载解析完毕的时间点就是首屏时间点，要采集这个时间，可以使用浏览器提供的 DOMContentLoaded 接口来实现



上图中在开发者工具中获得的 DOMContentLoaded 就是首屏时间。

当页面中的 HTML 元素被加载和解析完成，DOMContentLoaded 事件触发，此时记录下当时的时间 DOMContentLoadedEventEnd，再减去页面初始化的时间 fetchStart。这就是 DOMContentLoaded 时间也就是首屏时间。

单页面应用的采集方法

在上述讲述的 DOMContentLoaded 只能用于服务端模板，并不能用于单页面。

就以vue为例：用户请求一个页面时，页面会先去加载 index.html ，加载完成后，就会触发 DOMContentLoaded 和 load，但是此时页面展示只是空白页，并不是真正意义上的首屏。接下来页面会加载相关脚本资源并通过 axios 异步请求数据，使用数据渲染页面主题部分，这个时候首屏才算真正渲染完成。

可见 SPA 并不能使用 Performance 接口来正常采集首屏时间，那么在这种情况下我们该如何采集首屏时间呢？

答案是使用 MutationObserver 采集首屏时间。

MutationObserver 接口提供了监视对 DOM 树所做更改的能力，它被设计为旧的 Mutation Events 功能的替代品，该功能是 DOM3 Events 规范的一部分

在用户进入页面时，我们可以使用 MutationObserver 监控 DOM 元素，当 DOM 元素发生变化时，程序会标记变化的元素，记录时间点和分数，存储到数组中。

考虑到首屏采集到某些条件时，渲染就已经结束了，所以我们要考虑首屏采集终止的条件，例如：计算时间超过30s、计算了四轮且一秒内分数不变化、计算九次且分数不变化。

递归遍历 DOM 元素及其子元素，根据子元素所在层数设定元素权重。

由于每个 DOM 元素对于首屏的意义是不同的，越往内层越接近真实的首屏内容，所以我们给出的每个元素的得分为： 层数 * 0.5 + 1

根据前面的得分，计算元素的分数变化率，获取变化率最大点对应的分数，然后找到该分数对应的时间，即为FMP。

但是如果包含图片，上述方法采集就不准确了。

因为上述计算逻辑主要针对 DOM 元素，但图片加载属于异步，图片容器（图片的 DOM元素）和内容的加载是分开的，当容器加载出来时，内容还没加载出来，一定要确保内容被加载出来才算首屏。

所以我们需要补充逻辑：

获取所有的图片路径（分两种：一种为 img标签；一种是 css属性中的 background值；前者可以直接通过src获取，后者可以通过 window.getComputedStyle 获取样式集合），接下来通过正则获取图片的路径即可，随后通过 performance.getEntriesByName()，可以获取图片下载的时间，随后与之前的计算的时间比较，哪个时间长哪个就是首屏时间。



