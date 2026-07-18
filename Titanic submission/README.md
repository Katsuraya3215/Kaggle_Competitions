# Titanic Submission
Kaggleにおける「Titanic - Machine Learning from Disaster」コンペティション用のデータ分析・機械学習リポジトリになります。
タイタニック号の乗客データから、各乗客が"生存したか否か"を予測するモデルを構築しています。

## 📊 成果・スコア
- **最終スコア (Accuracy):** 0.78229
- **主要採用モデル:** XGBClassifier

---

## 📁 フォルダ構成 (Directory Structure)

```text
project/
│
├── data/
│ 　　├── raw/ # 加工前のオリジナルデータ
│     │　　├── train.csv
│     │    ├── test.csv
│     └──  └── gender_submission.csv
│ 
├── notebooks/          # データの可視化（EDA）や単発の実験用ノートブック
│   ├── 01_778_getting_started_with_titanic.ipynb #練習用
│   ├── 02_706_missing_data_Imputation.ipynb
│   ├── 03-eda.ipynb                              #データ分析
│   ├── 04_768_baseline_model.ipynb               #基本ライン
│   ├── 05_782_randomforestclassifier.ipynb       
│   ├── 06_770_xgbclassifier.ipynb
│   ├── 07_773_pipeline.ipynb
│   ├── 08_feature_engineering.ipynb              #特徴量エンジニアリング
│   ├── 09_789_model-tuning.ipynb                 #パラメータ探索
│   ├── 10_785_xgbclassifier-ver2.ipynb
│   └── 11_770_xgbclassifier-ver3.ipynb
│   
├── src/                # 最終採用モデル
│    └── final_model 
│
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
・notebookの01(性差による分別：特徴量"Sex"重視スコア=0.778)，02(数値特徴量のみ、スコア=0.706)の試作より、性別に基づく特徴量が正確性に大きな影響を及ぼしていることを確認。文字列特徴量の数値への落とし込みを重点的に進めていく
・特徴量エンジニアリング: Name から抽出した敬称が生存率に強く相関していることをEDAで発見し、features.py に組み込みました。

再現性の担保: シード値（seed=42）やハイパーパラメータを config.yaml で一元管理し、誰が実行しても同じスコアが出るように設計しました。














