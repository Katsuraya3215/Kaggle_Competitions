# Titanic Survival Prediction
Kaggleにおける「Titanic - Machine Learning from Disaster」コンペティション用のデータ分析・機械学習リポジトリです。
タイタニック号の乗客データから、各乗客が"生存したか否か"を予測するモデルを構築しています。

## 成果・スコア
- **最高Kaggle Piblic Score:0.78947**
- **順位:1153位/11100人 上位10.4%**
- **最終再現モデルのスコア Kaggle Public Score (Accuracy):** 0.78229
- **交差検証 (Accuracy):** 0.84286
- **主要採用モデル:** XGBClassifier　再現モデル:XGBClassifier

  最高スコアは「09_789_model-tuning.ipynb」で記録しました。
  その後、処理を整理して最終モデルを再構築したところ、同様の構成でもスコアに小さな差が
  発生しました。この経験から、乱数固定や実行環境を含めた再現性の重要性を学びました。

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
│    └── 782_final_model.ipynb
│ 
├── output/
│   └── final_model_submission.csv                # Kaggle提出用ファイル
│
├── .gitignore
├── README.md           # 本説明書
└── requirements.txt    # 必要なPythonライブラリの一覧

```


** 実験記録とアプローチ (Approach & Validation)
ローカル環境での交差検証（Cross Validation: 5-Fold）のスコア推移です。

* 01	RandomForestClassifier	ベースライン（カテゴリ列のみ）		Public Score:0.778	まず動くものを作成
* 02	RandomForestClassifier	数値列のみ使用・中央値で欠損補完	　PS:0.706	予測正解率が大幅にダウン
* 04	LogisticRegression 新特徴量と数値列、カテゴリ列を加えて別のモデル作成　検証score:0.827 PS:0.768	点数は伸びず
* 07	XGBClassifier	Pipelineの作成　全行程を1つにまとめる	PS:0.773
* 09 LogisticRegression/RandomForest/XGBoost 全モデルの比較検証,XGBoostが最適であった　検証スコア:0.8496 PS:0.789  ※最高スコアモデル
* 10,11 XGBClassifier 特徴量の作成、欠損補完、パラメータ探索など全ての調査　PS:0.770~0.785 結果はでず
* final_model XGBClassifier　最高スコアモデルをキレイにまとめて再現　PS:0.782 ※最終再現モデル

💡 工夫したポイント
* notebookの01(性差による分別：特徴量"Sex"重視スコア=0.778)，02(数値特徴量のみ、スコア=0.706)の試作より、性別に基づく特徴量が正確性に大きな影響を及ぼしていることを確認。特徴量選びがスコアに与える影響の大きさを認識したので、特徴量の作成と取捨選択を工夫しました。
* 特徴量エンジニアリング: Name から抽出した敬称(Title)が生存率に強く相関していることをEDAで発見し、組み込みました。















