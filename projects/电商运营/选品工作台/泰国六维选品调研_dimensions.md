# 泰国六维选品调研 — 研究维度

research_question: 在TikTok Shop泰国跨境店可售范围内，用六个数据维度交叉验证求最大公约数，找出既符合"一件代发/不押库存/轻小件/无强制认证"硬约束、又能最快跑通第一单并开始测品的选品方向与具体单品。

## Scope
time_scope: 2025-11 至 2026-11（近12个月为主），近3个月趋势（2026-08至2026-11）；无时间窗的数据用最新可得年度并注明
geographic_scope: 需求侧=泰国市场；供给侧=中国全国（重点广东、浙江、福建产业带）
target_object: TikTok Shop泰国跨境店可售范围内的六维交叉验证选品方向与单品
decision_intent: 找到"需求确定性 × 可执行性 × 测品效率"最大公约数的选品方向，跑通第一单并开始测品
exclusions: 美妆个护(FDA限售)、个护电器(定邀)、美甲套件(暂不支持)、香水(暂不支持)、隐形眼镜护理液/卫生棉条/月经杯(暂不支持)、纹身器械(暂不支持)、按摩椅(暂不支持)、母婴(需合规文件)、食品饮料/补充剂/宠物食品、智能设备/手机配件/珠宝配饰(定邀)
ambiguities: 9610出口数据粒度可能无法精确到泰国单品级别，用中国海关分类数据替代；LINE/YouTube搜索排名可获取性有限，以Google Trends替代；"生活场景"排名无法直接获得，用搜索热词推断

### dim_01: TikTok跨境集货仓周边产业带成熟品类
Entity: 
Must-have: yes
Description: TikTok官方跨境分拨仓（东莞、义乌、上海、泉州、金华）周边3-50km辐射范围内的产业带，其成熟产业品类有哪些与泰国消费需求匹配
Sources hint: 1688产业带数据、义乌小商品/东莞制造/泉州鞋服产业带报告、跨境集货仓实测数据

### dim_02: 泰国各电商平台销量前十单品
Entity: 
Must-have: yes
Description: TikTok Shop泰国站、Shopee泰国站、Lazada泰国站的各品类/单品销量前十榜单（非限售品类），找出高频出现的品类交集
Sources hint: TikTok Shop泰国销量榜、Shopee Thailand Best Sellers、Lazada Thailand Top Sales、FastMoss/Kalodata等第三方数据

### dim_03: 泰国搜索引擎生活场景排名
Entity: 
Must-have: yes
Description: Google泰国、Google Trends泰国、LINE搜索、YouTube搜索中与"生活场景"相关的热搜主题及其隐含的消费需求
Sources hint: Google Trends Thailand、Thai search trends、LINE trending topics Thailand

### dim_04: 泰国社交平台话题/内容创作相关商品
Entity: 
Must-have: yes
Description: TikTok泰国、Facebook泰国、Instagram泰国、X(Twitter)泰国、LINE热门话题中涉及的具体商品或品类
Sources hint: TikTok Thailand trending、Facebook Thailand trending、social commerce Thailand

### dim_05: 中国出口泰国商品金额/数量前十
Entity: 
Must-have: yes
Description: 中国海关统计的中国对泰国出口商品分类中金额/数量排名前十的品类
Sources hint: 中国海关总署统计数据、UN Comtrade、TradeMap中国-泰国贸易数据

### dim_06: 中国9610监管方式出口泰国最多的产品
Entity: 
Must-have: yes
Description: 9610跨境直购出口监管方式下，出口泰国的最多产品品类
Sources hint: 中国海关9610数据、跨境电商出口统计、地方商务局跨境电商报告
