# Titanic Survival Prediction
Kaggleにおける「Titanic - Machine Learning from Disaster」コンペティション用のデータ分析・機械学習リポジトリです。
タイタニック号の乗客データから、各乗客が"生存したか否か"を予測するモデルを構築しています。

## 成果・スコア
- **最終スコア Kaggle Public Score (Accuracy):** 0.78229
- **交差検証 (Accuracy):** 0.84286
- **主要採用モデル:** XGBClassifier

## プロジェクトの目的
このプロジェクトでは、機械学習の基本的な分析手順を一通り実践しました。
- データの確認と可視化
- 欠損値の処理
- 特徴量エンジニアリング
- 複数モデルの比較
- 交差検証
- ハイパーパラメータ調整
- Pipelineによる前処理の一元化

## 使用技術
- Python
- pandas
- NumPy
- Matplotlib
- Seaborn
- scikit-learn
- XGBoost
- Optuna
- Jupyter Notebook

## 分析・モデル構築の流れ
1. データの概要と欠損値を確認
2. 生存率と各特徴量の関係を可視化
3. 欠損値を補完
4. 新しい特徴量を作成
5. 複数のモデルを交差検証で比較
6. Optunaでハイパーパラメータを探索
7. 最終モデルで予測結果を作成

## 主な特徴量エンジニアリング
- `Title`：乗客名から敬称を抽出
- `Family`：兄弟・配偶者・親子の人数から家族人数を作成
- `Age`：Title別に年齢の中央値で、欠損を補完
- `age_binning`：年齢帯を4つに区分
- `logFare`：運賃の偏りを小さくするため対数変換
- `has_cabin`：客室番号を持ち合わせているか否かの情報

とくに、性別、年齢帯や性別と結びつく敬称、家族の有無が生存予測に重要だと考えました。

## モデル別比較
詳しくは、09 model_tuning に記載。同じ前処理、同じ特徴量で検証し、パラメータはそれぞれ探索した結果。

| モデル | 交差検証 Accuracy |
|---|---:|
| LogisticRegression | 0.8024543343167408 |
| RandomForestClassifier | 0.8372544096415794 |
| XGBClassifier | 0.84286 |

交差検証の結果とkaggleスコアを比較し、最終的にXGBClassifierを採用しました。

---

## 📁 フォルダ構成 (Directory Structure)

```text
project/
│
├── data/
│   └── ※ Kaggleのデータセットはリポジトリに含めていません。コンペページからダウンロードしてください。
│ 
├── notebooks/          # データの可視化（EDA）や単発の実験用ノートブック
│   ├── 01_778_getting_started_with_titanic.ipynb #練習用
│   ├── 02_706_missing_data_Imputation.ipynb      #欠損値処理
│   ├── 03_eda.ipynb                              #データ分析
│   ├── 04_768_baseline_model.ipynb               #基本ライン
│   ├── 05_782_randomforestclassifier.ipynb       #Random Forest
│   ├── 06_770_xgbclassifier.ipynb                #XGBoost
│   ├── 07_773_pipeline.ipynb                     #Pipelineの導入
│   ├── 08_feature_engineering.ipynb              #特徴量エンジニアリング
│   ├── 09_789_model_tuning.ipynb                 #パラメータ探索
│   ├── 10_785_xgbclassifier_ver2.ipynb           #改善モデル
│   └── 11_770_xgbclassifier_ver3.ipynb           #追加検証
│   
├── src/                # 最終採用モデル
│    └── final_model.ipynb
│ 
├── output/
│   └── submission.csv                # Kaggle提出用ファイル
│
├── .gitignore
├── README.md           # 本説明書
└── requirements.txt    # 必要なPythonライブラリの一覧

```


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














