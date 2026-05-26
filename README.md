# 프로젝트 진행 기록 앱

과학 동아리 활동 등 학생 프로젝트 진행 상황을 단계별로 기록하고, 교사가 전체 현황을 조회하는 웹앱입니다.

## 시작 전 준비: Supabase 설정

### 1단계 — 테이블 생성 SQL

Supabase 대시보드 → **SQL Editor** 에 아래 전체를 붙여넣고 실행하세요.

```sql
-- project_records 테이블 생성
CREATE TABLE IF NOT EXISTS project_records (
  id           uuid        DEFAULT gen_random_uuid() PRIMARY KEY,
  created_at   timestamptz DEFAULT timezone('utc', now()) NOT NULL,
  project_name text        NOT NULL,
  school_level text        NOT NULL,
  student_name text        NOT NULL,
  stage        text        NOT NULL,
  today_work   text        NOT NULL,
  good_points  text        NOT NULL,
  difficulty   text,
  next_plan    text        NOT NULL
);

-- RLS 활성화
ALTER TABLE project_records ENABLE ROW LEVEL SECURITY;

-- anon 사용자 — 기록 제출 허용
CREATE POLICY "anon_insert" ON project_records
  FOR INSERT TO anon WITH CHECK (true);

-- anon 사용자 — 기록 조회 허용
CREATE POLICY "anon_select" ON project_records
  FOR SELECT TO anon USING (true);
```

### 2단계 — config.js 작성

`config.js` 파일을 열어 Supabase Project URL과 anon key를 입력하세요.

```js
const SUPABASE_URL     = 'https://xxxxxxxxxxxx.supabase.co';
const SUPABASE_ANON_KEY = 'eyJhbGc...';
```

> **주의**: `config.js`는 `.gitignore`에 포함되어 있어 GitHub에 올라가지 않습니다.

---

## 파일 구조

```
aiedu-dev01/
├── index.html   ← 앱 전체 (단일 파일)
├── config.js    ← Supabase 키 (git 제외)
├── .gitignore
└── README.md
```

## GitHub Pages 배포

1. GitHub에 `aiedu-dev01` 레포 생성 (public)
2. `index.html`, `.gitignore`, `README.md` push (config.js 는 push 하지 않음)
3. Settings → Pages → Branch: `main` / `/ (root)` → Save
4. 배포 URL: `https://<username>.github.io/aiedu-dev01/`

## 화면 구성

| 화면 | 대상 | 기능 |
|------|------|------|
| 설정 화면 | 교사 | 프로젝트명 입력, 학교급 선택 (초등·중등·고등) |
| 학생 입력 | 학생 | 이름, 진행 단계, 오늘 활동, 잘 된 점, 어려운 점, 다음 계획 |
| 전체 조회 | 교사 | 전체 기록 열람, 단계·이름 필터, 통계 |
