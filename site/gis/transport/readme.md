# 交通

交通分類管理車站點位及軌道、道路線資料。分類轉接器處理官方欄位與道路類別，查詢、圖徵互動及點線 3D 顯示使用 GIS 共用模組。

國土測繪中心圖資，採政府資料開放授權條款第 1 版。

<p align="center" dir="rtl">
  <a href="https://github.com/calvinmetcalf/shapefile-js"><img src="https://img.shields.io/badge/shpjs-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="shpjs" /></a>
</p>

<p align="center" dir="rtl">
  <a href="../../../third-party/README.md#待釐清項目"><img src="https://img.shields.io/badge/Tippecanoe-review_pending-808080?style=flat" alt="Tippecanoe 授權：待釐清" /></a>
</p>

## 主要功能

- 將車站及路線 SHP 轉為完整向量，保留官方圖徵 ID。
- 以同一份道路來源分出國道與快速道路。
- 發布顯示圖磚、縣市裁切資產與完整分析資料。

## 模組架構

### 目錄結構

```text
public/gis/transport/
├── manifest.json                # 產生的分類資料與服務契約
├── expressways/                 # 快速道路線
├── highways/                    # 國道線
├── thsr-routes/                 # 高鐵路線
├── thsr-stations/               # 高鐵站點位
├── tra-routes/                  # 臺灣鐵路線
├── tra-stations/                # 臺鐵站點位
└── readme.md                    # 分類說明與維護入口
```

### 資料流程

```text
官方 SHP → transport/datasets.mjs → 欄位及道路分類
    → 完整 GeoJSON / 顯示及區域 PMTiles
    → MapTransportLayers → 共用篩選 → 點位 + 光效 / 原線 + 光幕
```

## 主要資料

| 圖層     | 類型與內容                       | 官方來源                                      |
| -------- | -------------------------------- | --------------------------------------------- |
| 高鐵站   | 點：站址與名稱                   | [高鐵車站](https://data.gov.tw/dataset/73225) |
| 臺鐵站   | 點：臺鐵站址，各站區分別保留     | [臺鐵車站](https://data.gov.tw/dataset/73226) |
| 臺灣鐵路 | 線：含林業鐵路、側線及未命名線段 | [鐵路](https://data.gov.tw/dataset/73220)     |
| 高鐵路線 | 線：高鐵軌道                     | [高速鐵路](https://data.gov.tw/dataset/73221) |
| 國道     | 線：含附屬道路及匝道             | [道路](https://data.gov.tw/dataset/73232)     |
| 快速道路 | 線：依官方道路分類納入           | [道路](https://data.gov.tw/dataset/73232)     |

站點以 `code`／`name` 保存身分與名稱；路線保留道路分類、軌道類別及異動年月。點線光效與原幾何共用[尺寸與互動](../../../src/gis/README.md#3d-顯示)。

## 邊界

- 臺鐵站依官方名稱篩選；路線保留原始圖徵及未命名線段。
- 國道納入 HW／HU，快速道路納入 1E／RE；來源欄位定義見轉接器。
- 線光幕只連接同一路徑內相鄰段，保留圖徵身分與篩選造成的斷點。

## 依賴方向

```text
refresh → transport/datasets → transport/catalog / shared/source
MapTransportLayers → vectorStyle → GisSnapshot → transport manifest
```

共用資料契約、查詢與渲染入口見 [GIS 模組](../../../src/gis/README.md)。

## 更新入口

[資料定義](../../../scripts/gis/transport/catalog.mjs) · [轉接器](../../../scripts/gis/transport/datasets.mjs) · [資產紀錄](manifest.json) · [圖層顯示](../../../src/gis/layers/MapTransportLayers.ts)

更新：`pnpm gis:refresh --transport`。
