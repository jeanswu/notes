# 微信公众号「万里Tk」文章列表抓取结果

- 抓取时间：2026-11-03 06:26–06:45（北京时间）
- 目标：拿到全部 30 篇原创文章的「标题 + 链接 + 发布日期」列表（不抓正文）
- 执行环境：agent-browser（云主机真实 Chrome，headed，iPhone 14 设备模拟，独立会话 wx5/wx6/wx7）

---

## 一、结论先说

**没拿到列表。全部方法失败，卡死在微信的服务端 session 校验上。**

- 拿到文章数：**1 / 30**
- 成功方法：**无**（唯一那条已知文章本身可以正常打开，但无法由它扩散出列表）
- 是否撞验证码/登录墙：**是**，两类都撞了
  1. 微信官方「登录墙」：`profile_ext`（公众号主页）返回 `Open link in Weixin.` / `请在微信客户端打开链接。`
  2. 搜狗微信「验证码」：`weixin.sogou.com` 跳 antispider，要求点击图片验证码
- 是否调用 `browser_wait_user_action`：**没有调用**（原因见第五节：这两堵墙都不是「人点一下就能过」的类型，见下方分析）

---

## 二、账号元信息（以下均为页面实测，非推测）

| 字段 | 值 | 来源 |
|---|---|---|
| 公众号名 | 万里Tk | 文章页 `#js_name` + `cgiDataNew.nick_name` |
| 简介 | 为泗阳本地跨境献出一点绵薄之力！ | `cgiDataNew.biz_card.list[0].signature` |
| biz（fakeid） | `MzcxMDQ0NzgzNw==` | `cgiDataNew.bizuin`（与已知参数一致，已核验） |
| 微信号（alias） | `wuwanli18360208036` | `cgiDataNew.alias` |
| 原始ID | `gh_55c8d50cf100` | `cgiDataNew.user_name` |
| 原创篇数 | **30** | `cgiDataNew.biz_card.list[0].orignal_num = 30`（与文末卡片「30篇原创内容」吻合） |
| 合集（album） | **无** | `cgiDataNew.topic_infos = []`；文章正文与 DOM 内无「合集」标签（已搜 `合集` 关键字，命中 0） |

> 说明：该号**没有建任何「合集/话题」**，所以方法 C（走合集页拿整个系列）在这号上从一开始就不存在，不是被墙，是确实没有。

---

## 三、已获取文章（唯一 1 篇，字段齐全且已核验）

| 序号 | 标题 | 完整链接 | 发布日期 | 备注 |
|---|---|---|---|---|
| 1 | 做 TikTok 不出单 - 2：新手破局核心，把测品跑通，出单自然来 | https://mp.weixin.qq.com/s/5sNYFFeMxbADH14agA1wZA | 2026-10-23 17:53 | IP 属地：中国·江苏；`create_timestamp=1792749200`；`mid=2247483959`，`idx=1` |

**第 1 篇、第 3 篇及以后：未获取。**

---

## 四、方法尝试全记录（按尝试顺序，含失败原文）

| # | 方法 | 具体动作 | 结果 | 页面提示原文 |
|---|---|---|---|---|
| A | 直接访问公众号主页 | `mp.weixin.qq.com/mp/profile_ext?action=home&__biz=MzcxMDQ0NzgzNw==&scene=124#wechat_redirect` | ❌ 失败 | 页面标题 `Verify`，正文 `Open link in Weixin.`（截图 `A_profile_blocked.png`） |
| A+ | 主页加完整客户端参数 + 中文语言 | `&scene=110&uin=&key=&pass_ticket=&devicetype=&version=&lang=zh_CN&nettype=WIFI&a8scene=0&wx_header=1` | ❌ 失败 | 标题 `验证`，正文 `请在微信客户端打开链接。` |
| A++ | 伪造微信内置浏览器 UA | `set headers '{"User-Agent":"…MicroMessenger/8.0.45…"}'` 后再访问主页 | ❌ 失败 | UA 未被覆盖（实测 `navigator.userAgent` 仍为 Safari），页面照旧提示 `请在微信客户端打开链接。` |
| B | 从已知文章点文末公众号卡片进主页 | 打开已知文章 → 滚动到底 → 找到 `#js_name`（`<a>` 文本「万里Tk」，href 为空，靠 JS 唤起）→ `click()` 唤起 `#js_profile_card` | ❌ 失败 | 卡片容器 `#js_profile_card` 存在但 `innerHTML` 为空（未渲染）；该卡片的落地页本身就是 `profile_ext`，等于回到方法 A 的墙 |
| C | 走「合集」页 | 检查 `topic_infos`、`related_tag`、`appmsgalbuminfo`、DOM 内 `[class*=album]` | ❌ 不存在 | 该号**未建合集**（`topic_infos=[]`，页面无「合集」字样），此路客观不存在 |
| D | HTML 源码挖候选 URL | 在文章 HTML（约 4.48 MB）里 grep `appmsgalbum` / `album_id` / `profile_ext` / `homepage` / `mp.weixin.qq.com/s…` | ⚠️ 挖到但全不可用 | 命中的都是前端 JS 库代码（`openAllVideoPage()`、`appmsgalbuminfo` 变量名），无真实 album_id；正文内唯一另一条 `mp.weixin.qq.com/s/_2kC-fXw7UjneZSrsC9CVQ` 是「内容存在争议·了解更多」的系统横幅链接，与本号无关 |
| E | 列表页「查看更多/加载更多」 | — | ❌ 无对象 | 列表页从未成功打开过，不存在可点的「加载更多」 |
| F | 文末「往期推荐/上一篇下一篇」链式爬取 | 扫描已知文章页全部 `a[href]` 与 HTML 内所有 `mp.weixin.qq.com/s` 串 | ❌ 失败 | 该文章页**没有任何指向其他文章的内链**（`links: []`），`related_article_info = {"has_related_article_info":0}`，链式爬取起点就断了 |
| G | 直接打 JSON 列表接口 | `mp.weixin.qq.com/mp/profile_ext?action=getmsg&__biz=…&f=json&offset=0&count=10&is_ok=1&scene=124&uin=&key=&pass_ticket=` | ❌ 失败（**决定性证据**） | 返回：`{"base_resp":{"ret":-3,"errmsg":"no session","cookie_count":0},"ret":-3,"errmsg":"no session","cookie_count":0}` |
| H | 在文章页同源 XHR 再试（带 referer/cookie） | 在已加载文章页内 `fetch(getmsg 接口, {credentials:"include"})` | ❌ 失败 | 同 G：`ret:-3 no session cookie_count:0`；该页 `document.cookie` 只有 `rewardsn=`，无 `uin/key/pass_ticket` |
| I | 长链参数爆破 | `mp.weixin.qq.com/s?__biz=MzcxMDQ0NzgzNw==&mid=2247483959&idx=1`（及 mid-1） | ❌ 失败 | 页面正文 `Parameter error`（缺 `sn`，`sn` 无法推算） |
| J | 其他公开入口 | `mp/homepage?…&hid=1`、`mp/appmsgalbum?action=getalbum_list`、`mp/appmsgalbum?action=getalbum&album_id=0` | ❌ 失败 | 分别返回 `页面模板不存在`（该号未配自定义菜单文章模板）、`Open link in Weixin.`、`Unknown error. Try again later.` |
| K | 搜狗微信搜索（找号→找历史文章） | `weixin.sogou.com/weixin?type=1&query=万里Tk` | ❌ 验证码 | 跳 `antispider`，正文：`此验证码用于确认这些请求是您的正常行为而不是自动程序发出的…请依次点击【伤,告,悍,时】`（截图 `sogou_captcha.png`）。三次尝试 IP 每次都变（118.197.80.23 → .81.8 → .81.6），验证码与 IP 绑定，人工解一次也很可能被下一次请求的换 IP 作废 |
| L | 搜索引擎收录 | Bing `site:mp.weixin.qq.com 万里Tk`、Baidu `"万里Tk"`、Baidu `"做TikTok不出单"`、360 `万里Tk 公众号` | ❌ 无结果 | Bing 返回全是「万里」重名结果（万里副总理、万里汇等），**无一条本号文章**；Baidu/360 同样 0 条本号文章。该号未被搜索引擎收录 |
| M | 第三方镜像站 | `freewechat.com/profile/MzcxMDQ0NzgzNw==`、`wemp.app`、`gzhshoulu.wang`、`wxnmh.com` | ❌ 不可用 | freewechat / gzhshoulu / wxnmh 域名不可达（curl 超时 000，CDP `Page.navigate` 超时）；wemp.app 已变成博彩/成人广告站（跳 `lp1493.kkzzw.com`） |
| N | RSS 服务 | `wechat2rss.xlab.app/list/all.html` 全量公众号列表 | ❌ 无收录 | 列表内检索「万里」命中 0 条，该服务只收安全/技术类号 |

---

## 五、卡点本质（为什么人点一下也过不了）

1. **微信官方墙是「会话墙」，不是「登录页」。** `action=getmsg` 明确返回 `ret=-3 / errmsg:"no session" / cookie_count:0`。也就是说：不是缺一个验证码，而是缺一整套 `uin + key + pass_ticket`（只能由微信客户端在打开该公众号主页时生成，且 20 分钟左右失效）。
2. **所以没有调用 `browser_wait_user_action`。** 该工具适用于「扫码/输密码/短信验证」这类人工可完成的步骤；而这里即使主人亲手在浏览器里点，也点不出 session —— 浏览器里没有微信客户端身份。用它会变成让主人白忙一趟。
3. **搜狗那堵墙是「验证码 + IP 轮换」双重墙。** 云主机出口 IP 每次请求都在变（已实测 3 个不同 IP），而搜狗验证码是绑 IP + VerifyCode 的，人工解完下一次请求就换了 IP，大概率被判废；且搜狗微信本身对公众号只展示最近 10 篇左右，也补不齐 30 篇。
4. **这个号是「信息孤岛」**：没建合集、文章之间无内链、未被任何搜索引擎或镜像站收录。所以除了微信官方接口，世界上没有第二个地方有它的完整列表。

---

## 六、建议的解封路径（按性价比排序，需主人出手）

**路径 1（最省事，推荐）：主人用自己微信打开这个公众号主页，把链接复制出来给我。**
- 操作：微信里进入「万里Tk」公众号 → 右上角「…」→ 复制链接
- 复制出的链接形如 `https://mp.weixin.qq.com/mp/profile_ext?action=home&__biz=MzcxMDQ0NzgzNw==&scene=124&uin=xxx&key=xxx&pass_ticket=xxx…`
- 拿到后我用 `action=getmsg&offset=0/10/20&count=10` 分页，**30 篇一次拉全**（标题、`mp.weixin.qq.com/s/…` 短链、发布时间都在返回 JSON 里）
- 注意：`key` 约 20 分钟失效，链接要现取现用

**路径 2：主人在微信里连续「转发给自己」几篇文章，把转发出的链接发我。**
- 每篇能拿到短链 + 标题 + 日期；30 篇需主人手动转发，成本高，只能兜底

**路径 3：主人若认识该号运营者**，让对方在公众号后台「内容管理 → 已发表」导出/截图，最快最全

---

## 七、截图证据

目录：`/Coze/Drive/电商运营/browser/screenshots/wechat_list/`

| 文件 | 内容 |
|---|---|
| `A_profile_blocked.png` | 方法 A：公众号主页被拦，页面只有 `Open link in Weixin.` |
| `sogou_captcha.png` | 方法 K：搜狗微信 antispider 验证码页（含 IP / VerifyCode / 待点字符） |
| `article_card_evidence.png` | 已知文章整页截图（文末公众号卡片「万里Tk」所在位置，可作 30 篇原创的旁证） |

---

## 八、未做的事（明确声明，避免误读）

- **未编造任何文章标题、链接、日期。** 表格里只有 1 篇，就是核验过的那 1 篇。
- 未抓取任何文章正文（按要求只取列表）。
- 未尝试微信网页版（`wx.qq.com`）扫码登录：该入口对绝大多数账号已限制登录，且需要主人手机配合，成功率低、打扰成本高，故未擅自发起。
