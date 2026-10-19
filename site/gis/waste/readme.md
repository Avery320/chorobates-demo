# 廢棄物

廢棄物分類管理焚化廠及兩類掩埋場點資料。來源轉接器整理身分、座標及容量欄位，發布後使用共用點樣式與查詢。

環境管理署設施點位；容量與現況依原統計日期，未接入即時排放或營運資料。

<p align="center" dir="rtl">
  <a href="https://github.com/proj4js/proj4js"><img src="https://img.shields.io/badge/Proj4js-CB3837?style=flat&amp;logo=npm&amp;logoColor=white" alt="Proj4js" /></a>
</p>

## 主要功能

- 將設施來源轉為統一的 code／name 點位資料。
- 保留容量、統計日期、場址狀態及聯絡欄位。
- 提供設施篩選、結果識別與共用點光效。

## 模組架構

### 目錄結構

```text
public/gis/waste/
├── manifest.json                # 產生的分類資料與服務契約
├── coastal-landfills/           # 濱海掩埋場點位
├── incinerators/                # 焚化廠點位
├── landfills/                   # 衛生掩埋場點位
└── readme.md                    # 分類說明與維護入口
```

### 資料流程

```text
官方設施清單 → waste/datasets.mjs → 座標與欄位轉換
    → manifest + 完整 GeoJSON
    → MapWasteLayers → 共用篩選 / 點位 + 光效
```

## 主要資料

| 圖層       | 內容                         | 官方來源與授權依據                                                    |
| ---------- | ---------------------------- | --------------------------------------------------------------------- |
| 焚化廠     | 煙囪位置、廠名與聯絡資料     | [焚化廠](https://data.gov.tw/dataset/8341)                            |
| 衛生掩埋場 | 營運中公有設施，含簡易垃圾場 | [公有掩埋場](https://data.gov.tw/dataset/9370)                        |
| 濱海掩埋場 | 包含停用及復育場址           | [環境部資料說明](https://data.moenv.gov.tw/dataset/detail/STAT_P_113) |

三個資料集分別維護設施身分；查詢欄位見 [catalog.mjs](../../../scripts/gis/waste/catalog.mjs)，[點光效](../../../src/gis/README.md#3d-顯示)使用共用顯示參數。

## 邊界

- 容量及距海岸等數值保留資料單位；光效高度只與圖層大小綁定。
- 設施現況由來源欄位呈現，各圖徵保留官方身分。

## 依賴方向

```text
refresh → waste/datasets → waste/catalog / shared/source
MapWasteLayers → vectorStyle → GisSnapshot → waste manifest
```

共用資料契約、查詢與渲染入口見 [GIS 模組](../../../src/gis/README.md)。

## 更新入口

[資料定義](../../../scripts/gis/waste/catalog.mjs) · [轉接器](../../../scripts/gis/waste/datasets.mjs) · [資產紀錄](manifest.json) · [圖層顯示](../../../src/gis/layers/MapWasteLayers.ts)

更新：`pnpm gis:refresh --waste`。
