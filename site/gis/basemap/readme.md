# 底圖與地形

底圖分類保存 OpenFreeMap 樣式與 Mapterhorn DEM 的服務契約。MapLibre 串流載入底圖與高程，GIS 渲染模組管理建築、主題圖層順序及地形安裝。

<p align="center" dir="rtl">
  <a href="https://maplibre.org/"><img src="https://img.shields.io/badge/MapLibre-GL_JS-396CB2?style=flat&amp;logo=maplibre&amp;logoColor=white" alt="MapLibre GL JS" /></a>
</p>

## 主要功能

- 提供 dark、liberty、bright、positron、fiord 五種底圖樣式。
- 以 DEM 建立 3D 地形及 hillshade，供點線光效取得地表高程。
- 按底圖建築高度欄位顯示建築拉伸。

## 模組架構

### 目錄結構

```text
public/gis/basemap/
├── manifest.json                # 產生的分類資料與服務契約
└── readme.md                    # 分類說明與維護入口
```

### 資料流程

```text
basemap/catalog.mjs → 分類 manifest → GisSnapshot
    ├──→ MapBasemapLayers → MapLibre 底圖
    └──→ MapProjectView → MapTerrain → DEM 地形 / 陰影
DEM 地表高程 → MapLightBeams 點線定位
```

## 主要資料

| 服務        | 顯示內容                                           | 來源與授權                                                                                                                                                                                                             |
| ----------- | -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OpenFreeMap | 向量底圖、字形及圖示                               | [服務與樣式](https://openfreemap.org/)；[OpenMapTiles](https://openmaptiles.org/)／[OpenStreetMap](https://www.openstreetmap.org/copyright)；[程式授權](https://github.com/hyperknot/openfreemap/blob/main/LICENSE.md) |
| Mapterhorn  | DEM 高程圖磚，供 MapLibre 地形、陰影及光效地表定位 | [服務](https://mapterhorn.com/)；各高程來源的 [attribution 與條款](https://mapterhorn.com/attribution/)；[程式](https://github.com/mapterhorn/mapterhorn)為 BSD-3-Clause                                               |

`services` 保存樣式 URL 與 `raster-dem` 來源；設定入口見 [catalog.mjs](../../../scripts/gis/basemap/catalog.mjs)。

## 邊界

- 本分類維護服務設定，底圖及 DEM 圖磚由外部服務提供。
- 底圖與高程不參與主題向量篩選；DEM 為地表高程，光效及主題拉伸尺度由共用樣式管理。
- 地形倍率由 MapProjectView.terrain 指定；光效使用 MapLibre 回傳的顯示高程。

## 依賴方向

```text
MapBasemapLayers / MapProjectView → GisSnapshot → basemap manifest
MapLayerRenderer → MapTerrain → MapLibre
MapLightBeams → MapLibre.queryTerrainElevation
```

共用資料契約、查詢與渲染入口見 [GIS 模組](../../../src/gis/README.md)。

## 更新入口

[服務定義](../../../scripts/gis/basemap/catalog.mjs) · [服務紀錄](manifest.json) · [底圖設定](../../../src/gis/layers/MapBasemapLayers.ts) · [地形顯示](../../../src/gis/README.md#3d-顯示)

發布設定：`pnpm gis:refresh --catalog`。
