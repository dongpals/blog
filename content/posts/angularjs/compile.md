---
title: "Compile"
date: 2019-06-26T00:03:53+09:00
draft: true
---
## Overview

Compiles an HTML string or DOM into a template and produces a template function, which can then be used to link scope and the template together.
HTML 문자열 또는 DOM을 템플릿으로 컴파일하고 템플릿 함수를 생성한 다음 scope와 템플릿을 함께 연결하는데 사용될 수 있습니다.

The compilation is a process of walking the DOM tree and matching DOM elements to directives.
컴파일은 DOM 트리를 걷고 지시자에 DOM 요소를 일치시키는 프로세스 입니다.

Note: This document is an in-depth reference of all directive options. For a gentle introduction to directives with examples of common use cases, see the directive guide.
이 문서는 모든 지시자 옵션의 상세한 참고입니다. 흐한 사용사례의 예제와 함께 지시자에 대한 간랸한 소개를 위해 지시자 가이드를 살펴보십시오.

## Comprehensive Directive API
There are many different options for a directive.

The difference resides in the return value of the factory function. You can either return a Directive Definition Object (see below) that defines the directive properties, or just the postLink function (all other properties will have the default values).
차이점은 팩토리 함수의 반환값에 있습니다. 지시자 속성을 정의하는 지시자 정의 객체나 postLink 함수만을 반호나할 수 있습니다. (모든 다른 속성은 기본값을 가지고 있을 수 있습니다.)

> Best Practice: It's recommended to use the "directive definition object" form.

Here's an example directive declared with a Directive Definition Object:
다음은 지시자 선언 객체로 선언된 지시자 예제입니다.

```bash
var myModule = angular.module(...);

myModule.directive('directiveName', function factory(injectables) {
  var directiveDefinitionObject = {
    priority: 0,
    template: '<div></div>', // or // function(tElement, tAttrs) { ... },
    // or
    // templateUrl: 'directive.html', // or // function(tElement, tAttrs) { ... },
    transclude: false,
    restrict: 'A',
    templateNamespace: 'html',
    scope: false,
    controller: function($scope, $element, $attrs, $transclude, otherInjectables) { ... },
    controllerAs: 'stringIdentifier',
    bindToController: false,
    require: 'siblingDirectiveName', // or // ['^parentDirectiveName', '?optionalDirectiveName', '?^optionalParent'],
    multiElement: false,
    compile: function compile(tElement, tAttrs, transclude) {
      return {
         pre: function preLink(scope, iElement, iAttrs, controller) { ... },
         post: function postLink(scope, iElement, iAttrs, controller) { ... }
      }
      // or
      // return function postLink( ... ) { ... }
    },
    // or
    // link: {
    //  pre: function preLink(scope, iElement, iAttrs, controller) { ... },
    //  post: function postLink(scope, iElement, iAttrs, controller) { ... }
    // }
    // or
    // link: function postLink( ... ) { ... }
  };
  return directiveDefinitionObject;
});
```

> Note: Any unspecified options will use the default value. You can see the default values below.

Therefore the above can be simplified as:

```bash
var myModule = angular.module(...);

myModule.directive('directiveName', function factory(injectables) {
  var directiveDefinitionObject = {
    link: function postLink(scope, iElement, iAttrs) { ... }
  };
  return directiveDefinitionObject;
  // or
  // return function postLink(scope, iElement, iAttrs) { ... }
});
```
## Life-cycle hooks
Directive controllers can provide the following methods that are called by AngularJS at points in the life-cycle of the directive:
지시자 컨트롤러들은 지시자의 수명주기에서 AngularJS에 의해 호출되는 다음 메서드을 제공할 수 있습니다.  

* $onInit() - Called on each controller after all the controllers on an element have been constructed and had their bindings initialized (and before the pre & post linking functions for the directives on this element). This is a good place to put initialization code for your controller.  

요소에 모든 컨트롤러가 구성되고 바인딩이 초기화된 후 각 컨트롤러에 호출됩니다. 컨트롤러의 초기화 코드를 넣기 위한 좋은 장소 입니다.  

* $onChanges(changesObj) - Called whenever one-way (<) or interpolation (@) bindings are updated. The changesObj is a hash whose keys are the names of the bound properties that have changed, and the values are an object of the form { currentValue, previousValue, isFirstChange() }. Use this hook to trigger updates within a component such as cloning the bound value to prevent accidental mutation of the outer value. Note that this will also be called when your bindings are initialized.  

단 방향 또는 interpolation 바인딩이 업데이트 되면 호출됩니다. changesObj는 키가 변경된 바운드 속성의 이름인 해쉬이고 값은 폼 객체 입니다.

* $doCheck() - Called on each turn of the digest cycle. Provides an opportunity to detect and act on changes. Any actions that you wish to take in response to the changes that you detect must be invoked from this hook; implementing this has no effect on when $onChanges is called. For example, this hook could be useful if you wish to perform a deep equality check, or to check a Date object, changes to which would not be detected by AngularJS's change detector and thus not trigger $onChanges. This hook is invoked with no arguments; if detecting changes, you must store the previous value(s) for comparison to the current values.  

digest 사이클 각 차례마다 호출됩니다. 변경을 감지하고 조치할 기회를 제공합니다. 감지하는 변경의 응답으로 수행하고 싶은  모든 작업은 이 훅에서 호출되어야 합니다. 이것을 구현하면 $onChanges가 호출될때 효과가 없습니다. 예를들어 이 훅은 깊은 균등 검사 또는 데이터 객체를 검사를 수행하려 한다면 유용합니다. AngularJS의 변경 감시자가 감지하지 않는 변경은 $onChanges트리거 하지 않습니다. 이 훅은 인수없이 호출됩니다. 변경을 감지하면 현재 값과 비교하기 위해 이전 값을 저장해야만 합니다.

* $onDestroy() - Called on a controller when its containing scope is destroyed. Use this hook for releasing external resources, watches and event handlers. Note that components have their $onDestroy() hooks called in the same order as the $scope.$broadcast events are triggered, which is top down. This means that parent components will have their $onDestroy() hook called before child components.

포함하는 범위과 파괴될때 컨트롤러에서 호출됩니다. 출시된 외부 리소스를 해체하기 위해 이 훅을 사용합니다. 컴포넌트는 탑다운 방식으로 $scope.broadcast 이벤트들이 트리거되는 것과 같은 순서로 호출되는 $onDestroy() 훅을 갖습니다. 부모 컴포넌트가 자식 컴포넌트 보다 먼저 호출되는 $onDestroy() 훅을 갖습니다.

* $postLink() - Called after this controller's element and its children have been linked. Similar to the post-link function this hook can be used to set up DOM event handlers and do direct DOM manipulation. Note that child elements that contain templateUrl directives will not have been compiled and linked since they are waiting for their template to load asynchronously and their own compilation and linking has been suspended until that occurs.  

이 컨트롤러의 요소와 그 자식들이 연결된 후에 호출됩니다. post-link 함수와 비슷한 이 훅은 DOM 이벤트 핸들러들을 설정하고 직접 DOM 조작을 하는데 사용될 수 있습니다. 그들의 템플릿이 비동기적으로 로드되기를 기다리기 때문에 templateUrl 지시자들을 포함하는 자식 요소들은 컴파일과 연결되지않고 그들의 컴파일과 연결은 로드될때 까지 지연됩니다.  

## Comparison with life-cycle hooks in the new Angular
The new Angular also uses life-cycle hooks for its components. While the AngularJS life-cycle hooks are similar there are some differences that you should be aware of, especially when it comes to moving your code from AngularJS to Angular:
새로운 Angular는 컴포넌트를 위해 life-cycle hooks을 사용합니다. AngularJS 생명주기 훅은 비슷하지만 특히 AngularJS에서 Angular로 코드를 옮기게 되면 당신이 알아야되는 몇가지 차이점이 있습니다.

AngularJS hooks are prefixed with $, such as $onInit. Angular hooks are prefixed with ng, such as ngOnInit.  
AngularJS hooks can be defined on the controller prototype or added to the controller inside its constructor. In Angular you can only define hooks on the prototype of the Component class.
Due to the differences in change-detection, you may get many more calls to $doCheck in AngularJS than you would to ngDoCheck in Angular.
Changes to the model inside $doCheck will trigger new turns of the digest loop, which will cause the changes to be propagated throughout the application. Angular does not allow the ngDoCheck hook to trigger a change outside of the component. It will either throw an error or do nothing depending upon the state of enableProdMode().

## Life-cycle hook examples
This example shows how you can check for mutations to a Date object even though the identity of the object has not changed.  
이 예제는 객체의 ID가 변경되지 않더라도 Date 객체의 변형을 확인할 수 있는 방법을 보여줍니다.