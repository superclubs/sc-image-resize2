# Image Resize Via Lambda@Edge

이 프로젝트는 AWS Lambda@Edge 환경에서 이미지를 리사이즈하기 위한 Node.js 애플리케이션입니다. `sharp` 라이브러리를 사용하여 이미지를 처리합니다.

## Requirements

- `Node.js 20.x`
- `npm`
- `esbuild`

## 설치

프로젝트를 클론한 후 필요한 패키지를 설치합니다.

```bash
git clone <your-repository-url>
cd <your-repository-directory>
npm install
```

## 빌드

ESBuild를 사용하여 프로젝트를 번들링합니다. \
`sharp` 라이브러리는 바이너리 파일 때문에 함께 번들링하지 않고, 빌드 후에 설치해야 합니다. \
나머지 라이브러리는 프로젝트 코드와 함께 번들링되며, 용량을 줄이기 위해 minification을 진행합니다.

```bash
npm run build
```

위의 명령어를 실행하면 `build.js`가 실행되며, 아래의 동작들이 수행됩니다.

1. 설정에 따라 `dist` 폴더에 프로젝트를 빌드합니다.
2. `dist` 폴더에 `package.json`을 생성하고, 사용중인 버전의 `sharp`가 추가됩니다.
3. `--platform=linux --arch=x64`옵션을 사용해 `sharp`를 설치합니다.
4. 설치된 `sharp`모듈 내의 불필요한 파일을 제거합니다.

## 테스트

Docker를 사용하여 Lambda 환경과 유사한 환경에서 테스트를 실행합니다. \
코드가 번들링되기 전에 테스트를 실행합니다. \

테스트는 `Github Actions workflows`를 통한 배포 과정에 포함되어 있으며, \
테스트 실패 시 배포는 중단됩니다. \
테스트 명령어는 아래와 같습니다.

```bash
npm run test
```

## 배포

이 프로젝트는 `GitHub Actions`를 사용하여 자동으로 배포됩니다.\
배포 설정된 브랜치에 푸시될 때마다 각 스테이지에 맞게 워크플로우가 실행됩니다.\
워크플로우는 다음과 같은 작업을 수행합니다.

1. AWS 자격 증명을 설정합니다.
2. Node.js 환경을 설정합니다.
3. 의존성을 설치합니다.
4. 코드를 테스트합니다.
5. 코드를 빌드합니다.
6. Lambda 함수를 업데이트하고 버전을 발행합니다.
7. CloudFront 배포를 업데이트하여 Lambda@Edge 함수를 배포합니다.

워크플로우 파일들은 `.github/workflows/`에 위치해 있습니다.
