# kimchi-premium-monitor

USDT 김치프리미엄(역프리미엄) 실시간 모니터링 + 텔레그램 알림

**GitHub Pages 라이브 데모**: `https://<your-username>.github.io/kimchi-premium-monitor/`

---

## 주요 기능

- Bithumb(KRW) vs HashKey(HKD→KRW 환산) 실시간 프리미엄 계산
- HashKey CORS 차단 시 CoinGecko 자동 fallback
- 30초마다 자동 갱신, 임계값 초과 시 텔레그램 알림 (10분 쿨다운)
- 다크모드, 모바일 반응형, 단일 HTML 파일

---

## GitHub Pages 배포 방법

### 1. 저장소 생성 및 파일 업로드

```bash
# 새 저장소 생성 후 클론
git clone https://github.com/<username>/kimchi-premium-monitor.git
cd kimchi-premium-monitor

# index.html 복사 후 커밋
git add index.html
git commit -m "Initial commit"
git push origin main
```

### 2. GitHub Pages 활성화

1. 저장소 → **Settings** → 좌측 **Pages**
2. **Source**: `Deploy from a branch`
3. **Branch**: `main` / `/ (root)` 선택 후 **Save**
4. 약 1분 후 `https://<username>.github.io/kimchi-premium-monitor/` 접속 확인

---

## 텔레그램 알림 설정

### 봇 토큰 확인

`index.html` 상단 CONFIG 섹션에 이미 토큰이 입력되어 있습니다.  
변경이 필요하면 `TELEGRAM_BOT_TOKEN` 값을 교체하세요.

```js
const CONFIG = {
  TELEGRAM_BOT_TOKEN: 'YOUR_BOT_TOKEN',
  TELEGRAM_CHAT_ID:   '',   // ← 아래 방법으로 확인 후 입력
  ...
};
```

### CHAT_ID 확인 방법

1. 텔레그램에서 **본인의 봇**에게 아무 메시지나 전송
2. 브라우저에서 아래 URL 접속:
   ```
   https://api.telegram.org/bot<BOT_TOKEN>/getUpdates
   ```
3. 응답 JSON에서 `result[0].message.chat.id` 값 복사
4. `index.html` CONFIG의 `TELEGRAM_CHAT_ID`에 붙여넣기 (또는 UI 입력란에 입력)

> 채널에 알림을 보내려면 봇을 채널 관리자로 추가하고 채널 ID(`-100xxxxxxxx` 형식)를 사용하세요.

---

## 사용 API

| 데이터 | API | 인증 |
|--------|-----|------|
| USDT/KRW | Bithumb Public | 불필요 |
| USDT/HKD | HashKey Exchange | 불필요 |
| USDT/HKD (fallback) | CoinGecko | 불필요 |
| HKD/KRW 환율 | exchangerate-api.com | 불필요 |

---

## 로컬 실행

별도 서버 불필요 — 브라우저에서 `index.html`을 직접 열면 됩니다.  
단, 일부 브라우저의 CORS 정책으로 인해 로컬 파일(`file://`) 실행 시 API 요청이 차단될 수 있습니다.  
이 경우 간단한 로컬 서버를 사용하세요:

```bash
# Python
python -m http.server 8080

# Node.js (npx)
npx serve .
```

접속: `http://localhost:8080`

---

## 프리미엄 계산식

```
HashKey_KRW  = HashKey_HKD × HKD/KRW 환율
프리미엄(%)  = (Bithumb_KRW - HashKey_KRW) / HashKey_KRW × 100

양수 → 김프: 국내 가격 > 해외  (해외 매수 → 국내 매도 유리)
음수 → 역프: 국내 가격 < 해외  (국내 매수 → 해외 매도 유리)
```
