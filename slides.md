---
theme: default
fonts:
  sans: Google Sans
  mono: Roboto Mono Light
title: "From Boring to Charming: Building Expressive CLI with Go and Charm CLI"
# apply UnoCSS classes to the current slide
class: text-center
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
---
<img src="/images/title.jpg"/>
---
layout: center
---
# About Me
- Thet Naing Tun
  - Software Engineer at Kasikorn-X
  - <a href="https://thetnaingtun.dev">thetnaingtun.dev</a>
- Amateur Badminton Player 🏸 

---
layout: center
---
# Who is this for?
- New to building CLI apps.
- Want to build CLI app to meet your own need.
- Veteram who experienced in building CLI application can also gain different perspective on how to build CLI application in new paradim
---
layout: center
class: text-center
---
# What can we build with Bubbletea framework?
---
level: 2
---
# gh-dash
---
level: 2
---
# hackernew

---
---
# Agenda
<Toc />

---
---
# The Elm Architecture
<v-click>
The Elm Architecture revolve around three main concepts:
</v-click>
<ul>
  <li v-click>Model- Represents the state of the application</li>
  <li v-click>
    View - Defines how the model is rendered on the screen
  </li>
  <li v-click>Update - Describes how the state changes in response to messages or user actions</li>
</ul>
<img v-click class="mx-auto my-4" width="400" src="/images/elm-architecture.png"/>

<p v-click>
These three concepts form a continuous loop that runs throughout the entire lifecycle of the application.
</p>

---
---

# Bubbletea

Bubbletea TUI framework based on ELM architecture. In Bubbletea Model is represent as an `interface` which define three methods `Init`, `Update` and `View`.

```go
// https://pkg.go.dev/github.com/charmbracelet/bubbletea#Model

type Model interface {
	Init() Cmd
	Update(Msg) (Model, Cmd)
	View() string
}
```

This mean <b>any</b> can be <b>model</b> as long as it implement these methods.

<style>
h1 {
  background-color: #854cff;
  background-image: linear-gradient(45deg, #854cff 10%, #6a4dff 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---
level: 2
---
# Model: The Heart of the Application

````md magic-move {lines: true}
```go [main.go]
package main

import(
	"github.com/charmbracelet/bubbles/textarea"
)

type model struct{
  // current state of application
  loading bool
  // can include other model as well!
  textarea textarea.Model
}
```
```go
package main

import(
	...
)

type model struct{
  ...
}

func(m *model)Init()Cmd{
  return nil
}

```
```go
package main

import(
	...
)

type model struct{
  ...
  textarea textarea.Model
}

func(m *model)Init()Cmd{
  return m.textarea.Cursor.BlinkCmd()
}

```

```go
package main

import(
	...
)

type model struct{
  ...
}

func(m *model)View()string{
  return "Hello World"
}

```
```go
package main

import(
	"github.com/charmbracelet/bubbles/textarea"
)

type model struct{
  loading bool
  textarea textarea.Model
}

func(m *model)View()string{
  if m.loading {
    return "Loading..."
  }

  return m.textarea.View()
}
```
```go {*|9|10|12-13|14-16|18-19|22|*}
package main

import(
	tea "github.com/charmbracelet/bubbletea"
)

func(m *model)Update(msg tea.Msg)(tea.Model, tea.Cmd){
  // update the model depending on the incoming message(msg)
  switch msg := msg.(type) {
  case tea.KeyMsg:
    switch msg.Type {
    case tea.KeyCtrlC, tea.KeyEsc:
      return m, tea.Quit
    case tea.KeyEnter:
      value := m.textarea.Value() // do something when user press Enter key!
      m.loading = false // update model based on message
    }
  case fetchProductsDone:
    // can update model based on this custom message too!
  }

  return m, nil // return new updated model 
}
```
````
---
level : 2
---
# Message: bubbletea.Msg
- Simply an empty interface
- Any type can be used as a `Msg`

```go
// https://pkg.go.dev/github.com/charmbracelet/bubbletea#Msg

type Msg interface{}

type fetchProductsDone{
  products []Product
}

func fetchProducts() tea.Msg {
  return fetchProductsDone{
    products: []Product{}
  }
}
```


---
level: 2
---
# Commands: bubbletea.Cmd
- A command is simply a function that returns a `tea.Msg`.
- Use to perform I/O (e.g. reading from a disk, making network calls).

```go
type fetchProductsDone{
  products []Product
}

type errMsg{
  err error
}

func fetchProducts() tea.Msg{
  resp, err := http.Get("https://example.com/products")

  if err != nil{
    return errMsg{err:err}
  }

  return fetchProductsDone{
    products: products
  }
}
```
---
level: 2
---
# Commands(Cont'd): How to use it
- Return a command from `Update` method together with updated model.
- Bubbletea will call the command for us.
````md magic-move {lines: true}
```go{*|7|12|13-14|15-16|19}
type model struct{
  ...
  list list.Model
}

func(m *model)Update(msg tea.Msg)(tea.Model, tea.Cmd){
  var cmds []tea.Cmd
  switch msg := msg.(type) {
  case tea.KeyEnter:
    value := m.textarea.Value()
    m.loading = false
    cmds = append(cmds, fetchProducts)
  case errMsg:
    m.err = errMsg.err // if there is an error, do some error handing
  case fetchProductsDone:
    cmds = append(cmds, m.list.SetItems(msg.products))
  }

  return m, tea.Batch(cmds...)
}
```
````
---
layout: center
---
# Demo Time!

<style>
h1 {
  background-color: #854cff;
  background-image: linear-gradient(45deg, #854cff 10%, #6a4dff 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>