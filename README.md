한강 식물 AI — 교란종 판별 웹앱
한강유역 교란종을 카메라로 실시간 판별하는 모바일 웹 서비스입니다.  
Roboflow Workflow REST API를 사용하며, 별도 설치 없이 `index.html` 한 파일로 동작합니다.
---
빠른 시작
1. API 키 설정
`index.html` 상단 `ROBOFLOW_CONFIG` 블록을 수정합니다.
```js
const ROBOFLOW_CONFIG = {
  api_key:  "rf_XXXXXXXXXXXXXXXX",   // ← 여기에 입력
  endpoint: "https://serverless.roboflow.com/-d1vla/workflows/my-first-project-pj3zl",
  ...
};
```
API 키는 app.roboflow.com/settings/api 에서 확인할 수 있습니다.
> ⚠️ API 키를 코드에 직접 입력하면 GitHub에 공개될 수 있습니다.  
> 공개 레포지토리라면 Netlify/Vercel의 환경 변수 + 서버리스 함수로 키를 숨기는 것을 권장합니다.
2. 교란종 클래스 목록 업데이트
Roboflow 모델 학습 후 실제 클래스명으로 `INVASIVE_CLASSES` 배열을 수정합니다.
```js
const INVASIVE_CLASSES = [
  'gasibak', '가시박',
  'hwansam', '환삼덩굴',
  // ... 모델 클래스명 추가
];
```
3. 배포
카메라 API는 HTTPS 필수입니다. 아래 중 하나를 선택하세요.
플랫폼	방법
GitHub Pages	Settings → Pages → main branch 선택
Netlify	`index.html` 드래그앤드롭
Vercel	`vercel --prod`
배포 URL 예시: `https://lae04.github.io/hangang-plant-ai/`
---
Roboflow Workflow 연동
엔드포인트
```
POST https://serverless.roboflow.com/-d1vla/workflows/my-first-project-pj3zl
```
요청 형식
```json
{
  "inputs": {
    "image": {
      "type": "base64",
      "value": "<JPEG base64 string>"
    }
  }
}
```
인증
```
Authorization: Bearer <api_key>
```
쿼리 파라미터나 요청 바디에 키를 넣지 않습니다 (inference v1.5+ header-based auth).
응답 파싱
응답은 배열이며, 이미지 1장당 항목 1개입니다.
```json
[
  {
    "predictions": {
      "predictions": [
        { "class": "gasibak", "confidence": 0.94, "x": 320, "y": 240, "width": 100, "height": 80 }
      ]
    }
  }
]
```
`parseWorkflowResult()` 함수가 응답 키를 방어적으로 탐색하므로, 워크플로우 출력 키가 바뀌어도 자동으로 대응합니다.
---
주요 함수
함수	설명
`runWorkflow(base64)`	워크플로우 호출 (재시도 포함)
`callWorkflowOnce(base64)`	단일 HTTP 호출 (타임아웃 15초)
`parseWorkflowResult(raw)`	응답 → 앱 내부 결과 객체 변환
`canvasToBase64(canvas)`	Canvas → JPEG base64 변환
`initCamera()`	후면 카메라 시작
`triggerCapture()`	촬영 → 추론 → 결과 표시
---
데모 모드
API 키를 설정하지 않으면 데모 모드로 동작합니다.  
`DEMO_RESULTS` 배열의 샘플 데이터를 순환하여 UI를 테스트할 수 있습니다.
---
파일 구조
```
hangang-plant-ai/
└── index.html   # 앱 전체 (HTML + CSS + JS 단일 파일)
```
---
기술 스택
Vanilla HTML / CSS / JS (프레임워크 없음)
Roboflow Serverless Workflow REST API
Web Camera API (`getUserMedia`, 후면 카메라)
Tabler Icons, Noto Sans KR
