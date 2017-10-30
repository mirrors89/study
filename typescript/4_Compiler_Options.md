# Typescript

## 4. Compiler Options

##### http://json.schemastore.org/tsconfig

#### 최상위 프로퍼티
- compileOnSave
- extends
- compileOption
- file
- include
- exclude

#### compileOnSave
- true / false (default false)
- 최상단에 설정
- 특정 에디터에만 적용 가능
  - Visual Studio 2015 with TypeScript 1.8.4 이상
  - atom-typescript 플러그인

#### extends
- 파일 경로명 : string

### file, include, exclude
- 셋다 설정이 없으면, 전부다 컴파일
- file
  - 상대, 절대 경로의 리스트 배열
  - exclude 보다 우선
- include, exclude
  - glob 패턴
  - include
    -  exclude 보다 우선 순위가 낮음
    -  \* 같은걸 사용하면 , .ts / .tsx / .d.ts 만  include
  - exclude
    - 설정 안하면 4가지를 default로 제외함
      - node_modules, bower_components, jspm_packages, \<outDir\>
    - \<outDir\>은 항상 제외

### compileOption
- \@types
  - TypeScript 2.0 부터 사용 가능해진 내장 type definition 시스템
  - 설정하지 않으면 node_modules/@types 라는 모든 경로를 찾아서 사용
  - typeRoots 를 사용하면
    - 배열안에 들어있는 경로들 아래서만 가져옴
  - types 를 사용하면
    - 배열 안의 모듈 혹은 ./node_modules/@types/ 안의 모듈 이름에서 찾아옴
    - [] 빈 배열을 넣는다는건 이 시스템을 이용하지 않겠다는 의미
  - typeRoots와 types를 같이 사용하지 않음
- target
  - target 빌드의 결과물을 어떤 버전으로 할 것인지?
    - es3, es5, es2015, es2016, es2017, esnext
  - default는 es3
- lib
  - 기본 type definition 라이브러리르 어떤걸 사용 할 것인가?
  - lib를 지정 하지 않을때
    - target이 'es3' -> default로 lib.d.ts를 사용
    - target이 'es5' -> default로 dom, es5, scripthost를 사용
    - target이 'es6' -> default로 dom, es6, dom.iterable, scripthost를 사용
  - lib 를 지정하면 그 lib 배열로만 라이브러리를 사용함.
- outDir, outFile
- module
  - 컴파일 된 모듈의 결과물을 어떤 모듈 시스템으로 할지 결정


**PREV > [3. IDE 활용](https://github.com/mirrors89/study/tree/master/typescript/3_IDE_활용.md)**

**NEXT > 5.Typescript Basic Types**
