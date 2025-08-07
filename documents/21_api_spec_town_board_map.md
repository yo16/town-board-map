
# 📄 API仕様詳細（Next.js App Router準拠）

## **共通事項**
- **認証**: Supabase Auth JWTトークン（ヘッダー`Authorization: Bearer <token>`）
- **レスポンス形式**: JSON
- **ステータスコード**
  - 200: 正常
  - 400: リクエストエラー
  - 401: 認証エラー
  - 500: サーバーエラー

---

## **1. 認証**

### **1-1. サインアップ**
`POST /api/auth/signup`

#### **リクエスト**
```json
{
  "email": "user@example.com",
  "password": "password123",
  "name": "太郎"
}
```

#### **レスポンス**
```json
{
  "status": "success",
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "太郎"
  }
}
```

---

### **1-2. ログイン**
`POST /api/auth/login`

#### **リクエスト**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

#### **レスポンス**
```json
{
  "status": "success",
  "access_token": "jwt_token",
  "user": {
    "id": "uuid",
    "email": "user@example.com"
  }
}
```

---

## **2. 掲示板**

### **2-1. 掲示板一覧取得**
`GET /api/boards?lat=35.68&lng=139.76&zoom=14`

#### **レスポンス**
```json
{
  "boards": [
    {
      "id": "uuid",
      "latitude": 35.681236,
      "longitude": 139.767125,
      "latest_photo_url": "https://storage.supabase.io/photos/board123.jpg",
      "updated_at": "2025-07-25T10:00:00Z"
    }
  ]
}
```

---

### **2-2. 掲示板新規登録（初投稿時）**
`POST /api/boards`

#### **リクエスト（multipart/form-data）**
```
image: file.jpg
```

※ サーバー側でEXIFから座標を抽出し、新しい掲示板として登録。

#### **レスポンス**
```json
{
  "status": "success",
  "board": {
    "id": "uuid",
    "latitude": 35.68,
    "longitude": 139.76,
    "latest_photo_id": "uuid"
  }
}
```

---

## **3. 写真（掲示板への投稿）**

### **3-1. 写真アップロード**
`POST /api/photos`

#### **リクエスト（multipart/form-data）**
```
board_id: uuid
image: file.jpg
```

#### **レスポンス**
```json
{
  "status": "success",
  "photo": {
    "id": "uuid",
    "board_id": "uuid",
    "image_url": "https://storage.supabase.io/photos/uuid.jpg",
    "created_at": "2025-07-25T10:05:00Z"
  }
}
```

---

## **4. コメント**

### **4-1. コメント一覧取得**
`GET /api/comments/:board_id`

#### **レスポンス**
```json
{
  "comments": [
    {
      "id": "uuid",
      "user_name": "太郎",
      "content": "掲示板情報ありがとう！",
      "created_at": "2025-07-25T11:00:00Z"
    }
  ]
}
```

---

### **4-2. コメント投稿**
`POST /api/comments`

#### **リクエスト**
```json
{
  "board_id": "uuid",
  "content": "最新情報ありがとうございます！"
}
```

#### **レスポンス**
```json
{
  "status": "success",
  "comment": {
    "id": "uuid",
    "content": "最新情報ありがとうございます！"
  }
}
```

---

## **5. いいね**

### **5-1. いいね追加**
`POST /api/likes`

#### **リクエスト**
```json
{
  "board_id": "uuid"
}
```

#### **レスポンス**
```json
{
  "status": "success"
}
```

---

### **5-2. いいね取り消し**
`DELETE /api/likes`

#### **リクエスト**
```json
{
  "board_id": "uuid"
}
```

#### **レスポンス**
```json
{
  "status": "success"
}
```
