<p align = 'center' >
  <img src='https://user-images.githubusercontent.com/6457691/80343869-a0f8e600-88a1-11ea-9f9a-148490193fe3.png' width = '150'>
</p>

* Official Hexsticker / Icon 이 없어서 그럴싸한 이미지를 가져왔습니다. [freepik](https://www.flaticon.com/kr/authors/freepik)

# ShinyFeedBack by [merlinoa](https://github.com/merlinoa)

display user feedback next to Shiny inputs [링크](https://github.com/merlinoa/shinyFeedback)

# 목차

- [이 라이브러리는](#이-라이브러리는)
- [설치방법](#설치방법)
- [기능](#기능)
- [사용방법](#사용-방법)
- [예시](#예시)
- [데모페이지](#데모페이지)

# 이 라이브러리는

ShinyFeedBack 은 이름에서 나타낸대로, <br>

Shiny 상에서 Input이 들어올 때에 시각적으로 FeedBack을 주는 라이브러리입니다.<br>

안타깝게도, 이 역시 마찬가지로 [깃허브 링크](https://github.com/merlinoa/shinyFeedback)에는 별 다른 설명이 없습니다. 

번거롭지만, R package를 설치 후 `browseVignettes(package = "shinyFeedback")`를 통해서 Vignette를 확인 할 수 있습니다. 

대신, [shinyapps 링크](https://tychobra.shinyapps.io/shinyFeedbackExamples)를 통해 설치과정 없이 해당 패키지를 사용 할 수 있습니다.

# 설치방법

`install.packages('shinyFeedBack')`

- `Shiny` 가 아닌 `shiny`에 주의.

# 기능

현재 6개의 Shiny Input function 에 대해서 Feedback 을 제공 합니다. 

1. `textInput`
2. `numericInput`
3. `selectInput`
4. `dateInput`
5. `sliderInput`
6. `PickerInput` ( shinyWidgets )

이 외에 `loadingButton`과 관련된 기능이 있긴 합니다만, 자주 쓰이게 될 주요한 기능은 아니니 생략해도 좋습니다.

Feedback의 종류는 다음 4가지 입니다.

1. ![#d9534f](https://placehold.it/15/d9534f/000000?text=+) `Danger`
2. ![#f89406](https://placehold.it/15/f89406/000000?text=+) `Warning`
3. ![#5cb85c](https://placehold.it/15/5cb85c/000000?text=+) `Success`
4. ![#000000](https://placehold.it/15/000000/000000?text=+) `Normal`

- Normal의 경우 Keyword 를 붙이지 않고 사용.

# 사용 방법

1. `ui`에 `useShinyFeedBack()`으로 declare.
  - `shiny` 아닌 `Shiny`에 주의.

2. `inputWidget`에 연결된 `observeEvent` 내부에 조건을 주어 `showFeedbackWarning` / `hideFeedback` 같은 쌍을 사용.

`showFeedback` 시리즈의 경우 입력 parameter는 5가지 입니다.

1. `inputId` : Feedback을 줄 element 의 ID 
2. `text` : Feedback의 내용
3. `color` : Feedback의 색상
4. `icon` : Feedback 발생시 띄울 `shiny::icon`, default는 `glyphicon` 라이브러리를 사용.
5. `session` : 세션 정보, default 는 현재 활성화된 도메인.

즉, `showFeedbackWarning`, `showFeedbackDanger`, `showFeedbackSuccess`는 `showFeedback`을 통해서 구현이 가능합니다.


# 예시

- `myInput` 이라는 `textInput`에 입력된 글자 수가 3자 이상일 경우 경고창을 띄우는 예시.

<img src='https://user-images.githubusercontent.com/6457691/80346052-1914db00-88a5-11ea-9a40-cfa0da9189dd.gif'>

```R
library(shiny)
library(shinyFeedback)

ui <- fluidPage(
  useShinyFeedback(), # include shinyFeedback
  
  textInput(
    "myInput",
    "Warn if >3 characters",
    value = ""
  )
)

server <- function(input, output, session) {
  observeEvent(input$myInput, {
    
    if (nchar(input$myInput) > 3) {
      showFeedbackWarning(
        inputId = "myInput",
        text = "too many chars"
      )  
    } else {
      hideFeedback("myInput")
    }
    
  })
}

shinyApp(ui, server)
```

# 데모페이지 

아래 그림은 Feedback의 예시를 설명해놓은 Shiny Application 페이지입니다.

[링크](https://tychobra.shinyapps.io/shinyFeedbackExamples)를 참조하세요.

<img src='https://user-images.githubusercontent.com/6457691/80344553-be7a7f80-88a2-11ea-86e1-d14e38b7703d.png'>
