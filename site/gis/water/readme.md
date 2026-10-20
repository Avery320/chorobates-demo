# 水資源

水資源分類管理流域、水利設施與淹水潛勢面。一般資料與淹水來源各自轉接，完整向量、分類顯示與空間篩選共用 GIS 流程。

顯示位置、範圍與淹水模擬結果；水位、蓄水量及雨量未接入即時觀測。

<p align="center" dir="rtl">
  <a href="https://github.com/proj4js/proj4js"><img src="https://img.shields.io/badge/Proj4js-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="Proj4js" /></a>
  <a href="https://github.com/calvinmetcalf/shapefile-js"><img src="https://img.shields.io/badge/shpjs-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="shpjs" /></a>
</p>

<p align="center" dir="rtl">
  <a href="../../../third-party/README.md#待釐清項目"><img src="https://img.shields.io/badge/GEOS_WASM-review_pending-808080?style=flat" alt="GEOS WASM 授權：待釐清" /></a>
  <a href="../../../third-party/README.md#待釐清項目"><img src="https://img.shields.io/badge/Tippecanoe-review_pending-808080?style=flat" alt="Tippecanoe 授權：待釐清" /></a>
</p>

## 主要功能

- 產製流域、河道、集水區與水利設施的點面資料。
- 保留官方設施身分與欄位。
- 將淹水深度分類連接至配色及面拉伸級距。

## 模組架構

### 目錄結構

```text
public/gis/water/
├── manifest.json                # 產生的分類資料與服務契約
├── basins/                      # 流域面
├── flood-potential/             # 淹水潛勢面與深度分類表
├── reservoirs/                  # 水庫壩體點與集水區面
├── rivers/                      # 河道面
└── readme.md                    # 分類說明與維護入口
```

### 資料流程

```text
官方資料 → water/datasets.mjs / flood.mjs
    → 共用產製與裁切 → manifest + GeoJSON / PMTiles
    → MapWaterLayers → 共用篩選 / 點光效 / 面拉伸
flood-depths.json → depth_class → 共用配色與面高度
```

## 主要資料

| 圖層         | 類型與內容                                | 官方來源與授權依據                                                                     |
| ------------ | ----------------------------------------- | -------------------------------------------------------------------------------------- |
| 流域         | 面：流域範圍                              | [水利署](https://data.gov.tw/dataset/9823)                                             |
| 河道         | 面：河道範圍                              | [水利署](https://data.gov.tw/dataset/25781)                                            |
| 水庫集水區   | 面：集水範圍                              | [水利署](https://data.gov.tw/dataset/129474)                                           |
| 水庫壩體     | 點：壩體位置                              | [水利署](https://data.gov.tw/dataset/25776)                                            |
| 淹水潛勢     | 面：第三代 650 mm／24 h 模擬，2019 年建置 | [水利署](https://data.gov.tw/dataset/25766)                                            |

淹水 `depth_class` 連接五級分類的 `color`／`heightLevel`；其餘面資料使用共用固定高度。尺寸及高度入口見 [3D 顯示](../../../src/gis/README.md#3d-顯示)。

淹水深度配色與拉伸級距見 [flood-depths.json](flood-potential/flood-depths.json)。

## 邊界

- 河道使用面幾何；集水區保存集水範圍，水庫壩體資料為位置點。
- 淹水高度依 heightLevel 表示級距，使用共用顯示尺度，不是實際水深的等比例模型。

## 依賴方向

```text
refresh → water/datasets / flood → water/catalog / shared/source
MapWaterLayers → vectorStyle → GisSnapshot → water manifest
淹水轉接 / 配色 / 高度 → flood-depths.json
```

共用資料契約、查詢與渲染入口見 [GIS 模組](../../../src/gis/README.md)。

## 更新入口

[資料定義](../../../scripts/gis/water/catalog.mjs) · [轉接器](../../../scripts/gis/water/datasets.mjs)／[淹水轉接](../../../scripts/gis/water/flood.mjs) · [資產紀錄](manifest.json) · [圖層顯示](../../../src/gis/layers/MapWaterLayers.ts)

更新：`pnpm gis:refresh --water`。
