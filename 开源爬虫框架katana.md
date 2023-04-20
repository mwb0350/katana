# ProjectDiscovery开源爬虫框架katana

 2022年11月8日23:41:44本文共 12325 字阅读完需 49.5 分钟

## 前言

ProjectDiscovery组织开源了很多自动化扫描的内部工具和研究，它们都是基于`Go`语言编写，并且在实际渗透中有极大的作用。

katana是ProjectDiscovery开源的“下一代爬虫框架”，项目地址：https://github.com/projectdiscovery/katana

该爬虫框架特点：

- 快速且完全可配置的web爬虫
- Standard 和 Headless 模式支持
- JavaScript 解析/爬取
- 可定制化自动填充表单
- 通过预配置字段/正则表达式控制爬虫范围
- 可定制的输出 - 预配置的字段
- 可从标准输入、URL 和文件作为输入参数
- 可输出到标准输出、文件和 JSON

## 安装

katana 需要 Go 1.18 才能成功安装。运行以下命令进行安装或从release页面下载预编译的二进制文件。

```awk
go install github.com/projectdiscovery/katana/cmd/katana@latest
```

## 使用方法

```ebnf
katana -h
```

显示所有帮助项。

```vim
Usage:
./katana [flags]

Flags:
INPUT:
  -u, -list string[] target url / list to crawl

CONFIGURATION:
  -d, -depth int               maximum depth to crawl (default 2)
  -jc, -js-crawl               enable endpoint parsing / crawling in javascript file
  -ct, -crawl-duration int     maximum duration to crawl the target for
  -kf, -known-files string     enable crawling of known files (all,robotstxt,sitemapxml)
  -mrs, -max-response-size int maximum response size to read (default 2097152)
  -timeout int                 time to wait for request in seconds (default 10)
  -aff, -automatic-form-fill   enable optional automatic form filling (experimental)
  -retry int                   number of times to retry the request (default 1)
  -proxy string                 http/socks5 proxy to use
  -H, -headers string[]         custom header/cookie to include in request
  -config string               path to the katana configuration file
  -fc, -form-config string     path to custom form configuration file

HEADLESS:
  -hl, -headless       enable headless hybrid crawling (experimental)
  -sc, -system-chrome use local installed chrome browser instead of katana installed
  -sb, -show-browser   show the browser on the screen with headless mode

SCOPE:
  -cs, -crawl-scope string[]       in scope url regex to be followed by crawler
  -cos, -crawl-out-scope string[] out of scope url regex to be excluded by crawler
  -fs, -field-scope string         pre-defined scope field (dn,rdn,fqdn) (default "rdn")
  -ns, -no-scope                   disables host based default scope
  -do, -display-out-scope         display external endpoint from scoped crawling

FILTER:
  -f, -field string               field to display in output (url,path,fqdn,rdn,rurl,qurl,qpath,file,key,value,kv,dir,udir)
  -sf, -store-field string         field to store in per-host output (url,path,fqdn,rdn,rurl,qurl,qpath,file,key,value,kv,dir,udir)
  -em, -extension-match string[]   match output for given extension (eg, -em php,html,js)
  -ef, -extension-filter string[] filter output for given extension (eg, -ef png,css)

RATE-LIMIT:
  -c, -concurrency int         number of concurrent fetchers to use (default 10)
  -p, -parallelism int         number of concurrent inputs to process (default 10)
  -rd, -delay int               request delay between each request in seconds
  -rl, -rate-limit int         maximum requests to send per second (default 150)
  -rlm, -rate-limit-minute int maximum number of requests to send per minute

OUTPUT:
  -o, -output string file to write output to
  -j, -json           write output in JSONL(ines) format
  -nc, -no-color     disable output content coloring (ANSI escape codes)
  -silent             display output only
  -v, -verbose       display verbose output
  -version           display project version
```

## 运行 Katana

### katana的输入参数

katana 以 url 作为输入参数并可接受单个或多个输入。

可以使用 `-u`选项提供输入 URL，并且可以使用逗号分隔的输入提供多个值，类似地，使用`-list`选项支持文件输入，另外还支持管道输入 (stdin)。

**URL输入**

```awk
katana -u https://tesla.com
```

**多URL输入**

```awk
katana -u https://tesla.com,https://google.com
```

**文件输入**

```awk
$ cat url_list.txt

https://tesla.com
https://google.com

katana -list url_list.txt
```

**管道输入 (stdin)**

```awk
echo https://tesla.com | katana
```

一个示例：

```awk
katana -u https://youtube.com

  __       __                
/ /_____ _/ /____ ____ ___ _
/ '_/ _ / __/ _ / _ / _ /
/_/_\_,_/__/_,_/_//_/_,_/ v0.0.1                    

    projectdiscovery.io

[WRN] Use with caution. You are responsible for your actions.
[WRN] Developers assume no liability and are not responsible for any misuse or damage.
https://www.youtube.com/
https://www.youtube.com/about/
https://www.youtube.com/about/press/
https://www.youtube.com/about/copyright/
https://www.youtube.com/t/contact_us/
https://www.youtube.com/creators/
https://www.youtube.com/ads/
https://www.youtube.com/t/terms
https://www.youtube.com/t/privacy
https://www.youtube.com/about/policies/
https://www.youtube.com/howyoutubeworks?utm_campaign=ytgen&utm_source=ythp&utm_medium=LeftNav&utm_content=txt&u=https%3A%2F%2Fwww.youtube.com%2Fhowyoutubeworks%3Futm_source%3Dythp%26utm_medium%3DLeftNav%26utm_campaign%3Dytgen
https://www.youtube.com/new
https://m.youtube.com/
https://www.youtube.com/s/desktop/4965577f/jsbin/desktop_polymer.vflset/desktop_polymer.js
https://www.youtube.com/s/desktop/4965577f/cssbin/www-main-desktop-home-page-skeleton.css
https://www.youtube.com/s/desktop/4965577f/cssbin/www-onepick.css
https://www.youtube.com/s/_/ytmainappweb/_/ss/k=ytmainappweb.kevlar_base.0Zo5FUcPkCg.L.B1.O/am=gAE/d=0/rs=AGKMywG5nh5Qp-BGPbOaI1evhF5BVGRZGA
https://www.youtube.com/opensearch?locale=en_GB
https://www.youtube.com/manifest.webmanifest
https://www.youtube.com/s/desktop/4965577f/cssbin/www-main-desktop-watch-page-skeleton.css
https://www.youtube.com/s/desktop/4965577f/jsbin/web-animations-next-lite.min.vflset/web-animations-next-lite.min.js
https://www.youtube.com/s/desktop/4965577f/jsbin/custom-elements-es5-adapter.vflset/custom-elements-es5-adapter.js
https://www.youtube.com/s/desktop/4965577f/jsbin/webcomponents-sd.vflset/webcomponents-sd.js
https://www.youtube.com/s/desktop/4965577f/jsbin/intersection-observer.min.vflset/intersection-observer.min.js
https://www.youtube.com/s/desktop/4965577f/jsbin/scheduler.vflset/scheduler.js
https://www.youtube.com/s/desktop/4965577f/jsbin/www-i18n-constants-en_GB.vflset/www-i18n-constants.js
https://www.youtube.com/s/desktop/4965577f/jsbin/www-tampering.vflset/www-tampering.js
https://www.youtube.com/s/desktop/4965577f/jsbin/spf.vflset/spf.js
https://www.youtube.com/s/desktop/4965577f/jsbin/network.vflset/network.js
https://www.youtube.com/howyoutubeworks/
https://www.youtube.com/trends/
https://www.youtube.com/jobs/
https://www.youtube.com/kids/
```

## 爬虫模式

### Standard 模式

标准爬虫模式在底层使用标准的 go http 库来处理 HTTP  请求/响应。这种方式要快得多，因为它没有浏览器开销。它按原样分析 HTTP 响应正文，没有任何 javascript 或 DOM  渲染，可能会丢失 dom 呈现的URL或异步调用URL，这取决于浏览器特定的事件。



### Headless 模式

Headless模式hook内部浏览器调用以直接在浏览器上下文中处理 HTTP 请求/响应。两个优点：

- HTTP 指纹（TLS 和用户代理）将客户端完全识别为合法浏览器
- 覆盖范围更广，因为URL是在分析标准原始响应时发现的，也就是启用了 javascript 的浏览器渲染的模式。

Headless爬虫是可选的，可以使用 `-headless`选项启用。

以下是其他Headless CLI 选项

```mipsasm
katana -h headless

Flags:
HEADLESS:
  -hl, -headless       enable experimental headless hybrid crawling
  -sc, -system-chrome use local installed chrome browser instead of katana installed
  -sb, -show-browser   show the browser on the screen with headless mode
```

## 爬虫范围控制

katana 提供了多种支持来定义爬虫范围。

### -field-scope

使用预定义字段名称来定义范围，`rdn`是默认选项。

- `rdn`- 爬虫范围为根域名和所有子域（默认）
- `fqdn`- 爬虫范围为给定的子域
- `dn`- 范围为域名关键字

```awk
katana -u https://tesla.com -fs dn
```

### -crawl-scope

对于高级范围控制，可以使用带有正则表达式支持的 `-cs`选项。

```awk
katana -u https://tesla.com -cs login
```

对于多个范围内的规则，可以传递带有多行字符串/正则表达式的文件输入。

```stata
$ cat in_scope.txt

login/
admin/
app/
wordpress/

katana -u https://tesla.com -cs in_scope.txt
```

### -crawl-out-scope

为了定义不抓取的内容，可以使用 `-cos`选项，并且还支持正则表达式输入。

```awk
katana -u https://tesla.com -cs logout
```

对于多个超出范围的规则，可以传递带有多行字符串/正则表达式的文件输入。

```bash
$ cat out_of_scope.txt

/logout
/log_out

katana -u https://tesla.com -cs out_of_scope.txt
```

### -no-scope

Katana 默认为 `*.domain`范围，禁用此`-ns`选项可用于抓取互联网。

```awk
katana -u https://tesla.com -ns
```

### -display-out-scope

默认情况下，当使用范围选项时，它也适用于显示为输出的链接，因为这些外部 URL 默认排除并覆盖此行为，`-do`选项可用于显示目标中存在的所有外部 URL范围。



```awk
katana -u https://tesla.com -do
```

这是范围控制的所有 CLI 选项 -

```mipsasm
katana -h scope

Flags:
SCOPE:
  -cs, -crawl-scope string[]       in scope url regex to be followed by crawler
  -cos, -crawl-out-scope string[] out of scope url regex to be excluded by crawler
  -fs, -field-scope string         pre-defined scope field (dn,rdn,fqdn) (default "rdn")
  -ns, -no-scope                   disables host based default scope
  -do, -display-out-scope         display external endpoint from scoped crawling
```

## 爬虫配置

Katana 带有多个选项，可以按照我们想要的方式配置和控制爬虫。

### -depth

用于定义跟随 url 进行爬虫的深度的选项，深度越大，爬虫的url数量越多，爬虫时间也越长。

```awk
katana -u https://tesla.com -d 5
```

### -js-crawl

启用 JavaScript 文件解析 + 抓取在 JavaScript 文件中发现的URL的选项，默认禁用。

```awk
katana -u https://tesla.com -jc
```

### -crawl-duration

预定义抓取持续时间的选项，默认禁用。

```awk
katana -u https://tesla.com -ct 2
```

### -known-files

启用抓取 `robots.txt`和`sitemap.xml`文件的选项，默认禁用。

```awk
katana -u https://tesla.com -kf robotstxt,sitemapxml
```

### -automatic-form-fill

为已知/未知字段启用自动填写表单的选项，可以根据需要通过更新 `$HOME/.config/katana/form-config.yaml`中的表单配置文件来自定义已知字段值。

自动表格填写是实验性功能。

```fortran
  -aff, -automatic-form-fill enable optional automatic form filling (experimental)
```

需要时可以配置更多选项，这里是所有与配置相关的 CLI 选项 -

```livecodeserver
katana -h config

Flags:
CONFIGURATION:
  -d, -depth int               maximum depth to crawl (default 2)
  -jc, -js-crawl               enable endpoint parsing / crawling in javascript file
  -ct, -crawl-duration int     maximum duration to crawl the target for
  -kf, -known-files string     enable crawling of known files (all,robotstxt,sitemapxml)
  -mrs, -max-response-size int maximum response size to read (default 2097152)
  -timeout int                 time to wait for request in seconds (default 10)
  -retry int                   number of times to retry the request (default 1)
  -proxy string                 http/socks5 proxy to use
  -H, -headers string[]         custom header/cookie to include in request
  -config string               path to the katana configuration file
  -fc, -form-config string     path to custom form configuration file
```

## 过滤

### -field

Katana 带有内置字段，可用于过滤所需信息的输出，`-f`选项可用于指定任何可用字段。

```sas
  -f, -field string field to display in output (url,path,fqdn,rdn,rurl,qurl,qpath,file,key,value,kv,dir,udir)
```

这是一个表格，其中包含每个字段的示例以及使用时的预期输出 -

| 字段    | 描述               | 示例                                                         |
| ------- | ------------------ | ------------------------------------------------------------ |
| `url`   | URL                | `https://admin.projectdiscovery.io/admin/login?user=admin&password=admin` |
| `qurl`  | 包含查询参数的 URL | `https://admin.projectdiscovery.io/admin/login.php?user=admin&password=admin` |
| `qpath` | 包含查询参数的路径 | `/login?user=admin&password=admin`                           |
| `path`  | URL 路径           | `https://admin.projectdiscovery.io/admin/login`              |
| `fqdn`  | 完全限定域名       | `admin.projectdiscovery.io`                                  |
| `rdn`   | 根域名             | `projectdiscovery.io`                                        |
| `rurl`  | 主页 URL           | `https://admin.projectdiscovery.io`                          |
| `file`  | URL 中的文件名     | `login.php`                                                  |
| `key`   | URL 中的参数名     | `user,password`                                              |
| `value` | URL 中的参数值     | `admin,admin`                                                |
| `kv`    | URL 中的参数对     | `user=admin&password=admin`                                  |
| `dir`   | URL 中的目录       | `/admin/`                                                    |
| `udir`  | 带目录的 URL       | `https://admin.projectdiscovery.io/admin/`                   |

这是一个使用字段选项仅显示所有带有查询参数的 url 的示例 -

```awk
katana -u https://tesla.com -f qurl -silent

https://shop.tesla.com/en_au?redirect=no
https://shop.tesla.com/en_nz?redirect=no
https://shop.tesla.com/product/men_s-raven-lightweight-zip-up-bomber-jacket?sku=1740250-00-A
https://shop.tesla.com/product/tesla-shop-gift-card?sku=1767247-00-A
https://shop.tesla.com/product/men_s-chill-crew-neck-sweatshirt?sku=1740176-00-A
https://www.tesla.com/about?redirect=no
https://www.tesla.com/about/legal?redirect=no
https://www.tesla.com/findus/list?redirect=no
```

### -store-field

为了补充对在运行时过滤输出有用的字段选项，有 `-sf`，`-store-fields`选项，它与字段选项完全一样，除了过滤之外，它将磁盘上的所有信息存储在按目标 url 排序的`katana_output`目录下.

```stylus
katana -u https://tesla.com -sf key,fqdn,qurl -silent

$ ls katana_output/

https_www.tesla.com_fqdn.txt
https_www.tesla.com_key.txt
https_www.tesla.com_qurl.txt
```

> store-field 选项可以方便地收集信息以构建目标感知单词列表，但不限于 -

- 常用参数
- 常用的路径
- 最常见的文件
- 未知子（域）

以下是其他过滤器选项 -

```vim
  -f, -field string               field to display in output (url,path,fqdn,rdn,rurl,qurl,file,key,value,kv,dir,udir)
  -sf, -store-field string         field to store in per-host output (url,path,fqdn,rdn,rurl,qurl,file,key,value,kv,dir,udir)
  -em, -extension-match string[]   match output for given extension (eg, -em php,html,js)
  -ef, -extension-filter string[] filter output for given extension (eg, -ef png,css)
```

## 爬虫速率限制和延迟

如果不遵循目标网站的限制，在抓取时很容易被阻止/禁止，katana 带有多个选项来调整抓取速度以达到我们想要的快/慢。

### -delay

用于在 katana 在爬行时发出的每个新请求之间引入以秒为单位的延迟的选项，默认情况下禁用。

```awk
katana -u https://tesla.com -delay 20
```

### -concurrency

控制每个目标同时获取的 url 数量的选项。

```awk
katana -u https://tesla.com -c 20
```

### -parallelism

用于从列表输入定义同时处理的目标数量的选项。

```awk
katana -u https://tesla.com -p 20
```

### -rate-limit

用于定义每秒最大请求数的选项。

```awk
katana -u https://tesla.com -rl 100
```

### -rate-limit-minute

用于定义每分钟可以发出的最大请求数的选项。

```awk
katana -u https://tesla.com -rlm 500
```

这是用于速率限制控制的所有长/短 CLI 选项 -

```autoit
katana -h rate-limit

Flags:
RATE-LIMIT:
  -c, -concurrency int         number of concurrent fetchers to use (default 10)
  -p, -parallelism int         number of concurrent inputs to process (default 10)
  -rd, -delay int               request delay between each request in seconds
  -rl, -rate-limit int         maximum requests to send per second (default 150)
  -rlm, -rate-limit-minute int maximum number of requests to send per minute
```

## 输出

### -json

Katana 支持纯文本格式和JSON格式的文件输出，其中包括信息如 `source`、`tag`、和`attribute`

```awk
katana -u https://example.com -json -do | jq .

{
"timestamp": "2022-11-05T22:33:27.745815+05:30",
"endpoint": "https://www.iana.org/domains/example",
"source": "https://example.com",
"tag": "a",
"attribute": "href"
}
```

以下是与输出相关的其他 CLI 选项 -

```vim
katana -h output

OUTPUT:
  -o, -output string file to write output to
  -j, -json           write output in JSONL(ines) format
  -nc, -no-color     disable output content coloring (ANSI escape codes)
  -silent             display output only
  -v, -verbose       display verbose output
  -version  
```