---
title: "Creating Custom Directives"
date: 2019-06-29T20:09:14+09:00
draft: false
---

## What are Directives?
At a high level, directives are markers on a DOM element (such as an attribute, element name, comment or CSS class) that tell AngularJS's HTML compiler ($compile) to attach a specified behavior to that DOM element (e.g. via event listeners), or even to transform the DOM element and its children.  
고수준에서 지시자들은 AngularJS의 HTML 컴파일러가 지정된 동작을 DOM 요소들에 첨부하도록 지시하는 DOM 요소의 마커들입니다. 심지어 DOM 요소와 그 자식을 변형할 수 있습니다.

AngularJS comes with a set of these directives built-in, like ngBind, ngModel, and ngClass. Much like you create controllers and services, you can create your own directives for AngularJS to use. When AngularJS bootstraps your application, the HTML compiler traverses the DOM matching directives against the DOM elements.  
AngularJS는 내장된 일련의 지시자들을 포함하고 있습니다. 컨트롤러와 서비스를 생성하는것 처럼 AngularJS에서 사용할 당신의 지시자를 생성할 수 있습니다. AngularJS가 당신의 앱을 부트스트랩하면 HTML 컴파일러는 DOM 요소에 대하여 DOM 대응 지시자를 탐색합니다.

> What does it mean to "compile" an HTML template? For AngularJS, "compilation" means attaching directives to the HTML to make it interactive. The reason we use the term "compile" is that the recursive process of attaching directives mirrors the process of compiling source code in compiled programming languages.  

HTML 템플릿을 컴파일하는것은 무엇을 의미합니까? AngularJS에서 컴파일은 HTML에 지시자를 첨부하여 대화형으로 만드는 것을 의미합니다. 우리가 컴파일이란 용어를 사용하는 이유는 지시자를 첨부하는 반복적인 과정이 소스 코드를 컴파일된 프로그래밍 언어로 컴파일 하는 과정을 반영하기 때문입니다.

## Matching Directives
Before we can write a directive, we need to know how AngularJS's HTML compiler determines when to use a given directive.  
지시자를 작성하기 전에 우리는 AngularJS의 HTML 컴파일러가 주어진 지시자를 사용할 시기를 결정하는 방법을 알아야 합니다.

Similar to the terminology used when an element matches a selector, we say an element matches a directive when the directive is part of its declaration.  
요소가 선택자와 일치할때 사용하는 용어와 비슷합니다. 우리는 지시자가 선언의 일부일때 요소가 지시자와 일치한다고 이야기 합니다.

In the following example, we say that the `<input>` element matches the ngModel directive  
다음 예제에서 우리는 `input` 요소가 ngModel 지시자와 일치한다고 말합니다.

```
<input ng-model="foo">
```
The following `<input>` element also matches ngModel:

```
<input data-ng-model="foo">
```
And the following `<person>` element matches the person directive:

```
<person>{{name}}</person>
```