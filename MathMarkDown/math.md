# 논문 작성 프로세스 정리

## 문헌 검색

* ~~Papers app.~~ Bookend.app

## 논문 작성

* Scrivener

### 참고 문헌 삽입

* ~~Papers app.~~ Bookend.app의 floating citation 단축기는 ctrl + ctrl.
* 또는 {저자, 년도}로 바로 처넣기.
* 삽입한 문헌은 keyword에 흔적을 남겨 따로 관리해줘야함. 나중에 endnote library로 export해야 word에서 endnote로 style관리할 수 있음.

### 수식 삽입

* VScode에서 markdown 형식으로 작성 후 확인 -> C&P

### Formatting #1

* ~~Manuscript로 옮김.~~ -> Markdown(LATEX) 수식을 formatting 해준다.
* Target journal 에 맞춰 형식을 맞춘다.(introduction 등의 heading 처리와 subheading 처리)
* Word로 다시 export하면, 수식과 양식이 맞춰짐.

### Formatting #2

* Word로 옳김. insert equation에서 Markdown(LATEX) 수식입력해준다.
* Target journal에 맞춰 형식을 바꾼다. (Scrivener에서 가능한지 생각해볼 것)

#### 참고 문헌 Formatting

* 위 과정에서 citation key를 여전히 {}안에 있다.
* Papers app.에서 citation 들을 xml 형태로 export 후 Endnote에서 import.
  * import 시에 options에서 xml 형태임을 check 해줘야함.
* endnote library와 word의 formatting 연동. 끝.

### Table

* 미정.

# Math annotation 연습용

## Markdown의 Math 문법은 LATEX문법과 같음

## allen project 논문 삽입

### Methods for ANN

* Min-Max Scaling.

$$ X_{norm}=\frac{X-X_{min}}{X_{max}-X_{min}} $$

* RELU, linear transformation, Batchnormalization

$$ O_{l}=argmax(0, BatchNorm(W \cdot X_{l})) $$

* Weight initialization with the Xavier and He method

$$ Var(W)=\frac{2}{n_{inputs}} $$

