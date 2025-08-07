
# 📄 Next.js ディレクトリ構成 & ページ設計

## ✅ 技術前提
- Next.js 15 (App Router)
- TypeScript
- Leaflet（OpenStreetMap）
- Supabase SDK（認証・DB・ストレージ）
- UIライブラリ: Material UI（@mui/material）
  ※ @emotion/react と @emotion/styled も必要
- 画像アップロード: Supabase Storage
- 認証ページ: Supabase Auth + Middleware

---

## ✅ ディレクトリ構成（App Router）
```
/app
  ├─ layout.tsx
  ├─ page.tsx
  ├─ map/
  │    └─ page.tsx
  ├─ boards/
  │    ├─ [id]/page.tsx
  │    └─ upload/page.tsx
  ├─ auth/
  │    ├─ login/page.tsx
  │    └─ signup/page.tsx
  ├─ profile/page.tsx
  ├─ api/
  │    ├─ boards/route.ts
  │    ├─ photos/route.ts
  │    ├─ comments/route.ts
  │    ├─ likes/route.ts
  │    └─ auth/route.ts
/components
  ├─ MapView.tsx
  ├─ BoardMarker.tsx
  ├─ PhotoSlider.tsx
  ├─ CommentList.tsx
  ├─ LikeButton.tsx
/lib
  ├─ supabaseClient.ts
  ├─ apiClient.ts
  ├─ auth.ts
/styles
  ├─ globals.css
  └─ theme.ts
```

---

## ✅ ページ仕様

### 1. トップページ `/`
- 地図を全画面表示
- 掲示板ピン表示
- ヘッダーにログイン / プロフィールボタン

### 2. 掲示板詳細 `/boards/[id]`
- モーダルで詳細表示
- 最新写真 + 過去写真スライダー
- コメント & いいね
- 投稿ボタン

### 3. 写真投稿 `/boards/upload`
- カメラ起動 or ファイル選択
- EXIF座標必須

### 4. 認証 `/auth/login`, `/auth/signup`
- Supabase Auth
- メール + パスワード、Google、LINE

### 5. プロフィール `/profile`
- 名前、ランク、ポイント表示
- アイコン変更可能（初期文字、アップロード、動物アイコン）

---

## ✅ API構成
- `/api/boards` → GET, POST
- `/api/photos` → POST
- `/api/comments` → GET, POST
- `/api/likes` → POST, DELETE

---

## ✅ Middleware
- 認証必須ページでSupabase JWT検証
    - /boards/upload、/profile、/api/photosなどは認証必須
