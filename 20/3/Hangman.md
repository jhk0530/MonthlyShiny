<p align = 'center'>
  <img src='https://user-images.githubusercontent.com/6457691/77505118-982d8280-6ea5-11ea-87f3-89be2df4e43d.png' width = "150"/>
</p>

# Hangman by [smirnovayu](https://github.com/smirnovayu)

Small app to play the well-known game hanbman. [링크](https://community.rstudio.com/t/hangman-2020-shiny-contest-submission/54937)

# 목차

- [이 라이브러리는](#이-라이브러리는)
- [설치방법](#설치방법)
- [코드설명](#코드설명)
- [정리](#정리)

# 이 라이브러리는

Hangman은 `Shiny contest 2020` 에 출품된 작품으로, <br>

말 그대로 Shiny 상에서 Hangman을 플레이 할 수 있게 하는 라이브러리입니다.<br>

안타깝게도, [깃허브 링크](https://github.com/smirnovayu/Hangman/tree/master/hangman)에는 별다른 설명이 없습니다. 

대신, [shinyapps 링크](https://smirnovayu.shinyapps.io/hangman_en/)를 통해 설치과정 없이 해당 패키지를 사용 할 수 있습니다.

# 설치방법

설치가 권장되지 않습니다. 위의 shinyapps 링크를 참조하세요.

# 코드 설명

## 0. 개요

Hangman은 크게 4개의 요소를 사용 합니다.

- 1. Application을 실제로 구현한 `app.R`
- 2. 클릭으로 입력을 받는 테이블을 만드는 `gen_table.R`
- 3. 임의의 단어를 불러오는 `gen_word.R`
- 4. 임의의 단어를 저장해 둔 `most-common-nouns-english.csv`

<img src = 'https://community.rstudio.com/uploads/default/original/3X/0/6/065cdc88ac4eaaa0988f8fa32bd006bfec28128c.png'></img>

다만, `gen_table.R`과 `gen_word.R`을 반드시 구분하는 것이 아닌, app.R에 포함 시켜도 될 것 같습니다. 내용도 복잡하지 않구요.
그래서 이번 주제에서는 `app.R` 만 간단히 코드 해설을 해 보도록 하겠습니다.

- 라이브러리 로드, 추가 소스코드 로드 부분 생략

## 1. UI

```R
ui <- fluidPage(
  
  bootstrapPage(
  ## Javascript 코드 선언
    tags$script(HTML("
      spectrum1_click = function(event) {
        var canvas = document.getElementById('spectrum1');
        if (canvas.getContext) {
          var context = canvas.getContext('2d');
          Shiny.setInputValue('x1', event.offsetX);
          Shiny.setInputValue('y1', event.offsetY)
        }
      };")),
    
    mainPanel(
      div(class="wrapper",
          plotOutput("allSpectrum", click = "plot1_click"), # click parameter 필요 없음.
          HTML("<canvas id='spectrum1' class='spectrum' width=700 height=400 onclick='spectrum1_click(event)'></canvas>")
      )
    )
  )
```

- CSS 스타일 설정 (코드 생략)
- 엘리먼트 배치 (코드 생략)
- Shinyjs 로드 (코드 생략)
- Javascript Code 설정 및 해당 Canvas 선언

저자는 엘리먼트 배치를 위해 `br()`을 어거지로 꾸겨넣었는데, <br>
그냥 css 속성에 margin 이나 padding을 주는게 보기엔 더 좋을 것 같습니다.<br>

주의 깊게 봐야 할 부분은 제일 마지막 부분입니다. 

get_table로 만든 알파벳 테이블을 이미지 형태로 뿌린 다음, 
해당 위치에 정확하게 겹치는 canvas를 만들고
그 canvas에 클릭 이벤트를 읽게 코드를 설정했습니다.

![image](https://user-images.githubusercontent.com/6457691/77513796-00399400-6eb9-11ea-9e1a-f1d340dfd3fe.png)

![image](https://user-images.githubusercontent.com/6457691/77513824-0e87b000-6eb9-11ea-8c32-a931ece1b3bf.png)

다만 이도 마찬가지로, js파일을 따로 빼서 선언하는것도 좋을 것 같습니다.


## 2. Server

```R
server <- function(input, output, session) {
  
  responses <<- callModule(module = gen_table, id = "gen_table")
  dt_word   <<- callModule(module = gen_word, id = "gen_word")
  counter   <<- 0
  
  output$allSpectrum <- renderPlot({
    
    color_palette = c("#85C1E9","#FFFFFF","FFFFFF")
    
    ggplot(responses, aes(x, y, fill = col, label = val)) +
      geom_tile(colour = 'white', show.legend = FALSE, width=0.99, height=0.99, size=0.5) +
      geom_text(hjust=0.5, vjust=0, size = 10)+
      scale_fill_manual(values=color_palette, drop = FALSE)+
      theme_void()+
      theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank(),
            panel.background = element_blank(), axis.line = element_blank())
  })
  
  
  observeEvent(c(input$x1), {
    output$allSpectrum <- renderPlot({ 
      color_palette = c("#85C1E9","#FFFFFF","#FFFFFF")
      
      ggplot(responses, aes(x, y, fill = col, label = val)) +
        geom_tile(colour = 'white', show.legend = FALSE, width=0.99, height=0.99, size=0.5) +
        geom_text(hjust=0.5, vjust=0, size = 10) +
        scale_fill_manual(values=color_palette, drop = FALSE)+
        theme_void()+
        theme(panel.grid.major = element_blank(), panel.grid.minor = element_blank(),
              panel.background = element_blank(), axis.line = element_blank())
      })
  })
  
 
  output$word_hidden <- renderText({     paste(dt_word$guessed, sep = ' ')  })
  
  observeEvent(input$x1, {
  
    output$word_hidden <- renderText({     
      
      if(counter == 10) {
        paste(dt_word$letter, sep = ' ') 
      } else {
        paste(dt_word$guessed, sep = ' ')  
      }
      
    })

  })
  
  
  observeEvent(input$x1, {
    
    x_pos = ceiling(as.numeric(input$x1) / 100)
    y_pos = abs(ceiling(as.numeric(input$y1) / 100) - 5 )
    
    letter = responses$val[responses$x == x_pos & responses$y == y_pos]
    used   = responses$col[responses$val == letter] != 'blue'
    
    responses$col[responses$val == letter] <<- 'red'
    
    dt_word$guessed[dt_word$letter == letter] <<- letter
    
    if (sum(dt_word$letter == letter) == 0 & !used) {
      counter <<- counter + 1
    }
    
  }, priority = 1)
  
  
  observeEvent(input$x1, {
    
    if (sum(grepl('_', dt_word$guessed)) == 0) {
      
      showModal(modalDialog(
        tags$div(
          style = "text-align: center;",
          tags$h2(
            tags$span(icon("laugh-wink"), style = "color: #FF7034;"),
            "Well done! You've won!",
            tags$span(icon("laugh-wink"), style = "color: #FF7034;")
          ),
          tags$br(), tags$br(),
          actionButton(
            inputId = "reload",
            label = "Play again!",
            style = "width: 100%;"
          )
        ),
        footer = NULL,
        easyClose = FALSE
      ))
      
      
    } else if(counter == 10) {
      
      msg = paste("the word was", paste0(dt_word$letter))
      
      showModal(modalDialog(
        tags$div(
          style = "text-align: center;",
          tags$h2(
            tags$span(icon("sad-cry"), style = "color: #6e91cc;"),
            "Oh, no! You lost...",
            tags$span(icon("sad-cry"), style = "color: #6e91cc;")
          ),
          tags$br(), tags$br(),
          actionButton(
            inputId = "reload",
            label = "Don't despair! Try again!",
            style = "width: 100%;"
          )
        ),
        footer = NULL,
        easyClose = FALSE
      ))            
    }       
  }, priority = -1)
  
  observeEvent(input$reload, {
    session$reload()
  }, ignoreInit = TRUE)
    
  observeEvent(c(input$x1), {
    output$image <- renderImage({      
      return(list(
        src = paste0("images/0",counter,".png"),
        contentType = "image/png",
        alt = "Face1"
      ))
      
    }, deleteFile = FALSE)
  })
  
  
}

```

서버에서 작업되는 내용은 아래와 같습니다. (코드 순서 대로 표기)

- 실행할 단어를 gen_table, gen_word로 부터 로드
- 시도 회수 count
- 알파벳 테이블 렌더링 (output$allSpectrum)
- 현재 진행중인 단어 렌더링 (output$word_hidden)
- (UI에서, input$x1 이라는 이벤트 발생하게 설정 됨)
- 클릭시 캔버스 위치에서 클릭된 알파벳 추측 (input$x1) 
- 클릭시 현재 진행중인 단어 재 렌더링 
- 클릭시 현재 알파벳 테이블 상황 재 렌더링
- 클릭시 단어 100% 혹은 기회 소진에 따라 결과물 출력
- reload 버튼에 reload 이벤트 설정 (input$reload)
- 클릭시 Hangman 그림 진행

우선 코드 자체에 styler로 한번 씌우는것도 괜찮아 보이고, <br>
무엇보다 input$x1 이벤트를 일일히 쪼개 놓은 부분은 수정을 하면 더 좋을 것 같습니다.

이 중에서 알파벳 추측 부분만 간단히 짚어보겠습니다.

```R
observeEvent(input$x1, {    
    x_pos = ceiling(as.numeric(input$x1) / 100)
    y_pos = abs(ceiling(as.numeric(input$y1) / 100) - 5 )
    
    letter = responses$val[responses$x == x_pos & responses$y == y_pos]
    used   = responses$col[responses$val == letter] != 'blue'    
    responses$col[responses$val == letter] <<- 'red'    
    dt_word$guessed[dt_word$letter == letter] <<- letter    
    if (sum(dt_word$letter == letter) == 0 & !used) { counter <<- counter + 1 }    
  }, priority = 1)  
```

`priority = 1`은 이벤트를 하나로 합치면 되는 부분이기 때문에 관심 갖지 않아도 좋고,<br>
canvas 의 사이즈를 강제로 700, 400이라고 정해둔 상태에서 알파벳의 배치 또한 7 * 4로 배치 해두었기 때문에

x 포지션을 예로 들면

| 0 ~ 100 | 100 ~ 200 | 200 ~ 300 | 300 ~ 400 | 400 ~ 500 | 500 ~ 600 | 600 ~ 700 |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| A | B | C | D | E | F | G |

이런식으로 x 포지션의 위치에 따라 알파벳의 내용을 계산 하는 방식을 사용 하고 있습니다.

------

# 정리

Hangman 은 작년의 Shiny contest winner 였던 
[Hex Memory Game](https://community.rstudio.com/t/shiny-contest-submission-hex-memory-game/25336)을 이어나가는 포지션을 잘 잡았다고 생각합니다.

특히 canvas를 이미지에 덮어 씌우고 클릭 이벤트를 발생시켜서 상호작용을 하는 건<br> 
기존의 shiny widget들로는 해내기 어렵기 때문에 배워둘만한 꿀팁인것 같습니다. <br>

이와 유사한 shiny contest 2020 작품으로는, [deminR](https://github.com/DivadNojnarg/deminR) 가 있는데 
열어 보진 않았지만 한가지 큰 차이점으로는 UI에 shinymobile을 썼다는 점이 있습니다.
관심이 있으시면 [설명 링크](https://community.rstudio.com/t/deminr-a-minesweeper-for-r-2020-shiny-contest-submission/56356)나 [깃허브 링크](https://github.com/DivadNojnarg/deminR)를 참조하길 바립니다. 
