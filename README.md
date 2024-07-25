## Clash分流规则

### 文件解释
 
[DIRECT](https://github.com/TrueRou/Clash-Rules/tree/master/DIRECT) 内所有配置文件均为直连规则  
[PROXY](https://github.com/TrueRou/Clash-Rules/tree/master/PROXY) 内所有配置文件均为代理规则  
[REJECT](https://github.com/TrueRou/Clash-Rules/tree/master/REJECT) 内所有配置文件均为屏蔽规则

### 使用方法

#### Clash 订阅规则

```yaml
rule-providers:
  ChinaCloudServiceProvider:   # 直连-中国各类云服务商IP.
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaCloudServiceProvider.yaml"
    path: ./ruleset/ChinaCloudServiceProvider.yaml
    interval: 1800
    
  ChinaDomain: # 直连-中国常见域名
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaDomain.yaml"
    path: ./ruleset/ChinaDomain.yaml
    interval: 1800
    
  ChinaIP: # 直连-中国IP
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaIP.yaml"
    path: ./ruleset/ChinaIP.yaml
    interval: 1800
    
  ChinaVideo: # 直连-中国媒体列表
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaVideo.yaml"
    path: ./ruleset/ChinaVideo.yaml
    interval: 1800
    
  DownloadClient: # 直连-中国常用下载客户端
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/DownloadClient.yaml"
    path: ./ruleset/DownloadClient.yaml
    interval: 1800
    
  GoogleCN: # 直连-中国可以直连的Google地址
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/GoogleCN.yaml"
    path: ./ruleset/GoogleCN.yaml
    interval: 1800
    
  LocalAreaNetwork: # 直连-局域网地址
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/LocalAreaNetwork.yaml"
    path: ./ruleset/LocalAreaNetwork.yaml
    interval: 1800
    
  Netflix:   # 代理-奈菲.
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/Netflix.yaml"
    path: ./ruleset/Netflix.yaml
    interval: 1800
    
  OneDrive:   # 代理-OneDrive.
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/OneDrive.yaml"
    path: ./ruleset/OneDrive.yaml
    interval: 1800
    
  ProxyGWFList:   # 代理-GWFList.
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/ProxyGWFList.yaml"
    path: ./ruleset/ProxyGWFList.yaml
    interval: 1800
    
  ProxyVideo:   # 代理-国外媒体列表.
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/ProxyVideo.yaml"
    path: ./ruleset/ProxyVideo.yaml
    interval: 1800
    
  Telegram:   # 代理-Telegram.
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/Telegram.yaml"
    path: ./ruleset/Telegram.yaml
    interval: 1800
    
  BanAD:   # 屏蔽-广告联盟.
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/REJECT/BanAD.yaml"
    path: ./ruleset/BanAD.yaml
    interval: 1800

  BanEasyListChina: # 屏蔽广告
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/REJECT/BanEasyListChina.yaml"
    path: ./ruleset/BanEasyListChina.yaml
    interval: 1800
    
  ChinaApp: # 需要直连的常见软件列表
    type: http
    behavior: classical
    url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaApp.yaml"
    path: ./ruleset/ChinaApp.yaml
    interval: 1800
```

#### Clash for Windows 使用 Parsers 预处理文件
```yaml
parsers: # array
  # - reg: ^.*$ 匹配所有订阅，或  - url: https://example.com/profile.yaml 指定订阅
  - reg: ^.*$
    # 删除服务商提供的策略组和规则
    code: |
      module.exports.parse = (raw, { yaml }) => {
        const rawObj = yaml.parse(raw)
        const groups = []
        const rules = []
        return yaml.stringify({ ...rawObj, 'proxy-groups': groups, rules })
      } 
    # 建立自己的配置
    yaml:
      prepend-proxy-groups: # 建立策略组
        - name: 🔯 代理模式
          type: select
          proxies:
            - 绕过大陆丨黑名单 # 黑名单模式，意为「只有命中规则的网络流量，才使用代理」
            - 绕过大陆丨白名单 # 白名单模式，意为「没有命中规则的网络流量，统统使用代理」

        - name: 🔰 选择节点
          type: select

        - name: 🛑 广告拦截
          type: select
          proxies:
            - DIRECT
            - REJECT
            - PROXY

        - name: Ⓜ️ OneDrive
          type: select
          proxies:
            - DIRECT
            - PROXY

        - name: 🔰 Emby
          type: select
          proxies:
            - DIRECT
            - PROXY

        - name: 绕过大陆丨黑名单
          type: url-test
          url: http://www.gstatic.com/generate_204
          interval: 86400
          proxies:
            - DIRECT

        - name: 绕过大陆丨白名单
          type: url-test
          url: http://www.gstatic.com/generate_204
          interval: 86400
          proxies:
            - PROXY

        - name: PROXY
          type: url-test
          url: http://www.gstatic.com/generate_204
          interval: 86400
          proxies:
            - 🔰 选择节点

        - name: ⚖️ 负载均衡-散列
          type: load-balance
          url: 'http://www.google.com/generate_204'
          interval: 300
          strategy: consistent-hashing

        - name: ⚖️ 负载均衡-轮询
          type: load-balance
          url: 'http://www.google.com/generate_204'
          interval: 300
          strategy: round-robin

                  # 策略组示例
                  # - name: 分组名
                  # type: select       # 手动选点   
                  # url-test     # 自动选择延迟最低的节点
                  # fallback     # 节点故障时自动切换下一个
                  # laod-balance # 均衡使用分组内的节点
                  # url: http://www.gstatic.com/generate_204 # 测试地址 非select类型分组必要
                  # interval: 300 # 自动测试间隔时间，单位秒 非select类型分组必要
                  # tolerance: 50 # 允许的偏差，节点之间延迟差小于该值不切换 非必要
                  # proxies:  
          # - 节点名称或其他分组套娃

      commands:
        - proxy-groups.🔰 选择节点.proxies=[]proxyNames # 向指定策略组添加订阅中的节点名，可使用正则过滤
        - proxy-groups.🔰 选择节点.proxies.0+DIRECT # 向指定分组第一个位置添加一个 DIRECT 节点名
        - proxy-groups.⚖️ 负载均衡-散列.proxies=[]proxyNames
        - proxy-groups.1.proxies.0+⚖️ 负载均衡-散列
        - proxy-groups.⚖️ 负载均衡-轮询.proxies=[]proxyNames
        - proxy-groups.1.proxies.0+⚖️ 负载均衡-轮询
        # 一些可能用到的正则过滤节点示例，使分组更细致
        # []proxyNames|a                         # 包含a
        # []proxyNames|^(.*)(a|b)+(.*)$          # 包含a或b
        # []proxyNames|^(?=.*a)(?=.*b).*$        # 包含a和b
        # []proxyNames|^((?!b).)*a((?!b).)*$     # 包含a且不包含b
        # []proxyNames|^((?!b|c).)*a((?!b|c).)*$ # 包含a且不包含b或c

      # 添加规则
      prepend-rules: # 规则由上往下遍历，如上面规则已经命中，则不再往下处理
        - AND,(AND,(DST-PORT,443),(NETWORK,UDP)),(NOT,((GEOIP,CN,no-resolve))),REJECT
        - GEOSITE,Private,DIRECT
        - GEOSITE,Category-games@cn,DIRECT
        - RULE-SET,ChinaApp,DIRECT
        - DOMAIN,clash.razord.top,DIRECT
        - DOMAIN,yacd.haishan.me,DIRECT
        - RULE-SET,ChinaCloudServiceProvider,DIRECT
        - RULE-SET,ChinaDomain,DIRECT
        - RULE-SET,ChinaIP,DIRECT
        - RULE-SET,ChinaVideo,DIRECT
        - RULE-SET,DownloadClient,DIRECT
        - RULE-SET,GoogleCN,DIRECT
        - RULE-SET,LocalAreaNetwork,DIRECT
        - RULE-SET,Emby,🔰 Emby
        - RULE-SET,BanAD,🛑 广告拦截
        - RULE-SET,Netflix,PROXY
        - RULE-SET,OneDrive,Ⓜ️ OneDrive
        - RULE-SET,ProxyGWFList,PROXY
        - RULE-SET,ProxyVideo,PROXY
        - RULE-SET,Telegram,PROXY
        - GEOSITE,CN,DIRECT
        - GEOIP,CN,DIRECT,no-resolve
        - MATCH,🔯 代理模式 # 规则之外的
      # 添加规则集
      mix-rule-providers:
        ChinaCloudServiceProvider:   # 直连-中国各类云服务商IP.
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaCloudServiceProvider.yaml"
          path: ./ruleset/ChinaCloudServiceProvider.yaml
          interval: 1800

        ChinaDomain: # 直连-中国常见域名
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaDomain.yaml"
          path: ./ruleset/ChinaDomain.yaml
          interval: 1800

        ChinaIP: # 直连-中国IP
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaIP.yaml"
          path: ./ruleset/ChinaIP.yaml
          interval: 1800

        ChinaVideo: # 直连-中国媒体列表
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaVideo.yaml"
          path: ./ruleset/ChinaVideo.yaml
          interval: 1800

        DownloadClient: # 直连-中国常用下载客户端
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/DownloadClient.yaml"
          path: ./ruleset/DownloadClient.yaml
          interval: 1800

        GoogleCN: # 直连-中国可以直连的Google地址
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/GoogleCN.yaml"
          path: ./ruleset/GoogleCN.yaml
          interval: 1800

        LocalAreaNetwork: # 直连-局域网地址
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/LocalAreaNetwork.yaml"
          path: ./ruleset/LocalAreaNetwork.yaml
          interval: 1800

        Emby: # Emby
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/Emby.yaml"
          path: ./ruleset/ChinaApp.yaml
          interval: 1800

        Netflix:   # 代理-奈菲.
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/Netflix.yaml"
          path: ./ruleset/Netflix.yaml
          interval: 1800

        OneDrive:   # 代理-OneDrive.
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/OneDrive.yaml"
          path: ./ruleset/OneDrive.yaml
          interval: 1800

        ProxyGWFList:   # 代理-GWFList.
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/ProxyGWFList.yaml"
          path: ./ruleset/ProxyGWFList.yaml
          interval: 1800

        ProxyVideo:   # 代理-国外媒体列表.
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/ProxyVideo.yaml"
          path: ./ruleset/ProxyVideo.yaml
          interval: 1800

        Telegram:   # 代理-Telegram.
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/Telegram.yaml"
          path: ./ruleset/Telegram.yaml
          interval: 1800

        BanAD:   # 屏蔽-广告联盟.
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/REJECT/BanAD.yaml"
          path: ./ruleset/BanAD.yaml
          interval: 1800

        BanEasyListChina: # 屏蔽广告
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/REJECT/BanEasyListChina.yaml"
          path: ./ruleset/BanEasyListChina.yaml
          interval: 1800

        ChinaApp: # 需要直连的常见软件列表
          type: http
          behavior: classical
          url: "https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaApp.yaml"
          path: ./ruleset/ChinaApp.yaml
          interval: 1800
```

#### Clash Verge 使用 Script 预处理功能

- 注意：Clash Verge 为 Meta 内核，不支持 Parsers 预处理文件，因此无法使用上述 Parsers 配置方式。
- 但是通过内置的 Script 功能可以实现类似 Parsers 的预处理功能。

```js
// Define the `main` function

function main(params) {

  // 所有地区
  const allRegex = /^(?!.*(?:自动|故障|流量|官网|套餐|机场|订阅|年|月|失联|频道)).*$/;
  const allProxies = getProxiesByRegex(params, allRegex);

  // 代理模式
  const ProxyMode = {
    name: "🔯 代理模式",
    type: "select",
    url: "http://www.gstatic.com/generate_204",
    interval: 300,
    tolerance: 20,
    timeout: 2000,
    lazy: true,
    proxies: ["绕过大陆丨黑名单", "绕过大陆丨白名单"]
  };

  const SelectProxy = {
    name: "🔰 选择节点",
    type: "select",
    url: "http://www.gstatic.com/generate_204",
    interval: 300,
    tolerance: 20,
    timeout: 2000,
    lazy: true,
    proxies: allProxies
  };

  const BanAD = {
    name: "🛑 广告拦截",
    type: "select",
    url: "http://www.gstatic.com/generate_204",
    interval: 300,
    tolerance: 20,
    timeout: 2000,
    lazy: true,
    proxies: ["DIRECT", "REJECT", "PROXY"]
  };

  const OneDrive = {
    name: "Ⓜ️ OneDrive",
    type: "select",
    url: "http://www.gstatic.com/generate_204",
    interval: 300,
    tolerance: 20,
    timeout: 2000,
    lazy: true,
    proxies: ["DIRECT", "PROXY"]
  };

  const Emby = {
    name: "🔰 Emby",
    type: "select",
    url: "http://www.gstatic.com/generate_204",
    interval: 300,
    tolerance: 20,
    timeout: 2000,
    lazy: true,
    proxies: ["DIRECT", "PROXY"]
  };

  const BypassingBlack = {
    name: "绕过大陆丨黑名单",
    type: "url-test",
    url: "http://www.gstatic.com/generate_204",
    interval: 300,
    tolerance: 20,
    timeout: 2000,
    lazy: true,
    proxies: ["DIRECT"]
  };

  const BypassingWhite = {
    name: "绕过大陆丨白名单",
    type: "url-test",
    url: "http://www.gstatic.com/generate_204",
    interval: 300,
    tolerance: 20,
    timeout: 2000,
    lazy: true,
    proxies: ["PROXY"]
  };

  const PROXY = {
    name: "PROXY",
    type: "url-test",
    url: "http://www.gstatic.com/generate_204",
    interval: 300,
    tolerance: 20,
    timeout: 2000,
    lazy: true,
    proxies: ["🔰 选择节点"]
  };


  // 负载均衡
  const Balance = {
    name: "Balance",
    type: "load-balance",
    url: "http://www.gstatic.com/generate_204",
    icon: "https://fastly.jsdelivr.net/gh/Koolson/Qure/IconSet/Color/Available.png",
    interval: 300,
    strategy: "consistent-hashing",
    lazy: true,
    proxies: allProxies.length > 0 ? allProxies : ["DIRECT"]
  };

  // 故障转移
  const Fallback = {
    name: "Fallback",
    type: "fallback",
    url: "http://www.gstatic.com/generate_204",
    icon: "https://fastly.jsdelivr.net/gh/Koolson/Qure/IconSet/Color/Bypass.png",
    interval: 300,
    timeout: 2000,
    lazy: true,
    proxies: allProxies.length > 0 ? allProxies : ["DIRECT"]
  };


  // 插入分组
  const groups = params["proxy-groups"] = [];
  groups.unshift(ProxyMode, SelectProxy, BanAD, OneDrive, Emby, BypassingBlack, BypassingWhite, PROXY, Balance, Fallback);

  // 规则
  const rules = [
    "RULE-SET,cn,DIRECT",
    "AND,(AND,(DST-PORT,443),(NETWORK,UDP)),(NOT,((GEOIP,CN,no-resolve))),REJECT",// quic
    // "GEOSITE,Category-ads-all,REJECT",// 可能导致某些网站无法访问
    "GEOSITE,Private,DIRECT",
    "GEOSITE,category-scholar-!cn,PROXY",
    "GEOSITE,microsoft@cn,DIRECT",
    "GEOSITE,twitter,PROXY",
    "GEOSITE,steam@cn,DIRECT",
    "GEOSITE,apple-cn,DIRECT",
    "GEOSITE,category-games@cn,DIRECT",
    "GEOSITE,geolocation-!cn,PROXY",
    "GEOSITE,cn,DIRECT",
    "GEOIP,CN,DIRECT,no-resolve",
    "DST-PORT,80/8080/443/8443,PROXY",
    "GEOIP,private,DIRECT,no-resolve",
    // 自己的规则
    "RULE-SET,ChinaApp,DIRECT",
    "RULE-SET,ChinaCloudServiceProvider,DIRECT",
    "RULE-SET,ChinaDomain,DIRECT",
    "RULE-SET,ChinaIP,DIRECT",
    "RULE-SET,ChinaVideo,DIRECT",
    "RULE-SET,DownloadClient,DIRECT",
    "RULE-SET,GoogleCN,DIRECT",
    "RULE-SET,LocalAreaNetwork,DIRECT",
    "RULE-SET,Emby,🔰 Emby",
    "RULE-SET,BanAD,🛑 广告拦截",
    "RULE-SET,Netflix,PROXY",
    "RULE-SET,OneDrive,Ⓜ️ OneDrive",
    "RULE-SET,ProxyGWFList,PROXY",
    "RULE-SET,ProxyVideo,PROXY",
    "RULE-SET,Telegram,PROXY",
    "MATCH,🔯 代理模式"
  ];
  // 插入规则
  params.rules = rules;


  // 远程规则类型
  const ruleAnchor = {
    ip: { type: 'http', interval: 1800, behavior: 'ipcidr', format: 'text' },
    domain: { type: 'http', interval: 1800, behavior: 'domain', format: 'text' },
    classical: { type: 'http', interval: 1800, behavior: 'classical', format: 'yaml' }
  };

  // 远程规则资源
  const ruleProviders = {
    cn: { ...ruleAnchor.classical, url: 'https://testingcf.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/cn_domain.yaml', path: './ruleset/CN.yaml' },
    ChinaCloudServiceProvider: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaCloudServiceProvider.yaml', path: './ruleset/ChinaCloudServiceProvider.yaml' },
    ChinaDomain: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaDomain.yaml', path: './ruleset/ChinaDomain.yaml' },
    ChinaIP: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaIP.yaml', path: './ruleset/ChinaIP.yaml' },
    ChinaVideo: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/ChinaVideo.yaml', path: './ruleset/ChinaVideo.yaml' },
    DownloadClient: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/DownloadClient.yaml', path: './ruleset/DownloadClient.yaml' },
    GoogleCN: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/GoogleCN.yaml', path: './ruleset/GoogleCN.yaml' },
    LocalAreaNetwork: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/DIRECT/LocalAreaNetwork.yaml', path: './ruleset/LocalAreaNetwork.yaml' },
    Emby: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/Emby.yaml', path: './ruleset/ChinaApp.yaml' },
    Netflix: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/Netflix.yaml', path: './ruleset/Netflix.yaml' },
    OneDrive: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/OneDrive.yaml', path: './ruleset/OneDrive.yaml' },
    ProxyGWFList: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/ProxyGWFList.yaml', path: './ruleset/ProxyGWFList.yaml' },
    ProxyVideo: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/ProxyVideo.yaml', path: './ruleset/ProxyVideo.yaml' },
    Telegram: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/PROXY/Telegram.yaml', path: './ruleset/Telegram.yaml' },
    BanAD: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/REJECT/BanAD.yaml', path: './ruleset/BanAD.yaml' },
    BanEasyListChina: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/REJECT/BanEasyListChina.yaml', path: './ruleset/BanEasyListChina.yaml' },
    ChinaApp: { ...ruleAnchor.classical, url: 'https://cdn.jsdelivr.net/gh/TrueRou/Clash-Rules/REJECT/ChinaApp.yaml', path: './ruleset/ChinaApp.yaml' }
  };

  // 插入远程规则
  params["rule-providers"] = ruleProviders;

  return params;
}

function getProxiesByRegex(params, regex) {
  return params.proxies
          .filter((e) => regex.test(e.name))
          .map((e) => e.name);
}
```

- 开启后刷新订阅即可生效

#### Clash Verge 额外配置（选配）
- 使用内置的 Merge 功能可以实现类似 Mixin 配置的效果（可以不使用）

```yaml
# The `Merge` format used to enhance profile

bind-address: '*'                     # 监听IP白名单，可绑定单个IPv4和v6地址，"*" 为绑定所有IP地址，仅在将allow-lan设置为true时适用
unified-delay: false                  # 统一延迟，更换延迟计算方式，去除握手等额外延迟
tcp-concurrent: true                  #【Meta专属】TCP 并发连接所有 IP, 将使用最快握手的 TCP
keep-alive-interval: 15               #  TCP keep alive interval
skip-auth-prefixes:                   # 设置跳过验证的IP段
  - 127.0.0.1/8
  - ::1/128

geodata-mode: true                    #【Meta专属】使用geoip.dat数据库(默认：false使用mmdb数据库)
geox-url:                             # 自定义 geodata url, 需要有代理的前提才能下载geoip和geosite
  geoip: "https://fastly.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/geoip-lite.dat"
  geosite: "https://fastly.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/geosite.dat"
  mmdb: "https://cdn.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/country.mmdb"
geo-auto-update: true                 # 是否自动更新 geodata
geo-update-interval: 24               # 更新间隔，单位：小时

find-process-mode: strict             # 匹配所有进程（always/strict/off）
global-client-fingerprint: chrome     # 全局 TLS 指纹，优先低于 proxy 内的 client-fingerprint
# 可选： "chrome","firefox","safari","ios","random","none" options.
profile:
  store-selected: true                # 存储 select 选择记录
  store-fake-ip: true                 # 持久化 fake-ip

sniffer:                              # 嗅探域名 可选配置
  enable: true
  parse-pure-ip: true                 # 是否使用嗅探结果作为实际访问，默认 true
  sniff:
    HTTP:
      ports: [80, 8080-8880]
      override-destination: true
    TLS:
      ports: [443, 8443]
    QUIC:
      ports: [443, 8443]
  force-domain: []                    # 强制对此域名进行嗅探
  skip-domain: ['Mijia Cloud']        # 跳过对此域名进行嗅探

tun:                                  # Tun 配置
  enable: true
  stack: system                       # 可选： system/gvisor/mixed
  # tun模式堆栈,如无使用问题,建议使用 system 栈;
  dns-hijack: [any:53]                # dns劫持,一般设置为 any:53 即可, 即劫持所有53端口的udp流量
  strict-route: true                  # 将所有连接路由到tun来防止泄漏，但你的设备将无法被其他设备访问
  auto-route: true                    # 自动设置全局路由，可以自动将全局流量路由进入tun网卡。
  auto-detect-interface: true         # 自动识别出口网卡

dns:
  enable: true                        # 关闭将使用系统 DNS
  ipv6: false                         # IPV6解析开关；如果为false，将返回ipv6结果为空
  enhanced-mode: fake-ip              # 模式：redir-host或fake-ip
  listen: :1053                       # DNS 监听地
  fake-ip-range: 198.18.0.1/16        # fakeip 下的 IP 段设置，tun 网卡的默认 ip 也使用此值
  fake-ip-filter: ['*', '+.lan', '+.local', '+.msftncsi.com', '+.msftconnecttest.com']
  # Fake-ip 过滤，列表中的域名返回真实IP
  proxy-server-nameserver: [https://dns.alidns.com/dns-query, https://doh.pub/dns-query]
  # 代理DNS服务器，支持udp/tcp/dot/doh/doq
  nameserver-policy:
    "geosite:cn,private": [https://doh.pub/dns-query, https://dns.alidns.com/dns-query]
    # 指定域名查询的解析服务器，可使用 geosite, 优先于 nameserver/fallback 查询
  nameserver: [https://dns.alidns.com/dns-query, https://doh.pub/dns-query]
  # 默认DNS服务器，支持udp/tcp/dot/doh/doq
  fallback: [tls://8.8.4.4, tls://1.1.1.1]
  # fallbaack DNS服务器，支持udp/tcp/dot/doh/doq
  fallback-filter: { geoip: true, geoip-code: CN, ipcidr: [240.0.0.0/4, 0.0.0.0/32] }
```


