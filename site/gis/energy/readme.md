# 再生能源

再生能源分類管理離岸風場與潛力場址的完整面資料。轉接器讀取官方角點，共用產製模組發布向量；地圖以資料集 ID 使用面外觀、篩選與拉伸。

<p align="center" dir="rtl">
  <a href="https://github.com/proj4js/proj4js"><img src="https://img.shields.io/badge/Proj4js-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="Proj4js" /></a>
  <a href="https://csv.js.org/parse/"><img src="https://img.shields.io/badge/Node_CSV-csv--parse-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="Node CSV：csv-parse" /></a>
  <a href="https://gitlab.com/catamphetamine/read-excel-file"><img src="https://img.shields.io/badge/read--excel--file-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="read-excel-file" /></a>
  <a href="https://github.com/101arrowz/fflate"><img src="https://img.shields.io/badge/fflate-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="fflate" /></a>
</p>

<p align="center" dir="rtl">
  <a href="../../../third-party/README.md#待釐清項目"><img src="https://img.shields.io/badge/GEOS_WASM-review_pending-808080?style=flat" alt="GEOS WASM 授權：待釐清" /></a>
</p>

## 主要功能

- 將官方 TWD97 角點轉為 EPSG:4326 場址多邊形。
- 檢查場址幾何，將排除紀錄寫入分類 manifest。
- 提供完整範圍查詢與共用面拉伸。

## 模組架構

### 目錄結構

```text
public/gis/energy/
├── manifest.json                # 產生的分類資料與服務契約
├── offshore-wind-farms/         # 離岸風場
├── offshore-wind-potential/     # 風電潛力場址
└── readme.md                    # 分類說明與維護入口
```

### 資料流程

```text
官方場址資料 → energy/datasets.mjs → 場址多邊形
    → 共用產製與發布 → manifest + GeoJSON
    → MapEnergyLayers → 共用篩選 / 面顯示
```

## 主要資料

| 圖層         | 資料意義                               | 來源與授權依據                                                                                                                                                 |
| ------------ | -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 離岸風場     | 有效設置同意或籌設許可場址，非商轉清單 | [能源署公告](https://www.moeaea.gov.tw/ECW/populace/Law/LawsList.aspx?kind=6&menu_id=3302)；[國家海洋資料庫](https://nodass.namr.gov.tw/dataInfo?metadataid=9) |
| 風電潛力場址 | 候選潛力範圍，非核准風場               | [政府資料開放平臺](https://data.gov.tw/dataset/36681)                                                                                                          |

資料集 ID 為 `moeaea-offshore-wind-farms`／`moeaea-offshore-wind-potential`；兩者使用完整場址幾何。[3D 顯示](../../../src/gis/README.md#3d-顯示)由共用面樣式提供。

## 邊界

- 海上場址保留完整範圍，不套用預設陸地縣界。
- 面拉伸使用固定顯示高度，容量等欄位不換算成高度。

## 依賴方向

```text
refresh → energy/datasets → energy/catalog / shared/source
MapEnergyLayers → vectorStyle → GisSnapshot → energy manifest
```

共用資料契約、查詢與渲染入口見 [GIS 模組](../../../src/gis/README.md)。

## 更新入口

[資料定義](../../../scripts/gis/energy/catalog.mjs) · [轉接器](../../../scripts/gis/energy/datasets.mjs) · [資產紀錄](manifest.json) · [圖層顯示](../../../src/gis/layers/MapEnergyLayers.ts)

更新：`pnpm gis:refresh --energy`。
