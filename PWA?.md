# 什么是PWA
Progressive Web App, 简称 PWA，是提升 Web App 的体验的一种新方法，能给用户原生应用的体验。

PWA 能做到原生应用的体验不是靠特指某一项技术，而是经过应用一些新技术进行改进，在安全、性能和体验三个方面都有很大提升，PWA 本质上是 Web App，借助一些新技术也具备了 Native App 的一些特性，兼具 Web App 和 Native App 的优点。
PWA 的主要特点包括下面三点：
* 可靠 - 即使在不稳定的网络环境下，也能瞬间加载并展现
* 体验 - 快速响应，并且有平滑的动画响应用户的操作
* 粘性 - 像设备上的原生应用，具有沉浸式的用户体验，用户可以添加到桌面
PWA 本身强调渐进式，并不要求一次性达到安全、性能和体验上的所有要求，开发者可以通过 [PWA Checklist](https://developers.google.cn/web/progressive-web-apps/checklist) 查看现有的特征。

# PWA中的一些技术
1. Web App Mainfest
  * Manifest 的目的是将Web应用程序安装到设备的主屏幕，为用户提供更快的访问和更丰富的体验。
  * Manifest是一个JSON格式的文件，一个指定了Web App桌面图标、名称、开屏图标、运行模式等一系列资源的一个清单。
   ```
   <!-- 在index.html中添加以下meta标签 -->
   <link rel="manifest" href="/manifest.json">
   ```
   * 浏览器兼容
   ![浏览器兼容](./pwa-imgs/maifes.webp)
2. Service Worker
  * 一个独立于前端页面，在后台运行的进程。
  * 让我们的Web App在无网（offline）情况下可以访问，甚至使用部分功能，而不是展示“无网络连接”的错误页。
  * 让我们在弱网的情况下，能使用缓存快速访问我们的应用，提升体验。
  * 在正常的网络情况下，也可以通过各种自发控制的缓存方式来节省部分请求带宽
  * 浏览器兼容
   ![浏览器兼容](./pwa-imgs/service-worker.webp)
3. Push & Notification 推送与通知
  * 可以通过这两个API推送消息
  * 浏览器可以向push server发起订阅，订阅后将订阅信息发送给服务端，服务端根据Web Push 协议通知Push Service, Push Server 效验后推送给已订阅的客户端
   ![push](./pwa-imgs/push-flow.webp)
  * 浏览器兼容
   ![浏览器兼容](./pwa-imgs/push.webp)

# 可靠
当用户打开我们站点时（从桌面 icon 或者从浏览器），通过 [Service Worker](https://developers.google.cn/web/fundamentals/primers/service-workers/) 能够让用户在网络条件很差的情况下也能瞬间加载并且展现。

Service Worker 是用 JavaScript 编写的 JS 文件，能够代理请求，并且能够操作浏览器缓存，通过将缓存的内容直接返回，让请求能够瞬间完成。开发者可以预存储关键文件，可以淘汰过期的文件等等，给用户提供可靠的体验。
# 体验
如果站点加载时间超过 3s，53% 的用户会放弃等待。页面展现之后，用户期望有平滑的体验，过渡动画和快速响应。

为了保证首屏的加载，我们需要从设计上考虑，在内容请求完成之前，可以优先保证 App Shell 的渲染，做到和 Native App 一样的体验，[App Shell](https://developers.google.cn/web/fundamentals/architecture/app-shell) 是 PWA 界面展现所需的最小资源。
# 粘性
* PWA 是可以安装的，用户点击安装到桌面后，会在桌面创建一个 PWA 应用，并且不需要从应用商店下载

* PWA 可以借助 Web App Manifest 提供给用户和 Native App 一样的沉浸式体验

* PWA 可以通过给用户发送离线通知，让用户回流
Web App Manifest 允许开发者控制 PWA 添加到桌面，允许定制桌面图标、URL等等。
# 其他
上面讲到 PWA 是兼具 Web App 和 Native App 的特征的，Web App 无版本问题、可索引也是很重要的特性。

总结，PWA 具有下面一些特性：
* **渐进式** - 适用于所有浏览器，因为它是以渐进式增强作为宗旨开发的
* **连接无关性** - 能够借助 Service Worker 在离线或者网络较差的情况下正常访问
* **类似应用** - 由于是在 App Shell 模型基础上开发，因为应具有 Native App 的交互和导航，给用户 Native App 的体验
* **持续更新** - 始终是最新的，无版本和更新问题
* **安全** - 通过 HTTPS 协议提供服务，防止窥探和确保内容不被篡改
* **可索引** - 应用清单文件和 Service Worker 可以让搜索引擎索引到，从而将其识别为『应用』
* **粘性** - 通过推送离线通知等，可以让用户回流
* **可安装** - 用户可以添加常用的 webapp 到桌面，免去去应用商店下载的麻烦
* **可链接** - 通过链接即可分享内容，无需下载安装
PWA 是对站点体验的一个飞跃式的提升，我们会从安全、性能和体验三个角度来分析如何打造一个完善的 PWA。

# Service Worker和HTTP缓存
1、改写默认行为。
例如，浏览器默认在刷新时，会对所有资源都重新发起请求，即使缓存还是有效期内，而使用了SW，就可以改写这个行为，直接返回缓存。

2、缓存和更新并存。
要让网页离线使用，就需要整站使用长缓存，包括HTML。而HTML使用了长缓存，就无法及时更新（浏览器没有开放接口直接删除某个html缓存）。而使用SW就可以，每次先使用缓存部分，然后再发起SW js的请求，这个请求我们可以实施变更，修改HTML版本，重新缓存一份。那么用户下次打开就可以看到新版本了。

3、无侵入式。
无侵入式版本控制。最优的版本控制，一般是HTML中记录所有js css的文件名（HASH），然后按需发起请求。每个资源都长缓存。而这个过程，就需要改变了项目结构，至少多一个js或者一段js控制版本号，发起请求时还需要url中注入冬天的文件名。使用了SW，就可以把这部分非业务逻辑整合到sw js中。
无侵入式请求统计。例如缓存比例统计、图片404统计。

4、额外缓存。
HTTP缓存空间有限，容易被冲掉。虽然部分浏览器实现SW的存储也有淘汰机制，但多一层缓存，命中的概率就要更高了

5、离线处理。
当监测到离线，而且又没有缓存某个图片时，可以做特殊处理，返回离线的提示。又或者做一个纯前端的404/断网页面。类似Chrome的小恐龙页面。


[lavas](https://lavas.baidu.com/pwa/README)

[Service Worker 应用详解](https://lzw.me/a/pwa-service-worker.html)
