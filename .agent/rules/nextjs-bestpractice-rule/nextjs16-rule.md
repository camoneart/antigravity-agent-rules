---
trigger: model_decision
description: プロジェクト内のNext.jsのバージョンが16を使用してる場合、必ず必ず必ず必ず発動。
---

## 概要
Next.js 16 プロジェクトにおける実装ルールとベストプラクティス。
このバージョンでは「**Dynamic by Default (デフォルトで動的)**」かつ「**Explicit Caching (明示的なキャッシュ)**」が基本原則となる。

## 1. キャッシュ戦略 (Cache Components)
**原則: キャッシュはオプトイン（明示的）に行う**

### 1.1 設定
`next.config.ts` で `cacheComponents: true` を有効化する必要がある。

```typescript
import type { NextConfig } from 'next'
const nextConfig: NextConfig = {
  cacheComponents: true,
}
export default nextConfig
```

### 1.2 `"use cache"` ディレクティブ
ページ、コンポーネント、関数レベルでキャッシュを有効化する。
- **制約**: 引数と戻り値はシリアライズ可能である必要がある（Class インスタンス等は不可）。
- **制約**: `cookies()`, `headers()` などのランタイム API はキャッシュスコープ内で直接呼べない（引数として渡す必要がある）。

```typescript
// File level
"use cache"
export default async function Page() { ... }

// Component level
export async function MyComponent() {
  "use cache"
  return <></>
}

// Function level
export async function getData() {
  "use cache"
  const data = await fetch('/api/data')
  return data
}
```

### 1.3 キャッシュ寿命 (`cacheLife`)
`cacheLife` 関数を使用して、キャッシュの寿命（stale, revalidate, expire）を制御する。
プリセットプロファイルを使用することを推奨する。

- **プリセット**:
  - `seconds`: リアルタイムデータ (株価など)
  - `minutes`: 頻繁に更新 (ニュースフィード)
  - `hours`: 数回の更新/日 (天気、在庫)
  - `days`: 日次更新 (ブログ記事)
  - `weeks`: 週次更新
  - `max`: ほぼ変更なし (アーカイブ)

```typescript
import { cacheLife } from 'next/cache'

export async function ProductPage() {
  "use cache"
  cacheLife('hours') // 推奨: 意味のあるプリセット名を使う
  // cacheLife({ stale: 300 }) // インライン設定も可能だが、configでの定義推奨
  return <div>...</div>
}
```

### 1.4 キャッシュタグ (`cacheTag`)
オンデマンドな無効化のために、キャッシュエントリにタグを付ける。

```typescript
import { cacheTag } from 'next/cache'

export async function getData() {
  "use cache"
  cacheTag('products', 'featured') // 複数指定可能
  return fetch('/api/data')
}
```

## 2. データ更新と再検証 (Server Actions)
**原則: 用途に応じた適切な API を使い分ける**

### 2.1 `updateTag(tag)` (Read-Your-Writes)
- **用途**: Server Actions 内でのみ使用可能。
- **挙動**: キャッシュを**即座に無効化**し、次のリクエスト（リダイレクト先など）で**最新データを待機して取得**する。
- **シナリオ**: フォーム送信後、ユーザーに自分の変更を即座に見せたい場合。

```typescript
'use server'
import { updateTag } from 'next/cache'
import { redirect } from 'next/navigation'

export async function updateProfile() {
  await db.update(...)
  updateTag('user-profile') // 即座に反映
  redirect('/profile')
}
```

### 2.2 `revalidateTag(tag, profile)` (Stale-While-Revalidate)
- **用途**: Server Actions, Route Handlers で使用可能。
- **必須**: 第2引数に `cacheLife` プロファイル（通常は `'max'`）を指定する。
- **挙動**: キャッシュを **Stale (古い)** とマークする。次のアクセス時は古いデータを返しつつ、裏で再検証を行う。
- **シナリオ**: CMS の Webhook 受信時や、即時性が求められない更新。

```typescript
import { revalidateTag } from 'next/cache'

export async function webhook() {
  // 古いデータを返しつつ裏で更新
  revalidateTag('posts', 'max') 
}
```

## 3. ミドルウェアとプロキシ
**原則: `middleware.ts` ではなく `proxy.ts` を使用する**

- **`proxy.ts`**:
  - Node.js ランタイムで動作する。
  - `src/proxy.ts` またはルートに配置。
  - `middleware.ts` は Edge ランタイムが必要な場合のみ使用する。

```typescript
// proxy.ts
import { NextRequest, NextResponse } from 'next/server'

export default function proxy(request: NextRequest) {
  // Request 介入ロジック
  return NextResponse.next()
}
```

## 4. 非同期 API の待機
以下の API は必ず `await` すること。
- `cookies()`, `headers()`, `draftMode()`
- `params`, `searchParams`

## 5. 移行ガイド (非推奨・削除)
以下の設定は Next.js 16 + Cache Components では不要または非推奨となるため削除する。

- `export const dynamic = 'force-dynamic'` -> **削除** (デフォルトで動的)
- `export const dynamic = 'force-static'` -> **削除** (`"use cache"` + `cacheLife('max')` に置き換え)
- `export const revalidate = 3600` -> **削除** (`cacheLife` に置き換え)
- `export const fetchCache = 'force-cache'` -> **削除** (`"use cache"` で自動制御)