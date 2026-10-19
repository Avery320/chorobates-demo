# 地質

地質分類整合經濟部地質調查及礦業管理中心的活動斷層、液化潛勢與地表地層圖資。斷層及液化提供本地完整向量，地層由官方影像服務按縮放載入。

<p align="center" dir="rtl">
  <a href="../../../third-party/README.md#待釐清項目"><img src="https://img.shields.io/badge/GEOS_WASM-review_pending-808080?style=flat" alt="GEOS WASM 授權：待釐清" /></a>
  <a href="../../../third-party/README.md#待釐清項目"><img src="https://img.shields.io/badge/Tippecanoe-review_pending-808080?style=flat" alt="Tippecanoe 授權：待釐清" /></a>
</p>

## 主要功能

- 保留斷層原始線位與觀察／推測分類。
- 將液化潛勢對應至分類配色及面拉伸級距。
- 按影像縮放層級切換地表地層圖例。

## 模組架構

### 目錄結構

```text
public/gis/geology/
├── manifest.json                # 產生的分類資料與服務契約
├── active-faults/               # 活動斷層向量
├── soil-liquefaction/           # 液化向量與潛勢分類表
├── surface-geology/             # 線上服務的分級圖例
└── readme.md                    # 分類說明與維護入口
```

### 資料流程

```text
斷層 / 液化來源 → geology/datasets.mjs
    → 完整向量 / 顯示與區域圖磚 → 共用篩選
地層影像服務 + 圖例 → MapGeologyLayers → MapLibre
液化分類表 → potential → 共用配色與面高度
```

## 主要資料

| 圖層         | 顯示內容                                                 | 來源與授權依據                                                                                                                                           |
| ------------ | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 活動斷層     | 線：觀察線位為實線，推測或隱伏為虛線；顏色不表示危險等級 | [地質雲](https://www.geologycloud.tw/data/zh-tw/ActiveFault?all=true)；[開放資料說明](https://data.gov.tw/dataset/6697)                                  |
| 土壤液化潛勢 | 面：100 m 網格的高／中／低潛勢，沿用官方配色與分類拉伸   | [官方服務](https://gis.liquid.net.tw/arcgis/rest/services/P04032/Soil_cgs_2026/MapServer/0?f=pjson)；[開放資料說明](https://data.gov.tw/dataset/28691)   |
| 地表地層     | 線上影像與構造線，圖幅及圖例依比例尺切換                 | [圖磚 API](https://geomap.gsmma.gov.tw/api/Tile/v1/oas/)；[官方圖例](https://geomap.gsmma.gov.tw/gwh/gsb97-1/legend_new/legend_get_pg.cfm)，條款依原服務 |

斷層使用 `observe` 判斷線樣式；液化使用 `potential` 對應分類的 `color`／`heightLevel`。級距與光幕入口見 [3D 顯示](../../../src/gis/README.md#3d-顯示)。

本地圖例：[液化分類](soil-liquefaction/liquefaction-potentials.json) · [地層圖例](surface-geology/surface-geology-legends.json)。

## 邊界

- 液化轉接器排除未提供高／中／低分級的 TYPE=NOT，排除紀錄保存於 manifest；空白不表示低潛勢。
- 液化高度為分類顯示尺度；地表地層僅提供影像，沒有本地完整向量可參與篩選。
- 斷層實線／虛線由原線樣式呈現，3D 光幕沿查詢後的路徑顯示。

## 依賴方向

```text
refresh → geology/datasets → shared/source / liquefaction-potentials.json
MapGeologyLayers → vectorStyle / rasterStyle → GisSnapshot → geology manifest
圖層配色 / 高度 / 圖例 → 本分類 JSON 表
```

共用資料契約、查詢與渲染入口見 [GIS 模組](../../../src/gis/README.md)。

## 更新入口

[資料定義](../../../scripts/gis/geology/catalog.mjs) · [轉接器](../../../scripts/gis/geology/datasets.mjs) · [資產紀錄](manifest.json) · [圖層顯示](../../../src/gis/layers/MapGeologyLayers.ts)

更新斷層與液化：`pnpm gis:refresh --geology`；發布服務設定：`pnpm gis:refresh --catalog`。
