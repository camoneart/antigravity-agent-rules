---
trigger: model_decision
description: このルールファイルは、このリポジトリでNext.jsのプロジェクトを実装する際にGeminiへの指針を提供するためのものです。Next.jsのプロジェクト実装時には必ず必ず必ず必ず発動。
---

## リファレンス

`.agent/rules/nextjs-bestpractice-rule/docs/`

リファレンスの設計思想に必ず必ず必ず必ず従うこと。

このルールファイルの各セクションで、URLパスを指定してる箇所が複数あるが、全て `.agent/rules/nextjs-bestpractice-rule/docs/` の中にあるので必ず必ず必ず必ず参照すること。

## 開発コマンド

### コアコマンド
- `pnpm dev` - Turbopack付きの開発サーバーを起動
- `pnpm build` - 本番用ビルド
- `pnpm start` - 本番サーバーを起動
- `pnpm test:run` - Vitestでテストを実行
- `pnpm check:fix` - Biomeのチェックと自動修正を実行
- `pnpm typecheck` - TypeScriptの型チェック

### テスト
- `pnpm test:run`でVitestを使用してテスト
- テスト環境はjsdomで構成
- セットアップファイル: `vitest.setup.ts`
- テストはコンポーネントと同じ場所に配置（例：`src/app/page.test.tsx`）

## アーキテクチャ

App Routerを使用したミニマルなNext.jsアプリケーション：

### 技術スタック
- **フレームワーク**: Next.js（最新版）with App Router
- **スタイリング**: Tailwind CSS v4
- **テスト**: Vitest + React Testing Library
- **リンティング・フォーマット**: Biome
- **パッケージマネージャー**: pnpm

### プロジェクト構造
- `src/app/` - Next.js App Routerのページとコンポーネント
- `.agent/rules/nextjs-bestpractice-rule/docs/` - ドキュメントベース（Biomeチェック対象外）
- ルートレベルの設定ファイル

### 主要機能
- データ取得最適化のためのDataLoader
- Server-onlyコンポーネントサポート
- スタイリングのためのTailwind CSS
- 日本語サポート（layoutでlang="ja"）

### コードスタイル
- Biome設定による強制：
  - 文字列にダブルクォート
  - 常にセミコロン
  - 末尾カンマ
  - 2スペースインデント
  - オブジェクトキーのソート

## テスト戦略
- テストはコンポーネントと同じ場所に配置
- テストは`.agent/rules/nextjs-bestpractice-rule/docs/testing/frontend-unit-testing.md`を必ず必ず必ず必ず参照し、AAAパターンで記述
- コンポーネントテストにはReact Testing Libraryを使用
- Vitest設定にはjsdom環境を含む
- セットアップファイルでテスト設定を処理

## ドキュメント・ナレッジベース

`.agent/rules/nextjs-bestpractice-rule/docs/`ディレクトリには、React・Next.js・テストに関する包括的なベストプラクティスドキュメントが含まれています。

### 主要ドキュメント構成

#### 1. Next.js基本原理ガイド (`.agent/rules/nextjs-bestpractice-rule/docs/nextjs-basic-principle/`)
Next.js App Routerの包括的なガイド（36章構成）：

**Part 1: データ取得 (11章)**
- **参照タイミング**: データ取得パターンを実装する際
- **主要ファイル**:
  - `part_1_server_components.md` - Server Components設計の基本
  - `part_1_colocation.md` - データ取得の配置戦略
  - `part_1_request_memoization.md` - リクエスト最適化
  - `part_1_concurrent_fetch.md` - 並行データ取得
  - `part_1_data_loader.md` - DataLoaderパターン
  - `part_1_fine_grained_api_design.md` - API設計戦略
  - `part_1_interactive_fetch.md` - インタラクティブなデータ取得

**Part 2: コンポーネント設計 (5章)**
- **参照タイミング**: コンポーネント設計・リファクタリング時
- **主要ファイル**:
  - `part_2_client_components_usecase.md` - Client Components使用指針
  - `part_2_composition_pattern.md` - コンポジションパターン
  - `part_2_container_presentational_pattern.md` - Container/Presentational分離
  - `part_2_container_1st_design.md` - Container優先設計

**Part 3: キャッシュ戦略 (6章)**
- **参照タイミング**: パフォーマンス最適化・キャッシュ制御時
- **主要ファイル**:
  - `part_3_static_rendering_full_route_cache.md` - 静的レンダリング最適化
  - `part_3_dynamic_rendering_data_cache.md` - 動的レンダリング制御
  - `part_3_router_cache.md` - クライアントサイドキャッシュ
  - `part_3_data_mutation.md` - データ変更とキャッシュ無効化
  - `part_3_dynamicio.md` - 実験的キャッシュ改善

**Part 4: レンダリング戦略 (4章)**
- **参照タイミング**: レンダリング最適化・Streaming実装時
- **主要ファイル**:
  - `part_4_pure_server_components.md` - Server Component純粋性
  - `part_4_suspense_and_streaming.md` - プログレッシブローディング
  - `part_4_partial_pre_rendering.md` - 部分的事前レンダリング

**Part 5: その他の実践 (4章)**
- **参照タイミング**: 認証・エラーハンドリング実装時
- **主要ファイル**:
  - `part_5_request_ref.md` - リクエスト・レスポンス参照
  - `part_5_auth.md` - 認証・認可パターン
  - `part_5_error_handling.md` - エラーハンドリング戦略

#### 2. 単体テスト (`.agent/rules/nextjs-bestpractice-rule/docs/testing/`)
**フロントエンド単体テスト** (`.agent/rules/nextjs-bestpractice-rule/docs/testing/frontend-unit-testing.md`)
- **参照タイミング**: テスト戦略策定・テスト実装時
- **内容**: 
  - Classical vs London school テスト手法
  - AAA（Arrange, Act, Assert）パターン
  - Storybookとの統合（`composeStories`）
  - テスト命名規則・共通セットアップパターン

### 参照ガイドライン

**参照タイミング**:
- 実装時には関連するドキュメントを必ず必ず必ず必ず参照する
- ドキュメントを参照したら、「📖{ドキュメント名}を読み込みました」と必ず必ず必ず必ず出力すること

**機能実装時の参照優先順位**:
1. **データ取得実装** → Part 1のドキュメント群を参照
2. **コンポーネント設計** → Part 2のパターンを適用
3. **パフォーマンス最適化** → Part 3のキャッシュ戦略を活用
4. **レンダリング最適化** → Part 4のStreaming・PPR戦略を参照
5. **認証・エラーハンドリング** → Part 5の実践パターンを適用
6. **テスト実装** → `articles/frontend-unit-testing.md`を参照

**重要な設計原則**:
- **Server-First**: Server Componentsを優先し、必要時にClient Componentsを使用
- **データ取得の配置**: データを使用するコンポーネントの近くでデータ取得を実行
- **コンポジション**: 適切なコンポーネント分離とコンポジションパターンの活用
- **プログレッシブ強化**: JavaScript無効時でも機能する設計を心がける