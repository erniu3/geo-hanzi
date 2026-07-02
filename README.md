# 二牛汉字定位编码

一个纯前端（单文件 `index.html`）的 GPS 定位与坐标编码互转工具，主要面向 iOS Safari，桌面浏览器同样可用。

## 功能

- **获取当前定位**：调用浏览器 Geolocation API，可显式选择「高精度（GPS）」或「普通精度（网络）」，并实时提示当前所用精度。
- **多格式展示**：把一个坐标同时展示为 5 种表示：
  1. **汉字编码**（4 字，字库 1024 = 2¹⁰，正好 40 bit 熵）
  2. **Geohash**（8 位，40 bit）
  3. **Plus Codes**（Open Location Code，8+2 位）
  4. **十进制经纬度**（精确到 4 位小数）
  5. **度分秒 DMS**（精确到秒）
- **手动输入任意格式**：输入框支持粘贴/输入以上任意一种格式，自动识别并反解回坐标，再换算出其余全部格式。
- **一键跳转地图**：将坐标转换到对应坐标系后，跳转到高德、百度、苹果地图、Google 地图（GCJ-02 / WGS-84 两版）、Geohash 地图、Google Earth。
- **点击复制**：点击任意结果卡片即可全选复制。
- 亮/暗色自适应，Apple 风格 UI，无任何外部依赖与网络请求。

## 汉字编码原理

思路与 Geohash 一致：把经纬度各自量化为定长二进制串并交织（interleave）成一个大整数，再把整数转成 base-1024，用汉字字库代替字符集。

- 字库 `hanzi_freq_1024.txt`：按字频排序的 1024 个常用汉字（去重）。
- 每字承载 log₂(1024) = 10 bit，4 字 = 40 bit，与 8 位 Geohash 的熵完全相等，定位精度相当（约百米级）。

### 汉字字库来源

字频数据取自 [Chinese Character Frequency List（MTSU）](http://lingua.mtsu.edu/chinese-computing/statistics/char/list.php?Which=TO)，从中取字频最高的前 1024 个字作为字库。

## 坐标系与地图跳转

中国大陆各地图使用不同坐标系，跳转前会做相应转换（页面上以标签标注）：

| 地图 | 坐标系 | 转换链路 |
|---|---|---|
| 高德地图 | GCJ-02（火星坐标） | WGS-84 → GCJ-02 |
| 苹果地图 | GCJ-02 | WGS-84 → GCJ-02 |
| Google 地图（GCJ-02） | GCJ-02 | WGS-84 → GCJ-02（对齐国内路网/手机端） |
| Google 地图（WGS-84） | WGS-84 | 不转换（对齐卫星影像/电脑网页版） |
| 百度地图 | BD-09 | WGS-84 → GCJ-02 → BD-09 |
| Geohash 地图 | WGS-84 | 不转换 |
| Google Earth | WGS-84 | 不转换 |

手机端（iOS/Android）高德、百度使用 App scheme 直接唤端；桌面浏览器回落到网页版链接。

## 使用

直接用浏览器打开 `index.html` 即可，无需构建。

> **注意**：iOS Safari 的定位 API 要求页面通过 **HTTPS 或 localhost** 访问。用 `file://` 或局域网 `http://` 打开会拿不到定位。部署到任意 HTTPS 静态托管即可正常使用。