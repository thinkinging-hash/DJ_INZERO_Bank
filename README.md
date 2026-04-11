# 대생인재로 · 대전생활과학고등학교 학생 관리 대시보드

> **GitHub Pages** (프론트엔드) + **Google Apps Script** (API) + **Google Sheets** (DB) 구조

---

## 📁 파일 구조

```
/
├── index.html          ← 로그인 페이지
├── dashboard.html      ← 메인 대시보드
├── README.md
└── .github/
    └── workflows/
        └── deploy.yml  ← GitHub Pages 자동 배포
```

---

## 🚀 전체 설정 순서

### 1단계 — Google Spreadsheet 만들기

1. [Google Sheets](https://sheets.google.com) 에서 **새 스프레드시트** 생성
2. 시트 이름을 아래처럼 설정:

| 시트명 | 내용 |
|---|---|
| `학생통합DB` | 기존 엑셀의 학생 데이터 붙여넣기 |
| `산출식목표` | 기존 엑셀의 산출식/목표치 시트 붙여넣기 |
| `사용자` | 아래 형식으로 계정 입력 |

**사용자 시트 형식 (1행은 헤더):**
```
아이디    비밀번호    역할
admin     admin1234  admin
teacher1  pass5678   viewer
```

> ⚠️ 역할은 반드시 `admin` 또는 `viewer` 중 하나

3. 주소창에서 **스프레드시트 ID** 복사 (URL의 `/d/` 와 `/edit` 사이 값)
   ```
   https://docs.google.com/spreadsheets/d/【여기가_ID】/edit
   ```

---

### 2단계 — Google Apps Script 배포

1. 스프레드시트에서 **확장 프로그램 → Apps Script** 클릭
2. `Code.gs` 파일 내용을 이 저장소의 `Code.gs` 내용으로 **전체 교체**
3. 저장 (Ctrl+S)
4. **배포 → 새 배포** 클릭
5. 설정:
   - 유형: **웹 앱**
   - 설명: `대생인재로 API v1`
   - 실행 계정: **나 (자신의 Google 계정)**
   - 액세스 권한: **모든 사용자 (익명 포함)**
6. **배포** 클릭 → 권한 허용
7. 생성된 **웹 앱 URL** 복사
   ```
   https://script.google.com/macros/s/AKfycb.../exec
   ```

> ⚠️ 코드 수정 후에는 반드시 **새 버전으로 재배포**해야 반영됩니다

---

### 3단계 — 프론트엔드에 URL 입력

`index.html` 파일에서 아래 줄을 찾아 GAS URL로 교체:

```javascript
// ★ 변경 전
const GAS_URL = 'YOUR_GAS_WEBAPP_URL_HERE';

// ★ 변경 후 (예시)
const GAS_URL = 'https://script.google.com/macros/s/AKfycb.../exec';
```

---

### 4단계 — GitHub 저장소 설정

1. GitHub에서 **새 저장소** 생성 (Public 권장, Private도 가능)
2. 로컬에서 push:
   ```bash
   git init
   git add .
   git commit -m "초기 배포"
   git branch -M main
   git remote add origin https://github.com/【계정명】/【저장소명】.git
   git push -u origin main
   ```

---

### 5단계 — GitHub Pages 활성화

1. 저장소 → **Settings → Pages**
2. Source: **GitHub Actions** 선택
3. 자동으로 `.github/workflows/deploy.yml` 이 실행되어 배포됨
4. 배포 완료 후 URL 확인:
   ```
   https://【계정명】.github.io/【저장소명】/
   ```

---

## 🔧 운영 방법

### 학생 데이터 추가/수정
→ Google Sheets의 `학생통합DB` 시트에서 직접 편집  
→ 저장 즉시 대시보드에 반영 (새로고침 시)

### 계정 추가
→ Google Sheets의 `사용자` 시트에 행 추가
```
새아이디    새비밀번호    viewer
```

### 졸업생 사후관리 저장
→ 대시보드에서 입력 후 저장 버튼 클릭  
→ GAS를 통해 Google Sheets에 자동 저장

### 코드 수정 후 재배포
```bash
git add .
git commit -m "수정 내용"
git push origin main
# → GitHub Actions가 자동으로 Pages 재배포
```

---

## ⚠️ 주의사항

| 항목 | 내용 |
|---|---|
| 비밀번호 | 현재 평문 저장. 실 운영 시 해싱 권장 |
| GAS 토큰 | 간단한 base64 토큰 사용. 민감 데이터면 PropertiesService 기반 세션으로 강화 권장 |
| CORS | GAS 웹앱은 `Content-Type: text/plain`으로 전송해야 CORS 오류 없음 |
| GAS 무료 한도 | 일 6분 실행, 하루 20,000 URL Fetch 등 — 소규모 학교 운영에 충분 |

---

## 📞 문의
대전생활과학고등학교 관리자에게 문의하세요.
