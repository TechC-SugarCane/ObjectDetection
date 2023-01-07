# ObjectDetection

オブジェクトディテクション用のモデルのトレーニングおよび検証を行うためのリポジトリ

Azure Custom Vision, Yolov7をそれぞれ使用しトレーニングを行う

### Branch

- main: メインのリポジトリモデルの比較を行い性能が良いほうをこのブランチの中に置く。

- Azure: Azure Custom Visionを使ったモデルのトレーニングおよびモデルの検証を行うためのブランチ

- Yolo: Yoloを使ったモデルのトレーニングおよびモデルの検証を行うためのブランチ

### 推論結果

上記のコードは[公式サイト](https://github.com/WongKinYiu/yolov7)のコードを一部変更してローカル環境で実装しています。

推論結果は(こちら)[./runs/detect/exp-yolov7-e6-sugarcane-valid22]

## モデルの検証をローカルで行う方法

ここから先はモデルのトレーニング・テストを自分の環境でやってみたい方向けです。

### 0, 環境

------------------------------------ 
- os: windows 10
- Memory: 32GB
- GPU: NVIDIA RTX3070 (8GB)
- CUDA: 11.6
- PyTorch: 1.12.0+cu116
------------------------------------

### 1, セットアップ

ターミナルを開きこのリポジトリをクローンしてエディターを開きます。

``` git
git clone https://github.com/TechC-SugarCane/ObjectDetection

cd ObjectDetection

code .
```

以下のリンクにアクセスをしてデータセットをダウンロードし、`./ObjectDetection`の中に解凍します<br>
[dataset.zip](https://sugarcane.blob.core.windows.net/backup/dataset.zip)

事前学習済みモデルを以下のリンクからダウンロードします。新しく`checkpoints`のフォルダーを作成して、その中に格納するようにします。<br>
[事前学習済みモデルはこちら](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-d6.pt)


`.venv`のインストールをしてモジュールをインストールします。<br>

```powershell
python -m venv .venv

.venv/Scripts/activate

python -m pip install --upgrade pip

pip install -r requirements.txt
```

`CUDA`が使えるのかを確認します。
``` python
import torch
torch.cuda.is_available()

```

`False`の結果が返ってきた場合、GPUドライバがインストールされているか確認します。<br>
確認できた、既にインストールがされていた場合、ターミナルで以下のコマンドを入力します。
その際仮想環境にインストールしたtorchは一度アインストールします。

``` powershell
pip install torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu116
```

インストールしたらもう一度`CUDA`が使えるか確認します。

### 2 学習の実行

ターミナルで以下のコマンドを入力し学習を開始します。<br>
`--epochs`で学習を行う回数を指定できます。
学習の回数によってかかる時間は変わってきます。

```powershell
python train_aux.py --workers 2 --batch-size 8 \
  --data data/sugarcane.yaml \
  --cfg cfg/training/yolov7-e6.yaml \
  --weights 'checkpoints/yolov7-e6.pt' \
  --name yolov7-e6-candy \
  --hyp data/hyp.scratch.p6.yaml \
  --epochs 300 \
  --device 0
```

### 3 推論を行う

学習が正常に終了したら、以下のコマンドを実装し推論を行います。<br>
結果は`./runs/detect/{自分が指定したフォルダー名}`の中に格納されています

```powershell
python detect.py --weights runs/train/yolov7-e6-candy/weights/best.pt \
  --conf 0.25 --img-size 640 \
  --source dataset/candy/valid/images \
  --name exp-yolov7-e6-candy-valid
```
