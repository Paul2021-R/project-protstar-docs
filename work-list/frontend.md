## 2025-12-09 프론트엔드 개발 사항

### 개선 사항
- [x] PC, 태블릿, 모바일 기준이 1024 / 768 / 640 (Tailwind CSS 기준)로 제대로 동작하도록 검토 후 현재 변환 지점 제대로 수정할 것
- [x] `sticky top-0 z-50 w-full border-b bg-background/95 backdrop-blur supports-[backdrop-filter]:bg-background/60` 가 안의 `container mx-auto flex h-14 items-center` 가 매우 길게 설정되어서, Login, Get Started 버튼이 줄 아래로 내려가서, 한줄로 표현되지 않음. 
- [x] dashboard 에서, 사이드바를 펼쳤을 때, 펼친 사이드바 위로 메인 `flex flex-1 flex-col gap-4 p-4 pt-0` 항목이 펼처진 사이드바 아래로 내려가는 것 까진 괜찮음. 그러나 `flex flex-1 flex-col gap-4 p-4 pt-0`의 시작지점이 사이드바 접혔을 때 우측 기준으로 유지되어야 함. 예를 들어 사이드바 접힐 떼 x = 0 에서 시작, 사이드 우측이 x = 150 이라고 하면, x 150 지점에서 부터 `flex flex-1 flex-col gap-4 p-4 pt-0`가 시작되며, 사이드바가 펼쳐지더라도, `flex flex-1 flex-col gap-4 p-4 pt-0`는 x 150 지점부터 시작해야함. 현재 실제 사이즈 확인하고 제대로 수정할 것
- [x] dashboard 태블릿, 모바일 뷰에서 봤을 때 글자가 너무 작아서 모바일에 맞춰서 터치 가능한 레이아웃 및 글자 사이즈로 수정할 것
- [x] 접기 / 펼치기 버튼부터 사이드바 아래 레이아웃이 너무 높음. 좀더 아래로 내리고, 접기/펼치기 아이콘 옆에 fold/unfold 이름 추가할 것 

### 개발 사항 : 개선사항 작업 후 진행 예정 
- [] 