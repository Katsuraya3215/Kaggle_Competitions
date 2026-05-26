# Titanic Submission
Kaggleにおける「Titanic - Machine Learning from Disaster」コンペティション用のデータ分析・機械学習リポジトリになります。
タイタニック号の乗客データから、各乗客が"生存したか否か"を予測するモデルを構築しています。

## 📊 成果・スコア
- **最終スコア (Accuracy):** 0.79425 (Top 10% 相当)
- **主要モデル:** LightGBM, TensorFlow Decision Forests (TF-DF)

---

## 📁 フォルダ構成 (Directory Structure)

```text
project/
│
├── data/               # Kaggleからダウンロードした生データ（Git管理対象外）
├── notebooks/          # データの可視化（EDA）や単発の実験用ノートブック
│   ├── 01_eda.ipynb
│   └── 02_baseline.ipynb
├── src/                # 本番用ソースコード
│   ├── features.py     # データの前処理・特徴量エンジニアリング
│   ├── train.py        # モデルの学習と重みの保存
│   └── inference.py    # 学習済みモデルを使った予測ファイルの作成
│
├── models/             # 学習済みモデルの保存先（Git管理対象外）
├── README.md           # 本説明書
├── requirements.txt    # 必要なPythonライブラリの一覧
└── config.yaml         # ハイパーパラメータ等の設定ファイル
```

#Datasets
以下のCSVファイルより、Data参照
/kaggle/input/competitions/titanic/train.csv
/kaggle/input/competitions/titanic/test.csv
/kaggle/input/competitions/titanic/gender_submission.csv

🛠️ 環境構築 (Setup)
以下のコマンドを実行して、実行に必要な仮想環境の構築とライブラリのインストールを行ってください。

Bash
# リポジトリのクローン
git clone [https://github.com/Katsuraya3215/titanic-submission.git](https://github.com/Katsuraya3215/titanic-submission.git)
cd titanic-submission

# 必要なライブラリのインストール
pip install -r requirements.txt

⚠️ 注意: data/ フォルダの中に、Kaggle公式からダウンロードした train.csv および test.csv を配置してから以降の手順を実行してください。

🚀 実行手順 (How to Run)
本リポジトリは、以下の順にスクリプトを実行することで、前処理から予測ファイルの出力までが自動で完結します。

1. モデルの学習 (Train)
src/train.py を実行すると、データの前処理（features.py の呼び出し）が行われ、モデルの学習が始まります。学習済みモデルは models/ に保存されます。

python -m src.train

2. 推論と提出ファイルの作成 (Inference)
src/inference.py を実行すると、学習済みモデルを使ってテストデータの予測を行い、提出用の submission.csv をルートディレクトリに出力します。

Bash
python -m src.inference

🧪 実験記録とアプローチ (Approach & Validation)
ローカル環境での交差検証（Cross Validation: 5-Fold）のスコア推移です。

Exp No.	モデル	施策・特徴量エンジニアリング	Local CV	Public Score	備考
01	LightGBM	ベースライン（数値特徴量のみ）	0.762	0.755	まず動くものを作成
02	LightGBM	Name列から敬称(Mr/Miss)を抽出	0.785	0.779	予測に大きく貢献
03	TF-DF	決定木によるテキストトークン化	0.791	0.784	表現力が向上
04	Ensemble	Exp02 と Exp03 のアンサンブル	0.801	0.794	★最終採用モデル

💡 工夫したポイント
特徴量エンジニアリング: Name から抽出した敬称が生存率に強く相関していることをEDAで発見し、features.py に組み込みました。

再現性の担保: シード値（seed=42）やハイパーパラメータを config.yaml で一元管理し、誰が実行しても同じスコアが出るように設計しました。














