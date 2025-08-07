
# 📄 システム仕様書（ドラフト）

## 1. システム概要
自治会・町内会の掲示板を地図上にマッピングし、スマホで撮影した写真をユーザーが投稿できるWebアプリ。掲示板の情報共有を簡単にし、地域の情報をオンラインで閲覧可能にする。

---

## 2. 技術スタック
- **フロントエンド**: Next.js (App Router), TypeScript, React
- **地図表示**: Leaflet + OpenStreetMap
- **バックエンド**: Next.js API Routes
- **データベース**: Supabase (PostgreSQL)
- **ストレージ**: Supabase Storage
- **認証**: Supabase Auth (メール＋パスワード、Google、LINE)
- **デプロイ**: Vercel

---

## 3. 主な機能
- **掲示板投稿**
  - スマホカメラ起動、または端末内の写真から投稿
  - 投稿写真はEXIF位置情報必須（なければ拒否）
  - 画像クリックで拡大表示（文字を読める）
- **掲示板マップ**
  - 地図上に掲示板アイコン表示
  - 最新投稿が優先表示、過去写真の履歴も閲覧可能
- **ユーザー管理**
  - 未ログイン：閲覧のみ
  - ログインユーザー：閲覧＋投稿
  - 投稿制限：同一掲示板に3日以内再投稿不可
- **ポイント＆ランク**
  - 投稿1回＝1ポイント
  - ランク：5pt、20pt、100pt、1000ptで昇格
  - ランク表示：アイコンのフレーム色
- **コメント・いいね**
  - 掲示板詳細でコメント、いいね可能（ログイン必須）

---

## 4. ユーザーフロー
```
[トップページ] → [地図表示] → 
  ├─ [掲示板クリック] → [モーダル：詳細＋過去写真＋投稿ボタン]
  │                                ├─ [投稿：写真アップロード]
  │                                ├─ [コメント・いいね]
  └─ [ログイン／登録]
```

---

## 5. データベース設計（ER図）

### **テーブル**
- **users**
  - id (UUID)
  - name (string)
  - email (string)
  - rank (int)
  - points (int)
  - avatar_url (string)
  - provider (enum: email, google, line)
  - created_at (timestamp)
- **boards**（掲示板）
  - id (UUID)
  - latitude (float8)
  - longitude (float8)
  - latest_photo_id (UUID)
  - created_at (timestamp)
- **photos**
  - id (UUID)
  - board_id (UUID)
  - user_id (UUID)
  - image_url (string)
  - exif_location (geometry)
  - created_at (timestamp)
- **comments**
  - id (UUID)
  - board_id (UUID)
  - user_id (UUID)
  - content (text)
  - created_at (timestamp)
- **likes**
  - id (UUID)
  - board_id (UUID)
  - user_id (UUID)
  - created_at (timestamp)

---

## 6. APIエンドポイント仕様（Next.js App Router準拠）

### **1. 認証**
- `POST /api/auth/signup`
- `POST /api/auth/login`
- Supabase Authを利用するため、クライアントSDKで実装

### **2. 掲示板**
- `GET /api/boards`  
  - クエリ：地図範囲（lat, lng, zoom）
  - レスポンス：掲示板一覧＋最新写真URL
- `POST /api/boards`  
  - リクエスト：画像ファイル（multipart）、EXIF情報
  - 処理：Supabase Storageアップロード、DB登録

### **3. 写真**
- `GET /api/photos/:board_id`  
  - 掲示板の写真履歴
- `POST /api/photos`  
  - 同一掲示板3日以内投稿不可のバリデーション

### **4. コメント**
- `GET /api/comments/:board_id`
- `POST /api/comments`

### **5. いいね**
- `POST /api/likes`
- `DELETE /api/likes`

---

## 7. UI設計（概要）
- **トップページ**
  - 地図表示（Leaflet）
  - 掲示板アイコン＋最新写真サムネイル
- **掲示板詳細モーダル**
  - 最新写真
  - 過去写真（スライダー）
  - コメント欄
  - いいねボタン
  - 投稿ボタン
- **投稿モーダル**
  - カメラ起動 or 画像選択
  - 投稿ボタン
  - エラーメッセージ（位置情報必須）

---

## 8. セキュリティ・制限
- 投稿画像：最大10MB、JPEG/PNGのみ
- 認証必須操作：投稿、コメント、いいね
- 画像URL署名付き（Supabase Signed URL）

---

## 9. 拡張計画
- 将来対応：
  - EXIFなし投稿 → 地図から位置選択
  - 通知機能（コメント、いいね）
  - 管理画面（不適切投稿削除）
  - 投稿報告機能

---

### ✅ 次のステップ
- [ ] **UIワイヤーフレーム作成（Figma）**
- [ ] **DBスキーマのSQL定義**
- [ ] **API詳細仕様（リクエスト例・レスポンス例）**
- [ ] **Next.js App Router構成図**
