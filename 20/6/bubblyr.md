<p align = 'center'>
 <img src='https://user-images.githubusercontent.com/6457691/86201569-df10cf80-bb9a-11ea-872b-6b07632f35b2.gif' width = '150'>
</p>

# bubblyr by [feddelegrand7](https://github.com/feddelegrand7)

# 목차

- [이 라이브러리는](#이-라이브러리는)
- [설치방법](#설치방법)
- [사용방법](#사용-방법)
- [예시](#예시)

# 이 라이브러리는

`bubblyr` 은 `bubbly-bg`라는 javascript library를 Shiny에 구현한 라이브러리입니다.<br>

여기서, [bubbly-bg](https://github.com/tipsy/bubbly-bg)는 적은 용량의 소스로 (1kb 미만) 버블을 배경에 움직이는 이미지로 넣는 역할을 합니다.

사용 할 수 있는 테마는 `ocean`, `cherry`, `bigmom` 등이 있습니다. 

# 설치방법
```R

install.packages('bubblyr') # CRAN version 0.1.0

remotes::install_github("feddelegrand7/bubblyr") # Development version

```

# 사용 방법

1. `ui`에 `bubbly(~~)`으로 declare 및 사용.  

# 예시

- 기본 `fluidPage`에, 
- 배경으로 `bigmom`이라는 `bubblyr` 테마를 적용 후,
- `hello world`라는 text와 
- `A-B` 라는 graph를 출력하는 어플리케이션.

<img src='https://user-images.githubusercontent.com/6457691/86201569-df10cf80-bb9a-11ea-872b-6b07632f35b2.gif'>

```R
library(shiny)
library(bubblyr)
library(shinyCyJS)

ui <- fluidPage(
  
  bubbly(theme = "bigmom"),
  textOutput('text1'),
  ShinyCyJSOutput('graph1')  
  
)

server <- function(input, output) {
  output$text1 = renderText('Hello World')
  output$graph1 = renderShinyCyJS(
    shinyCyJS(
      list(buildNode('A'),buildNode('B'), buildEdge('A','B'))
    )
  )
}

shinyApp(ui = ui, server = server)

```

# 기타

## 작동원리

<img src='https://user-images.githubusercontent.com/6457691/86203004-c5718700-bb9e-11ea-801b-7896455c5eed.png'>

1. `htmltools`를 이용해서, 
2. 코어 라이브러리 (`bubbly-bg.js`) 호출 후, 
3. `bubbly()` 의 형태로, parameter 설정하여 실행 시킴.

## 권장사항
`Rstudio browser`에서 돌리기보다 `Internet browser`에서 돌리기를 권장함. 

사실 이건 대부분 shiny library 에도 해당 되는 내용인게, Rstudio의 자체 메모리에서 돌아가는 것보다, 브라우저에서 돌아가는게 훨씬 더 부드럽기 때문.


