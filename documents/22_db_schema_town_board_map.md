
# 📄 DBスキーマ仕様（Supabase / PostgreSQL）

## ✅ 基本方針
- Supabase Authを利用してユーザー管理
- アプリ独自のユーザー情報はprofilesテーブルで管理
- 掲示板（boards）、写真（photos）、コメント（comments）、いいね（likes）
- EXIF情報はphotosに保存
- 拡張性を考慮（タグ、報告機能追加可）

---

## ✅ ER図（概念）
```
users (Supabase Auth)
  └─ profiles
boards
  ├─ photos
  ├─ comments
  └─ likes
```

---

## ✅ テーブル定義

### 1. profiles（ユーザー拡張情報）
```sql
create table profiles (
  id uuid primary key references auth.users on delete cascade,
  name text not null,
  avatar_url text,
  points int default 0,
  rank int default 0,
  icon_type text default 'default', -- default, upload, animal
  created_at timestamp with time zone default now()
);
```

---

### 2. boards（掲示板）
```sql
create table boards (
  id uuid primary key default gen_random_uuid(),
  latitude float8 not null,
  longitude float8 not null,
  latest_photo_id uuid references photos(id),
  created_at timestamp with time zone default now()
);
```

---

### 3. photos（掲示板写真）
```sql
create table photos (
  id uuid primary key default gen_random_uuid(),
  board_id uuid not null references boards(id) on delete cascade,
  user_id uuid not null references profiles(id) on delete cascade,
  image_url text not null,
  exif_lat float8 not null,
  exif_lng float8 not null,
  created_at timestamp with time zone default now()
);
```
**制約**
- 同一board_id + user_idで3日以内に投稿不可 → Supabase RLS + Functionで制御

---

### 4. comments（コメント）
```sql
create table comments (
  id uuid primary key default gen_random_uuid(),
  board_id uuid not null references boards(id) on delete cascade,
  user_id uuid not null references profiles(id) on delete cascade,
  content text not null,
  created_at timestamp with time zone default now()
);
```

---

### 5. likes（いいね）
```sql
create table likes (
  id uuid primary key default gen_random_uuid(),
  board_id uuid not null references boards(id) on delete cascade,
  user_id uuid not null references profiles(id) on delete cascade,
  created_at timestamp with time zone default now(),
  unique(board_id, user_id)
);
```

---

## ✅ インデックス
```sql
create index idx_boards_location on boards using gist (ll_to_earth(latitude, longitude));
```
→ 地図範囲検索を高速化するためにPostGISやearthdistance拡張を利用

---

## ✅ RLS（Row Level Security）
- profiles → auth.uid() = id
- photos → 認証必須、3日以内同じ掲示板投稿不可
- comments、likes → 認証必須

---

## ✅ 将来的な追加
- reports（不適切投稿の通報）
- tags（掲示板分類）
- notifications（コメントやいいね通知）
