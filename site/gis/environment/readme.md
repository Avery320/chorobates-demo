# 環境

環境分類管理三市樹木、生態保育範圍及植群資料。樹木、生態與植群轉接器各自處理來源欄位，資產產製、查詢及顯示沿用 GIS 共用流程。

<p align="center" dir="rtl">
  <a href="https://github.com/proj4js/proj4js"><img src="https://img.shields.io/badge/Proj4js-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="Proj4js" /></a>
  <a href="https://github.com/calvinmetcalf/shapefile-js"><img src="https://img.shields.io/badge/shpjs-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="shpjs" /></a>
  <a href="https://csv.js.org/parse/"><img src="https://img.shields.io/badge/Node_CSV-csv--parse-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="Node CSV：csv-parse" /></a>
</p>

<p align="center" dir="rtl">
  <a href="../../../third-party/README.md#待釐清項目"><img src="https://img.shields.io/badge/GEOS_WASM-review_pending-808080?style=flat" alt="GEOS WASM 授權：待釐清" /></a>
  <a href="../../../third-party/README.md#待釐清項目"><img src="https://img.shields.io/badge/Tippecanoe-review_pending-808080?style=flat" alt="Tippecanoe 授權：待釐清" /></a>
</p>

## 主要功能

- 將臺北、新北及高雄樹木資料集呈現為同一個點圖層。
- 保留保護區、棲息環境、濕地及綠網範圍與分區。
- 以天然植群分類表提供名稱及配色。

## 模組架構

### 目錄結構

```text
public/gis/environment/
├── manifest.json                       # 產生的分類資料與服務契約
├── ecological-conservation-corridors/  # 區域保育軸帶
├── ecological-network-focus-areas/     # 綠網關注區域
├── important-wetlands/                 # 重要濕地
├── natural-vegetation/                 # 植群面資料與分類表
├── protected-trees/                    # 三市樹木點位
├── wildlife-habitats/                  # 重要棲息環境
├── wildlife-protected-areas/           # 野生動物保護區
└── readme.md                           # 分類說明與維護入口
```

### 資料流程

```text
樹木 / 生態 / 植群官方資料 → 各分類轉接器
    → 共用產製與縣市裁切 → manifest + GeoJSON / PMTiles
    → MapEnvironmentLayers → 共用篩選 / 點光效 / 面拉伸
```

## 主要資料

| 圖層             | 類型與內容                            | 官方來源與授權依據                                                                                                                 |
| ---------------- | ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| 受保護樹木       | 點：三市列管樹木，共用一個圖層        | [臺北](https://data.gov.tw/dataset/121274)、[新北](https://data.gov.tw/dataset/125615)、[高雄](https://data.gov.tw/dataset/163742) |
| 野生動物保護區   | 面：公告範圍，分區分別保留            | [林業保育署](https://data.moa.gov.tw/open_detail.aspx?id=162)                                                                      |
| 重要棲息環境     | 面：公告範圍                          | [林業保育署](https://data.moa.gov.tw/open_detail.aspx?id=156)                                                                      |
| 重要濕地         | 面：濕地及子區域                      | [國家公園署](https://data.gov.tw/dataset/25659)                                                                                    |
| 國土綠網關注區域 | 面：保育規劃範圍與關注物種            | [林業保育署](https://www.forest.gov.tw/0004767)                                                                                    |
| 區域保育軸帶     | 面：丘陵、溪流、平原與海岸等規劃範圍  | [林業保育署](https://www.forest.gov.tw/0004767)                                                                                    |
| 天然植群         | 面：2003–2009 年調查，2013 年部分修正 | [林業保育署](https://data.gov.tw/dataset/9930)                                                                                     |

樹木三個資料集各自保留圖徵 ID；圖層外觀由 `tw-protected-trees` 統一控制。主要欄位與幾何契約見 [catalog.mjs](../../../scripts/gis/environment/catalog.mjs)。

天然植群使用 [官方六類配色](natural-vegetation/vegetation-classes.json)。

## 邊界

- 部分面積欄位為整個區域的總面積，不宜直接按圖徵加總。
- 樹木點位涵蓋臺北、新北及高雄，保護區與植群範圍依原圖資保存。
- 天然植群配色由分類表提供；目前未設定高度級距，面拉伸使用共用固定高度。

## 依賴方向

```text
refresh → trees / ecology / vegetation → environment/catalog / shared/source
MapEnvironmentLayers → vectorStyle → GisSnapshot → environment manifest
植群轉接 / 圖層配色 → vegetation-classes.json
```

共用資料契約、查詢與渲染入口見 [GIS 模組](../../../src/gis/README.md)。

## 更新入口

[資料定義](../../../scripts/gis/environment/catalog.mjs) · 轉接器：[樹木](../../../scripts/gis/environment/trees.mjs)／[生態](../../../scripts/gis/environment/ecology.mjs)／[植群](../../../scripts/gis/environment/vegetation.mjs) · [資產紀錄](manifest.json) · [圖層顯示](../../../src/gis/layers/MapEnvironmentLayers.ts)

更新：`pnpm gis:refresh --environment`；部分更新使用 `--ecology`／`--vegetation`。
