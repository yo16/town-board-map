# ✅ システム構築計画（まちの掲示板）

## 1. 準備フェーズ
- [ ] Node.js 20.x、npmまたはpnpmをセットアップ
- [ ] Next.js 15 (App Router) プロジェクト作成（`create-next-app`）
- [ ] TypeScript・ESLint・Prettier導入
- [ ] Material UI + Emotion導入（`@mui/material @emotion/react @emotion/styled`）
- [ ] Leaflet + React-Leaflet導入
- [ ] Supabaseプロジェクト作成（DB、Storage、Auth設定）
- [ ] `.env.local`でSupabaseキー設定

## 2. データベース構築
- [ ] SupabaseでDBスキーマ作成（profiles, boards, photos, comments, likes）
- [ ] PostGIS / earthdistance拡張を有効化
- [ ] RLSポリシー設定（認証必須、3日以内再投稿不可）
- [ ] Supabase Storageに`photos`バケット作成

## 3. バックエンド(API)実装
- [ ] `/api/auth/signup`・`/api/auth/login`（Supabase Auth利用）
- [ ] `/api/boards` → GET（地図範囲取得）、POST（EXIF解析＋新規登録）
- [ ] `/api/photos` → POST（画像アップロード＋3日以内バリデーション）
- [ ] `/api/comments` → GET, POST
- [ ] `/api/likes` → POST, DELETE
- [ ] `lib/apiClient.ts`で共通APIクライアント作成
- [ ] Middlewareで認証保護（upload・profile・API一部）

## 4. フロントエンド実装
- [ ] 共通レイアウト（`app/layout.tsx`）＋Material UIテーマ
- [ ] トップページ `/`：地図表示（Leaflet）、掲示板ピン
- [ ] 掲示板詳細モーダル：写真スライダー、コメント、いいね、投稿ボタン
- [ ] 写真投稿モーダル `/boards/upload`：カメラ起動、EXIF必須チェック
- [ ] 認証ページ `/auth/login`, `/auth/signup`
- [ ] プロフィール `/profile`：ポイント・ランク表示、アイコン変更

## 5. テスト・デバッグ
- [ ] APIレスポンス・UI表示の単体テスト
- [ ] スマホ・PC動作確認
- [ ] 画像アップロード制限（10MB）、EXIFなし投稿エラーチェック
- [ ] 投稿制限（3日以内同一掲示板不可）

## 6. デプロイ
- [ ] VercelにNext.jsをデプロイ
- [ ] Supabase環境変数設定
- [ ] HTTPS対応、ドメイン設定

## 7. 運用・改善
- [ ] 利用ログ収集（Supabase Logs）
- [ ] 不適切投稿の報告機能追加
- [ ] 通知機能（コメント・いいね）
- [ ] UI改善（Figma検討）
