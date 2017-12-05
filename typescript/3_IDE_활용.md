# Typescript

## 3. IDE 활용

#### Visual Studio code
- Compiler
  - VS Code에 컴파일러가 내장되어 있음
  - 내장된 컴파일러 버전은 VS Code 가 업데이트 되면서 올라감
  - 내장, 외장 컴파일러를 선택 할 수 있음
- tslint
  - npm install typescript tslint
  - tslint --init
  - tslint 플러그인 설치
    - ext install tslint
    - https://marketplace.visualstudio.com/items?itemName=eg2.tslint
    - https://palantir.github.io/tslint/

#### Intellij IDEA
- Compiler
  - Node interpreter 를 지정해야 함
  - 타입스크립트 컴파일러를 지정해야 함
  - Use TypeScript Service
    - 컴파일이 아니라 컴파일 오류 체크
    - 코드 완성
      - https://www.jetbrains.com/help/idea/2017.1/auto-completing-code.html
  - Enable TypeScript Compiler 를 설정해서 컴파일을 할 수 있음
- tslint
  - Enable 체크를 통해 tslint 가 자동으로 오류 체크
  - Node interpreter 를 지정해야 함
  - TSLint 모듈을 지정해야 함
  - 설정 파일인 tslint.json 파일을 지정하거나, 자동으로 프로젝트 루트에서 찾도록 할 수 있음


**PREV > [2. 개발환경 구축 및 컴파일러 사용](https://github.com/mirrors89/study/tree/master/typescript/2_개발환경_구축_및_컴파일러_사용.md)**

**NEXT > [4. Compiler Options](https://github.com/mirrors89/study/tree/master/typescript/4_Compiler_Options.md)**
