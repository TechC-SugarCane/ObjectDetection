# YOLOv7のファインチューニング

公式のリポジトリからフォークして、独自のデータセットでファインチューニングを行うためのリポジトリです。

フォーク元: [WongKinYiu/yolov7](https://github.com/WongKinYiu/yolov7)

## 環境

- Python: 3.10.11
- CUDA: 11.8

## Setup

### 1. リポジトリをクローン

```sh
git clone git@github.com:TechC-SugarCane/train-YOLOv7.git

cd train-YOLOv7
```

### 2. Pythonの環境構築

`pyenv`を使うので、パソコンに入っていない人は[CONTRIBUTING.md](https://github.com/TechC-SugarCane/.github/blob/main/CONTRIBUTING.md#pyenv-pyenv-win-%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)を参考にしながらインストールしてください。

```bash
pyenv install
```

### 3. 仮想環境を作成

```bash
python -m venv .venv
```

### 4. 仮想環境を有効化

```bash
# mac
source .venv/bin/activate

# windows
.venv\Scripts\activate
```

※ 環境から抜ける場合は、`deactivate`コマンドを実行してください。

### 5. 依存パッケージをインストール

```bash
# CPUで推論を行う場合
pip install -r requirements-cpu.txt

# GPUで推論を行う場合
pip install -r requirements-gpu.txt
```

## Training

事前学習済みモデルとして`yolov7-d6.pt`を使用するので、[こちら](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-d6.pt)からダウンロードして`weights`ディレクトリに配置してください。

### 1. データセットのダウンロード

データセットは 一階層上に[manage-datasetリポジトリ](https://github.com/TechC-SugarCane/manage-dataset)をcloneし、[manage-dataset/README](https://github.com/TechC-SugarCane/manage-dataset/blob/main/README.md)に従ってダウンロードしてください。

```shell
# clone済みの人はスキップ
cd ..
git clone git@github.com:TechC-SugarCane/manage-dataset.git
```

### 2. 学習の実行

ターミナルで以下のコマンドを入力し学習を開始します。<br>
`--epochs`で学習を行う回数を指定できます。
学習の回数によってかかる時間は変わってきます。

```sh
# sugarcane
# タスク的にはp5のtrain.pyを使うべきですが、
# train.pyだとエラーが出るので、tiny以外はtrain_aux.pyを使います
python train_aux.py --workers 8 --batch-size 8 --data ../manage-dataset/datasets/sugarcane/data.yaml --cfg cfg/training/yolov7-d6.yaml --weights weights/yolov7-d6.pt --name yolov7-d6-sugarcane --hyp data/hyp.scratch.sugarcane.yaml --epochs 250 --device 0

# pineapple
python train_aux.py --workers 8 --batch-size 8 --data ../manage-dataset/pineapple/data.yaml --cfg cfg/training/yolov7-d6.yaml --weights weights/yolov7-d6.pt --name yolov7-d6-pineapple --hyp data/hyp.scratch.pineapple.yaml --epochs 250 --device 0
```

学習後の結果は`runs/train/<name(番号)>`に保存されます。

### [補足] yolov7, yolov7x, yolov7-tinyを学習させる場合

train_aux.pyを使うとエラーが出るので、train.pyを使います。

また、tinyを学習させる場合は `--hyp` に `data/hyp.scratch.tiny.yaml` を指定してください。

```sh
# sugarcane
python train.py --workers 8 --batch-size 8 --data ../manage-dataset/datasets/sugarcane/data.yaml --cfg cfg/training/yolov7.yaml --weights weights/yolov7.pt --name yolov7-sugarcane --hyp data/hyp.scratch.sugarcane.yaml --epochs 250 --device 0

# pineapple
python train.py --workers 8 --batch-size 8 --data ../manage-dataset/datasets/pineapple/data.yaml --cfg cfg/training/yolov7.yaml --weights weights/yolov7.pt --name yolov7-pineapple --hyp data/hyp.scratch.pineapple.yaml --epochs 250 --device 0
```

## Export

モデルをONNXにexportする際は下記コマンドを実行してください

```sh
# --weightsの<name(番号)>の部分は学習の結果が保存されているディレクトリ名にしてください。
python export.py　--weights ./runs/train/<name(番号)>/weights/best.pt --grid --end2end --dynamic --max-wh 640 --device 0
```

## コントリビューター向けガイドライン

コントリビューター向けのガイドラインについては、こちらの[CONTRIBUTING.md](https://github.com/TechC-SugarCane/.github/blob/main/CONTRIBUTING.md)を参照してください。
