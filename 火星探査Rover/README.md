# Mars Rover Project - 画像処理とマッピングシステム

このプロジェクトは、火星探査ローバーの画像処理とマッピングシステムの実装を目的としたJupyterノートブックです。ローバーが収集した画像データから、ナビゲーション可能な地形、障害物、岩石サンプルを識別し、世界座標系でのマッピングを行います。

## 📋 プロジェクト概要

このプロジェクトでは以下の機能を実装しています：

- **画像の前処理**: 遠近法変換による画像の正規化
- **色閾値処理**: RGB値に基づく地形分類
- **座標変換**: 画像座標からローバー座標、世界座標への変換
- **マッピング**: ナビゲーション可能な地形、障害物、岩石サンプルの可視化
- **動画生成**: 処理結果の動画出力

## 🖼️ サンプル画像

プロジェクトには以下のサンプル画像が含まれています：

![岩石サンプル例](example_rock1.jpg)

この画像は火星表面の岩石サンプルを示しており、黄色の岩石を識別するための色閾値処理の参考として使用されます。

## 🚀 セットアップ

### 必要なライブラリ

```bash
pip install opencv-python
pip install numpy
pip install matplotlib
pip install scipy
pip install pandas
pip install moviepy
pip install imageio
```

### ディレクトリ構造

```
myportfolio/
├── Rover_Project_Test_Notebook.ipynb  # メインのノートブック
├── example_rock1.jpg                  # 岩石サンプル画像
├── test_dataset/                      # テストデータセット
│   └── IMG/                          # ローバー画像
├── calibration_images/                # キャリブレーション画像
│   ├── example_grid1.jpg             # グリッド画像
│   ├── example_rock1.jpg             # 岩石画像
│   └── map_bw.png                    # グランドトゥルースマップ
└── output/                           # 出力動画
    └── test_mapping.mp4
```

## 📖 ノートブックの使用方法

### 1. 基本的な実行手順

1. ノートブックの各セルを順番に実行
2. シミュレーターで「Training Mode」でデータを記録
3. データディレクトリパスを設定
4. 提供された関数をテスト
5. 新しい関数を実装して障害物と岩石サンプルを検出
6. `process_image()`関数を実装
7. 動画出力の生成

### 2. 主要な関数

#### 遠近法変換
```python
def perspect_transform(img, src, dst):
    M = cv2.getPerspectiveTransform(src, dst)
    warped = cv2.warpPerspective(img, M, (img.shape[1], img.shape[0]))
    return warped
```

#### 色閾値処理
```python
def color_thresh(img, rgb_thresh=(160, 160, 160)):
    color_select = np.zeros_like(img[:,:,0])
    above_thresh = (img[:,:,0] > rgb_thresh[0]) \
                & (img[:,:,1] > rgb_thresh[1]) \
                & (img[:,:,2] > rgb_thresh[2])
    color_select[above_thresh] = 1
    return color_select
```

#### 座標変換
- `rover_coords()`: 画像座標からローバー座標への変換
- `to_polar_coords()`: 極座標への変換
- `pix_to_world()`: 世界座標への変換

## 🎯 実装すべき機能

### 1. 障害物検出
- ナビゲーション可能な地形の閾値処理を反転
- 障害物の位置を世界座標でマッピング

### 2. 岩石サンプル検出
- 黄色の岩石を識別する色閾値処理
- RGB値の範囲を調整して岩石を検出
- 岩石の位置を世界座標でマッピング

### 3. マッピング機能
- ナビゲーション可能な地形（緑色）
- 障害物（赤色）
- 岩石サンプル（青色）

## 📊 データ処理フロー

1. **画像読み込み**: ローバーが撮影した画像を読み込み
2. **遠近法変換**: 画像を鳥瞰図に変換
3. **色閾値処理**: 地形、障害物、岩石を分類
4. **座標変換**: 画像座標から世界座標への変換
5. **マッピング**: 世界マップの更新
6. **可視化**: 処理結果の表示

## 🎬 動画出力

ノートブックの最後で、処理された画像から動画を生成します：

```python
from moviepy.editor import ImageSequenceClip

clip = ImageSequenceClip(data.images, fps=60)
new_clip = clip.fl_image(process_image)
new_clip.write_videofile(output, audio=False)
```

## 🔧 カスタマイズ

### 色閾値の調整
岩石検出の精度を向上させるため、RGB閾値を調整できます：

```python
# 岩石検出用の色閾値（例）
rock_thresh = (100, 100, 50)  # 黄色の岩石用
```

### 座標変換パラメータ
ローバーの位置と向きに応じて座標変換パラメータを調整：

```python
# ソースポイントとデスティネーションポイントの調整
source = np.float32([[14, 140], [301, 140], [200, 96], [118, 96]])
destination = np.float32([...])  # 適切な値に調整
```

## 📈 期待される結果

- ナビゲーション可能な地形の正確な識別
- 障害物の位置特定
- 岩石サンプルの検出と位置記録
- 世界座標系でのマッピング
- 処理過程の可視化動画

## 🚨 注意事項

- シミュレーターで長時間のデータ記録を行うとクラッシュする可能性があります
- 表示ウィンドウがフリーズした場合は、カーネルを再起動してください
- OpenCVはBGR色空間を使用するため、RGBとの変換に注意が必要です

## 📚 参考資料

- [OpenCV Python Tutorials](http://opencv-python-tutroals.readthedocs.io/)
- [MoviePy Documentation](https://zulko.github.io/moviepy/)
- [Matplotlib Documentation](https://matplotlib.org/)

## 🤝 貢献

このプロジェクトは教育目的で作成されています。改善提案やバグ報告は歓迎します。

## 📄 ライセンス

このプロジェクトはMITライセンスの下で公開されています。
