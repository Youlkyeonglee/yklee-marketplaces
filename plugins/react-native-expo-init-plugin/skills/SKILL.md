---
name: mobile-setup
description: |
  React Native + NativeWind(TailwindCSS) + Expo 기반 모바일 앱 개발 환경 초기 세팅 가이드.
  사용자가 새로운 React Native 앱 프로젝트를 시작하거나, 모바일 앱 개발 환경을 처음 구축하거나,
  "앱 개발 시작", "expo 세팅", "RN 환경 설정", "모바일 앱 초기 설정", "NativeWind 설치"
  등의 키워드를 언급할 때 반드시 이 스킬을 사용한다.
  Android Studio 설치 여부를 확인하고, 미설치 시 가이드를 제공하며,
  설치된 경우 Expo 환경 세팅 → Hello World 예제 앱 생성 → README.md 작성까지 단계적으로 진행한다.
---

# 모바일 앱 초기 세팅 스킬

이 스킬은 React Native(Expo) + NativeWind(TailwindCSS) 조합으로 모바일 앱 개발 환경을 처음부터 세팅하는 과정을 안내한다.
각 단계마다 사용자의 확인을 받고 진행하는 것이 핵심이다. 강제로 설치하지 말고, 반드시 확인 후 진행한다.

---

## 기술 스택 개요

| 역할 | 기술 |
|------|------|
| 앱 프레임워크 | React Native |
| 스타일링 | NativeWind (TailwindCSS for React Native) |
| 개발/데모 환경 | Expo |
| Android 에뮬레이터 | Android Studio |

> **왜 이 조합인가?** Expo는 React Native 앱을 빠르게 개발·미리보기 할 수 있는 플랫폼이고,
> NativeWind는 TailwindCSS의 유틸리티 클래스를 React Native에서 사용할 수 있게 해준다.
> Android Studio는 실기기 없이 에뮬레이터로 앱을 테스트할 때 필요하다.

---

## 전체 플로우

```
1. Android Studio 설치 확인
   ├── 미설치 → 설치 가이드 안내 후 종료
   └── 설치됨 → 2단계 진행
2. 필수 개발 도구 확인 (Node.js, npm)
3. 사용자 확인 후 Expo 프로젝트 생성
4. NativeWind(TailwindCSS) 설치 및 설정
5. Hello World 버튼 예제 구현
6. README.md 작성
```

---

## 0단계: 프로젝트 폴더명 확인

가장 먼저 사용자에게 프로젝트 폴더명을 묻는다.

> "프로젝트 폴더 이름을 어떻게 할까요? (예: my-app, todo-app 등, 기본값: `my-app`)"

- 사용자가 이름을 입력하면 그 이름을 사용한다.
- 입력하지 않으면 `my-app`을 기본값으로 사용한다.
- 이후 모든 단계에서 이 폴더명을 일관되게 사용한다.

---

## 1단계: Android Studio 설치 확인

먼저 명령어로 설치 여부를 자동 감지한다. Bash 실행이 불가하거나 결과가 불명확하면 직접 사용자에게 묻는다.

```bash
# macOS/Linux
which adb 2>/dev/null || echo "NOT_FOUND"
echo $ANDROID_HOME
```

**Bash 실행 권한이 없거나 결과가 불확실할 경우 → 사용자에게 직접 확인:**

> "Android Studio가 설치되어 있나요? (설치됨 / 미설치)"

사용자의 답변에 따라 아래 두 분기 중 하나로 진행한다.

**미설치 확인 시 → 설치 가이드 제공 후 종료:**

사용자에게 아래 가이드를 안내하고, 설치 후 다시 실행하도록 안내한다. 더 이상 진행하지 않는다.

```
Android Studio 설치 가이드:

1. 공식 사이트에서 다운로드
   → https://developer.android.com/studio

2. 설치 완료 후 Android Studio 실행

3. SDK 설정:
   More Actions → SDK Manager → SDK Platforms 탭
   → Android 14.0 (API 34) 체크 후 Apply

4. 에뮬레이터 설정:
   More Actions → Virtual Device Manager
   → Create Device → Pixel 7 선택 → API 34 시스템 이미지 다운로드 → Finish

5. 환경변수 설정 (macOS/Linux ~/.zshrc 또는 ~/.bashrc에 추가):
   export ANDROID_HOME=$HOME/Library/Android/sdk
   export PATH=$PATH:$ANDROID_HOME/emulator
   export PATH=$PATH:$ANDROID_HOME/platform-tools

6. 터미널 재시작 후 adb --version으로 확인

설치 완료 후 이 스킬을 다시 실행하세요.
```

**설치 확인 시 → 2단계로 진행**

---

## 2단계: 필수 도구 확인

```bash
node --version   # v18 이상 권장
npm --version
```

Node.js가 없다면 사용자에게 설치 여부를 확인하고, 동의 시 안내한다:

```
Node.js 설치: https://nodejs.org (LTS 버전 권장)
또는 nvm 사용:
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
  nvm install --lts
```

**사용자에게 확인:** "Node.js 버전이 확인됐습니다. 프로젝트 생성을 진행할까요?"

---

## 3단계: Expo 프로젝트 생성

**사용자 확인 후 진행.** 0단계에서 확인한 폴더명을 그대로 사용한다.

```bash
npx create-expo-app@latest <0단계에서 확인한 폴더명> --template blank
cd <폴더명>
```

생성 후 구조를 간단히 설명한다:
- `app/` 또는 `App.js` → 메인 앱 파일
- `package.json` → 의존성 관리
- `app.json` → Expo 앱 설정

**사용자에게 확인:** "프로젝트가 생성됐습니다. NativeWind(TailwindCSS) 설치를 진행할까요?"

---

## 4단계: NativeWind 설치 및 설정

**사용자 확인 후 진행.**

```bash
# NativeWind와 TailwindCSS 설치
npm install nativewind tailwindcss

# TailwindCSS 설정 파일 생성
npx tailwindcss init
```

### tailwind.config.js 수정

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./App.{js,jsx,ts,tsx}",
    "./app/**/*.{js,jsx,ts,tsx}",
    "./components/**/*.{js,jsx,ts,tsx}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

### babel.config.js 수정

```js
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: ['nativewind/babel'],
  };
};
```

**사용자에게 확인:** "NativeWind 설정이 완료됐습니다. Hello World 예제를 구현할까요?"

---

## 5단계: Hello World 버튼 예제 구현

**사용자 확인 후 진행.**

이 예제의 핵심은 **버튼을 클릭했을 때 "Hello, World!" 텍스트가 나타나는 인터랙션**이다.
단순히 텍스트를 화면에 띄우는 것이 아니라, `onPress` 이벤트와 `useState`를 통한 상태 변화가 반드시 포함되어야 한다.

`App.js`(또는 `app/index.tsx`)를 다음 내용으로 교체한다:

```jsx
import { useState } from 'react';
import { View, Text, TouchableOpacity, Alert } from 'react-native';
import { styled } from 'nativewind';

const StyledView = styled(View);
const StyledText = styled(Text);
const StyledTouchable = styled(TouchableOpacity);

export default function App() {
  const [message, setMessage] = useState('');

  const handlePress = () => {
    setMessage('Hello, World!');
  };

  return (
    <StyledView className="flex-1 items-center justify-center bg-white">
      <StyledText className="text-2xl font-bold text-gray-800 mb-8">
        My First App
      </StyledText>

      <StyledTouchable
        className="bg-blue-500 px-6 py-3 rounded-lg active:bg-blue-700"
        onPress={handlePress}
      >
        <StyledText className="text-white text-lg font-semibold">
          클릭하세요
        </StyledText>
      </StyledTouchable>

      {message ? (
        <StyledText className="mt-6 text-xl text-green-600 font-medium">
          {message}
        </StyledText>
      ) : null}
    </StyledView>
  );
}
```

### 앱 실행

```bash
# Android 에뮬레이터 실행 (Android Studio에서 에뮬레이터 먼저 실행 필요)
npx expo start --android

# 또는 Expo Go 앱으로 QR 코드 스캔
npx expo start
```

**사용자에게 확인:** "예제 구현이 완료됐습니다. README.md를 작성할까요?"

---

## 6단계: README.md 작성

**사용자 확인 후 진행.** 프로젝트 루트에 반드시 `README.md`라는 이름으로 생성한다.
(`setup-guide.md`, `GUIDE.md` 등 다른 이름을 사용하지 않는다.)

```markdown
# <프로젝트명>

React Native + NativeWind(TailwindCSS) + Expo 기반 모바일 앱

## 기술 스택

| 기술 | 버전 | 역할 |
|------|------|------|
| React Native | (package.json 확인) | 앱 프레임워크 |
| Expo | (package.json 확인) | 개발/빌드 환경 |
| NativeWind | (package.json 확인) | TailwindCSS for RN |
| TailwindCSS | (package.json 확인) | 유틸리티 CSS |

## 설치 라이브러리

\`\`\`bash
npm install nativewind tailwindcss
\`\`\`

## 실행 방법

### 사전 요구사항
- Node.js v18 이상
- Android Studio (에뮬레이터용)
- Expo CLI

### 개발 서버 시작

\`\`\`bash
npx expo start
\`\`\`

### Android 에뮬레이터 실행

1. Android Studio에서 Virtual Device Manager 열기
2. 에뮬레이터 실행 (▶ 버튼)
3. 터미널에서:
\`\`\`bash
npx expo start --android
\`\`\`

### 실기기 테스트 (Expo Go)

1. 스마트폰에 [Expo Go](https://expo.dev/client) 앱 설치
2. `npx expo start` 실행 후 QR 코드 스캔

## 프로젝트 구조

\`\`\`
<프로젝트명>/
├── App.js              # 메인 앱 (Hello World 예제 포함)
├── app.json            # Expo 앱 설정
├── tailwind.config.js  # TailwindCSS 설정
├── babel.config.js     # Babel 설정 (NativeWind 플러그인)
└── package.json        # 의존성 목록
\`\`\`
```

실제 버전 정보는 `package.json`을 읽어서 채워넣는다.

---

## 완료 메시지

모든 단계가 완료되면 사용자에게 다음을 안내한다:

```
✅ 모바일 앱 개발 환경 세팅 완료!

- 프로젝트: <프로젝트명>/
- 실행: npx expo start --android
- 예제: 버튼 클릭 시 "Hello, World!" 메시지 표시
- 문서: README.md 참고

다음 개발 시작 포인트: App.js를 수정해서 원하는 화면을 만들어보세요.
```
