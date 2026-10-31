# Yoon Padlet

Next.js, Supabase, Vercel 기반의 단일 개인 메모 보드입니다. 섹션·컬럼·카테고리 없이 메모 카드만 자유롭게 쌓입니다.

## 로컬 실행

1. Supabase SQL Editor에서 `schema.sql`을 실행합니다.
2. 프로젝트 루트의 `.env.local`에 Supabase URL, anon key, service role key, 관리자 비밀번호, 세션 비밀값을 입력합니다.
3. `ADMIN_PASSWORD`에 관리자 로그인에서 사용할 비밀번호를 입력합니다. 예시 파일의 기본값은 실제 서비스에 사용하지 마세요.
4. `ADMIN_SESSION_SECRET`에는 관리자 비밀번호와 다른 길고 무작위인 문자열을 넣습니다. 아래 명령으로 생성할 수 있습니다.

```bash
node -e "console.log(require('node:crypto').randomBytes(32).toString('hex'))"
```

5. 개발 서버를 실행합니다.

```bash
npm run dev
```

## Vercel 배포

GitHub 저장소를 Vercel에 연결한 뒤 Project Settings → Environment Variables에 `.env.local.example`에 안내된 다섯 환경변수를 등록하고 배포합니다. `SUPABASE_SERVICE_ROLE_KEY`, `ADMIN_PASSWORD`, `ADMIN_SESSION_SECRET`은 서버 전용으로 등록하고 절대 `NEXT_PUBLIC_` 접두사를 붙이지 마세요. `NEXT_PUBLIC_` 접두사가 붙은 값은 브라우저에 공개됩니다.

## 보안 구조

- 브라우저는 공개 URL/anon key로 메모 목록만 조회합니다.
- 메모 생성 시 4자리 비밀번호 해시는 서버 API에서 bcrypt로 만듭니다.
- 일반 삭제는 서버에서 해시를 비교한 뒤 service role로 수행합니다.
- 관리자 비밀번호는 서버 전용 환경 변수에서만 읽고, 관리자 인증과 삭제는 HttpOnly 세션 쿠키를 검증하는 서버 API에서만 수행합니다.
- `schema.sql`은 명시적 `GRANT`와 RLS 정책을 함께 적용하며, anon/authenticated 역할에는 메모의 비밀번호 해시를 읽을 권한을 주지 않습니다.
