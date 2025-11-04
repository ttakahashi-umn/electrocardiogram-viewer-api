# Product Definition: Electrocardiogram Viewer

## 1. Mission

心電図データから心臓発作のリスクを推論し、その結果とともに心電図の波形を医者に提示すること。

## 2. Vision

AIを活用し、血液検査を必要としない心臓発作のリスク評価を提供する。

## 3. Value Proposition

- **医者向け**：血液検査のできない離島や僻地でも心臓発作のリスク評価が可能。
- **非検者向け**：心臓発作のリスクを即座に取得。
- **AI分析**：血液検査を必要としない心臓発作のリスク評価を提供。

## 4. Core Features

| 機能名 | 概要 | 成果物 |
|---------|------|--------|
| 新規心電図ファイル検出 | ファイルの新規追加を監視し、ファイルが追加されたら本システムにそのデータを追加する。 | FileWatcher |
| 心臓発作リスク評価 | 心電図データから心臓発作のリスクを推論する。 | ElectrocardiogramInspector |
| 心臓状態表示 | 心電図と心臓発作リスク評価結果を表示する。 | ElectrocardiogramViewer |
| 被験者検索 | 被験者の検索をし、検索結果を一覧する。 | SubjectSearch |
| 検査データ検索 | 検査データを検索し、検索結果を一覧する。 | ExaminationSearch |

## 5. Differentiation (差別化要素)

- 従来の「血液検査」ではなく、**人間が認知できない心電図の細かい動きから推論**をし、血液検査設備なしでの診断が可能。

## 6. Technical Overview

- **Frontend:** React (UI + グラフ可視化)
- **Backend:** Python FastAPI + TortoiseORM
- **DB:** PostgreSQL
- **AI Layer:** PyTorch

## 7. Success Metrics (KPI)

| 指標 | 目標値 | 測定方法 |
|------|--------|----------|
| 分析時間 | 5分以内 | 平均処理時間計測 |

## 8. Risks & Constraints

- 外部との通信ができない。オフラインで完結する必要がある。
- GPUがない低スペックPCでの推論時のレイテンシ（推論軽量化が課題）
- 推論モデル更新やライブラリバンプアップによる再学習リスク

## 9. Roadmap

| フェーズ | 内容 | 期間 |
|-----------|------|------|
| Phase 1 | 新規心電図ファイル検出、心臓発作リスク評価、心臓状態表示 | 〜2025/11 |
| Phase 2 | 被験者検索、検査データ検索 | 〜2026/1 |

## 10. Future Possibilities

- Apple Watchの心電図による心臓発作リスク評価
- USBによるソフトウェアアップデート
