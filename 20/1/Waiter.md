<p align = 'center'>
  <img src='https://github.com/JohnCoene/waiter/raw/master/man/figures/logo.png' width = "150"/>
</p>

# Waiter by [JohnCoene](https://github.com/JohnCoene)

Loading screens for Shiny

# 목차

- [이 라이브러리는](#이-라이브러리는)
- [설치 방법](#설치-방법)
- [기능](#기능)
- [사용방법](#사용-방법)
- [예시](#예시)
- [데모페이지](#데모페이지)

# 이 라이브러리는

Waiter는 코드를 통해 <br>

Shiny Application의 화면을 숨기거나 다시 보이게 하는 라이브러리입니다.<br>

[깃허브 링크](https://github.com/JohnCoene/waiter)를 참조하세요. 

# 설치 방법

`install.packages('waiter')`

버전 0.1.0은 위 커맨드를 통해 CRAN 을 통해서 설치 할 수 있으며, 

개발 중인 버전은 `devtools::install_github('JohnCoene/waiter')`로 설치 할 수 있습니다. 

# 기능

크게 3가지 기능 `Waiter`, `Waitress`, `Hostess`로 나누어져 있으며 

각각 의도하는 바는 다음과 같이 설명하고 있습니다만, 

- wait**er** -> Spinn**er** <br>
- waitr**ess**, hostess -> Progr**ess** <br>
- *hostess* 는 waiter에 waitress를 사용 할 수 있게 하는 것이라 생각 하시면 됩니다. 

실제 가능한 기능은 아래 테이블과 같습니다. 

<div align = 'center'>
  
|Feature|Waiter|Waitress|Hostess|
|:---:|:---:|:---:|:---:|
|`Progress Bar`|:heavy_check_mark:|:heavy_check_mark:|:heavy_check_mark:|
|`Full Screen`|:heavy_check_mark:|:heavy_check_mark:|:x:|
|`Works with waiter`|:heavy_check_mark:|:x:|:heavy_check_mark:|
|`Spinner`|:heavy_check_mark:|:x:|:x:|
|`Updatable`|:heavy_check_mark:|:x:|:x:|
|`Notifications`|:x:|:heavy_check_mark:|:x:|

</div>

[WebSite](https://waiter.john-coene.com/#/)

# 사용 방법

핵심 순서는 아래와 같습니다. 

## Waiter 의 경우

0. `use_waiter`를 UI 헤더에 선언,

1. `waiter` 오브젝트 생성
2. `waiter` 오브젝트 보이기
3. `waiter` 오브젝트 숨기기

## Waitress 의 경우

0. `use_waitress`를 UI 헤더에 선언,

1. `waitress` 오브젝트 생성
  1-1. `start` 함수 호출

2. `waitress` 오브젝트 진행
3. `waitress` 오브젝트 숨기기

# 예시 

예시로 쓸 어플리케이션은 아래와 같은 코드를 사용하여,<br>
버튼을 누르면 **3초 후에** Hello World를 출력하는 어플리케이션 입니다. 

<img src='https://user-images.githubusercontent.com/6457691/73427256-b7160980-4379-11ea-9676-6cca93baad9d.gif'/>

```R
library(shiny)

ui = fluidPage(
  actionButton('Hi','Button'),
  textOutput('out')
)

server = function(input, output, server){
  observeEvent(input$Hi,{
    
    for(i in 1:3){
      Sys.sleep(1)
      showNotification(as.character(i), duration = 1)
    }
    
    output$out = renderText("Hello World")
  })
}

shinyApp(ui,server)
```

이제 위 어플리케이션에 `Waiter`를 더해보겠습니다. 접혀진 부분을 클릭하면 사용한 코드를 확인 할 수 있습니다. 

<details>
  <summary> 
    0. <code>use_waiter</code> 를 UI 헤더에 선언
  </summary>
  
```R
ui = fluidPage(
  use_waiter(), # Ceclare waiter
  actionButton('Hi','Button'),
  textOutput('out')
)
```

</details>

<details>
  <summary>
    1. <code>waiter</code> 오브젝트 생성
  </summary>
  
```R
server = function(input, output, server){

  w = Waiter$new() ## Create Waiter
  
  observeEvent(input$Hi,{
    
    for(i in 1:3){
      Sys.sleep(1)
      showNotification(as.character(i), duration = 1)
    }
    
    output$out = renderText("Hello World")
  })
}
```

</details>

<details>
  <summary>
    2. <code>waiter</code> 오브젝트 보이기
  </summary>
  
```R
server = function(input, output, server){

  w = Waiter$new() 
  
  observeEvent(input$Hi,{
    w$show() ## Show waiter
    for(i in 1:3){
      Sys.sleep(1)
      showNotification(as.character(i), duration = 1)
    }
    
    output$out = renderText("Hello World")
  })
}
```

</details>


<details>
  <summary>    
    3. <code>waiter</code> 오브젝트 숨기기
  </summary>
  
```R
server = function(input, output, server){

  w = Waiter$new() 
  
  observeEvent(input$Hi,{
    w$show() 
    for(i in 1:3){
      Sys.sleep(1)
      showNotification(as.character(i), duration = 1)
    }
    w$hide() ## Hide waiter
    output$out = renderText("Hello World")
  })
}
```

</details>

최종 코드와 실행결과는 아래와 같습니다.

```R
library(shiny)
library(waiter)

ui = fluidPage(
  use_waiter(),
  actionButton('Hi','Button'),
  textOutput('out')
)

server = function(input, output, server){
  
  w = Waiter$new() 
  
  observeEvent(input$Hi,{
    w$show() 
    for(i in 1:3){
      Sys.sleep(1)
      showNotification(as.character(i), duration = 1)
    }
    w$hide() 
    output$out = renderText("Hello World")
  })
}

shinyApp(ui,server)
```

<img src='https://user-images.githubusercontent.com/6457691/73427950-47088300-437b-11ea-87b1-845ee10a4b17.gif'/>


아래 코드는 위 어플리케이션에 `waiter`대신 `waitress`를 사용한것 입니다.

```R
ui = fluidPage(
  use_waitress(), # declare
  actionButton('Hi','Button'),
  textOutput('out')
)

server = function(input, output, server){
  
  w = Waitress$new()$start() # declare
  
  observeEvent(input$Hi,{
    for(i in 1:3){
      w$inc(100/3) # increase
      Sys.sleep(1)
      showNotification(as.character(i), duration = 1)
    }
    w$hide() # hide
    output$out = renderText("Hello World")
  })
}

shinyApp(ui,server)
```

<img src='https://user-images.githubusercontent.com/6457691/73429150-4fae8880-437e-11ea-952f-d446d887a868.gif'/>

# 데모페이지

아래 그림은 Hostess 예시를 설명해놓은 Shiny Application 페이지입니다.

[링크](https://shiny.john-coene.com/waiter/)를 참조하세요. 

<img src='https://user-images.githubusercontent.com/6457691/73429688-8fc23b00-437f-11ea-841c-8af05399783c.gif'/>



