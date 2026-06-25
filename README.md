# A股板块资金流动动态图

这个小工具把 09:30 到 15:00 的板块资金净流入做成动画：

- 左侧是按当前时刻绝对资金额排序的发散条形图，红色为净流入，绿色为净流出。
- 右侧是当前排名靠前板块的日内资金轨迹。
- 页面优先尝试东方财富公开 JSONP 接口；如果接口不可用，会读取站内 `data/fund-flow.csv`。
- 支持自动刷新：每 60 秒重新尝试实时接口和站内 CSV。
- 也可以导入自己的 CSV。

## 使用

直接用浏览器打开：

```bash
open /Users/tao/Documents/股票交易/index.html
```

如果浏览器限制本地页面访问网络，可以在目录里启动一个静态服务器：

```bash
python3 -m http.server 8000
```

然后打开 `http://localhost:8000`。

## 部署

这是纯静态网站，可以部署到任意静态托管平台。

### Vercel

1. 新建一个 GitHub 仓库，把本目录推上去。
2. 在 Vercel 里选择 `Import Project`，导入这个仓库。
3. Framework 选择 `Other`，Build Command 留空，Output Directory 留空或填 `.`。
4. 部署后得到一个公网网址。

项目里的 `vercel.json` 已经给 `/data/*` 设置了 `no-store`，方便更新 CSV 后尽快生效。

### Cloudflare Pages

1. 新建 Pages 项目并连接 GitHub 仓库。
2. Framework 选择 `None`。
3. Build command 留空，Output directory 填 `/` 或 `.`。
4. 部署后得到 `*.pages.dev` 网址。

### GitHub Pages

项目已经包含 `.github/workflows/pages.yml`。推到 GitHub 后：

1. 仓库设置里打开 Pages。
2. Source 选择 `GitHub Actions`。
3. 推送到 `main` 分支后会自动部署。

## 更新数据

最简单的长期更新方式是替换：

```text
data/fund-flow.csv
```

网站每次点击“站内数据”或打开“自动刷新”后，会用时间戳绕过缓存读取最新 CSV。

## CSV 格式

表头必须是：

```csv
time,sector,netFlow
09:30,半导体,120000000
09:30,证券,-80000000
09:31,半导体,145000000
09:31,证券,-65000000
```

`netFlow` 单位是元，页面会自动换算成亿元。

## 数据说明

页面里的“实时数据”按钮使用东方财富公开接口尝试获取行业/概念板块列表和板块日内分钟资金流。公开接口可能受跨域、限流或字段调整影响；如果接口返回为空，页面会依次回退到站内 CSV 和样例数据。
