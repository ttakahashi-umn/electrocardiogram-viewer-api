# Project Structure: Swing Insight

## 1. Directory Layout

```bash
swing-insight/
├── backend/ # Python: FastAPI + TortoiseORM
│   ├── api/
│   │ ├── controllers/
│   │ │ └── examination.py # スコアリングAPI
│   │ ├── use_cases/
│   │ │ └── examination.py # スコアリングAPI
│   │ ├── domains/
│   │ │ └── examination.py # スコアリングAPI
│   │ ├── main.py # FastAPIエントリポイント
│   │ └── config.py # 設定・CORS管理
│   ├── service/
│   │ ├── main.py # 姿勢推定 (PyTorch)
│   │ ├── inference.py # AIスコア算出モジュール
│   │ └── utils.py # 汎用ユーティリティ
│   ├── db/
│   │ ├── repositories/
│   │ │ ├── examination.py # AIスコア算出モジュール
│   │ │ ├── inference_result.py # AIスコア算出モジュール
│   │ │ └── electrocardiogram.py # スコアリングAPI
│   │ ├── models.py # ORMモデル定義 (Tortoise)
│   │ ├── schemas.py # Pydanticスキーマ
│   │ └── migrations/ # DBマイグレーション
│   ├── tests/
│   ├── pyproject.toml # uv 管理設定
│   └── README.md
├── frontend/ # TypeScript: React (Vite)
│   ├── src/
│   │   ├── components/
│   │   │   ├── ExaminationList.tsx
│   │   │   └── Examination.tsx
│   │   ├── pages/
│   │   │   ├── index.tsx
│   │   │   └── examination.tsx
│   │   ├── hooks/
│   │   │   └── useExaminationAPI.ts
│   │   ├── api/
│   │   │   └── client.ts # Axios/Fetch設定 (BaseURL = backend)
│   │   ├── types/
│   │   │   └── examination.d.ts # Analysis 型定義
│   │   ├── context/
│   │   │   └── AnalysisContext.tsx
│   │   ├── assets/
│   │   └── main.tsx
│   ├── public/
│   │   ├── favicon.ico
│   │   └── manifest.json
│   ├── package.json
│   ├── tsconfig.json
│   └── vite.config.ts
├── .spec-workflow/ # Spec Workflow MCP 用ディレクトリ
│   ├── steering/
│   │   ├── product.md
│   │   ├── tech.md
│   │   └── structure.md
│   └── specs/
│       ├── examination/
│       ├── file_detector/
│       └── inference/
├── docker/
│   ├── backend.Dockerfile
│   ├── frontend.Dockerfile
│   └── compose.yaml                   # 開発時連携用
├── docs/
│   ├── architecture.md
│   ├── api_reference.md
│   └── ui_design.md
└── README.md
```

## 2. Module Dependencies

```mermaid
graph TD
    Reract UI --> FastAPI
    FastAPI --> Engine
    Engine --> DB
    Engine --> AIModels
    DB --> PostgreSQL
```

依存方向は 一方向のみ。UI → API → Engine → DB の流れを厳守する。

## 3. Layered Responsibility

Layer	主な責務	技術
UI	動画表示・分析結果の可視化	Streamlit
API	フロントからの要求を受け、解析を実行	FastAPI
Engine	姿勢解析・モーション比較・スコア算出	Mediapipe, TensorFlow Lite
ORM	DBアクセス層	TortoiseORM
DB	永続データ保存	PostgreSQL

## 4. Data Objects and Flow
データ名	形式	生成元	利用先
pose_data.json	JSON	MediapipeEngine	PhaseDetector / Comparator
metrics.json	JSON	Analyzer	DB / Streamlit
video_path	str	UI Upload	FastAPI / FFmpeg
score	float	AI Model	Streamlit Chart

## 5. Naming Conventions
対象	命名規則	例
Python Modules	スネークケース	phase_detector.py
Classes	パスカルケース	SwingAnalyzer
Functions	スネークケース	detect_phase()
DB Tables	複数形・スネークケース	analysis_results
JSON Keys	lowerCamelCase	poseData, frameIndex

## 6. Coding Standards
PEP8 準拠（最大行長 100）

Docstring は Google Style

型ヒント必須（mypy通過をCIで強制）

1モジュール1責務原則

DB操作はすべて ORM 経由。生SQL禁止。

## 7. Directory Responsibility Matrix

ディレクトリ	役割	関連Spec
engine/	核となる解析ロジック	specs/motion_analysis/
ui/	可視化UI層	specs/ui_display/
db/	データ定義・永続層	specs/data_model/
api/	通信・処理制御層	specs/api_interface/
tests/	品質保証	specs/testing/

## 8. Build & Run Hierarchy

```bash
# ローカル起動
make run-dev

# API サーバー起動
uvicorn api.main:app --reload

# Streamlit 起動
streamlit run ui/main_app.py

# テスト
pytest
```

## 9. Dependency Map (技術モジュール単位)

上位	下位依存	理由
ui/main_app.py	api/routes/*	API 呼び出し
api/routes/analyze.py	engine/mediapipe_processor.py	姿勢推定処理
engine/phase_detector.py	db/models.py	結果保存
engine/scoring.py	engine/mediapipe_processor.py	入力データ取得
db/models.py	PostgreSQL	永続化

## 10. Future Structural Scalability

core/ ディレクトリを新設し、AIロジックをモジュール化
mobile/ ディレクトリで SwiftUI 向けクライアント統合予定

API を GraphQL 化し、複合クエリ対応

## 11. Traceability Integration

各 Spec (specs/*/requirements.md) は、対応するモジュールを structure.md で参照
MCP ダッシュボード上で “Spec → Code → Task” のリンクを自動生成

例：

```bash
specs/motion_comparison/ → engine/comparator.py
specs/impact_detection/ → engine/phase_detector.py
specs/scoring_model/ → engine/scoring.py
```

## 12. Maintenance Rules

- 新しいモジュールを追加する場合、structure.md の該当セクションに追記必須。
- 削除されたディレクトリはコメントアウトせず削除。
- product.md の Core Feature に未定義のディレクトリを追加しない。
