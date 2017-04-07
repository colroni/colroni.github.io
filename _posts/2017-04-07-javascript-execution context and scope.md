---
layout: post
title:  "javascript 실행 영역과 스코프 (Execution Context and Scope)"
date:   2017-04-07
author: colroni
categories: javascript
---

#### javascript 실행 영역과 스코프 (Execution Context and Scope)
자바스크립트는 타 프로그래밍 언어에 비해 조금 더 융통성있는 특별한 실행 영역과 스포프를 가지고 있다.
이로 인해 function은 다양한 영역에서 호출될 수도 있고, 그러한 스코프를 생성하고 유지할 수 있다. 이러한 컨셉은 클로져와 같은 특별한 디자인 패턴을 자바스크립트에서 구현 가능할 수 있게 만들었다.

##### Execution Context
흔히 컨텍스트라 부르기도 하는 실행영역은 스크립트가 실행되면서 생성(global context)되거나 function call에 의해 생성(active context)되게 된다.
각각의 활동 컨텍스트(active context)들은 function call에 의해 생성되며 생성된 순서대로 Stack 메모리에 삽입되게 되고, Stack의 FILO 원리대로 혅재 실행중인 컨텍스트는 Stack의 최상위에 위치하게 된다. 각각의 컨텍스트는 언제나 1개의 변수 환경(variable environment), 1개의 구성 환경(lexical environment), 그리고 1개의 thisbinding 속성, 총 3개의 부분으로 구성되어 있다.
그리고 3개의 속성 모두 object형식으로 저장된다.(자바스크립트의 object 자료형으로 저장되는게 아니다. 그래서 사용자가 access할 방법은 전혀 없다.)

##### Lexical Environment Object
한글로 번역하자면 "구성 환경 객체" 라고 할 수 있겠다. 해당 컨텍스트에서 선언된 변수/함수들(identifier)의 reference 값을 저장하는 객체이다.(내부 구성은 variable environment object에 저장)변수/함수이름을 reference로 변환할때 사용된다. 실행영역의 생성 초기시점에는 아래에서 설명할 variable environment object와 정확히 같은 값을 가지나, 컨텍스트내부에서 scope augmentation이 실행될 시 variable environment object와는 달리 새로운 identifier와 그의 reference값이 추가된다.

##### Variable Environment Object
한글로 번역하자면 "변수 환경 객체"라고 할수 있다. 사실 변수 환경 또한 lexical environment에 포함되는 개념이나, 위에서 설명한 lexical environment object가 생성 후에 내부 값이 변할 수 있는 것과 달리 variable environment object는 내부에서 선언된
* 변수(variable)
* 함수 선언(function declaration)
* 함수 매개 변수(function parameter)

들을 저장하기 때문에 호이스팅등 this 키워드를 이용한 표현에 의해 새로운 변수/함수가 등장하더라도 절대로 값이 변하지 않는다.

##### ThisBinding Object
thisbinding 객체는 해당 실행 영역의 this 키워드의 반환 값을 저장한다.
실행 영역에서 사용자가 유일하게 접근 가능한 부분이다. 참고로 this 키워드는 현재 컨텍스트가 참조하고 있는 객체를 가리키며 이값은 어떻게 함수가 호출되었냐에 따라 갈린다.
당연하겠지만 실행 영역 내부의 모든 코드가 실행이 완료된 후에는 실행 영역은 Stack에서 삭제되며 그와 관련된 lexical environment object, variable environment object, thisbinding object 모든 구성 환경 값들 또한 삭제된다.
global context는 최상위이자 가장 밖의 실행 영역으로 함수 외부에서 선언된 모든 변수/함수들이 이에 포함된다.
웹 브라우저 환경에서 global context의 variable environment object는 윈도우 오브젝트이며 global context에서 선언된 변수/함수들은 모두 윈도우 오브젝트에 저장된다.
이 실행 역역 내부의 모든 코드가 실행이 되고 난 후에는 윈도우 오브젝트에서 삭제되고 그 안에 저장된 변수/함수들 또한 파괴된다.
윈도우 오브젝트 위의 global context는 웹페이지가 종료 되고 난 후에야 파괴된다.

실행 영역의 variable environment object는 아래와 같은 형식으로 저장된다.
~~~javascript
var a = "글로벌";
var b = 33;

function fc(x){
	var c = "함수 컨텍스트";
};
~~~
위와 같은 코드는 아래와 같은 변수 객체를 가지게 된다.
~~~javascript
//variable object of the global context
변수객체(global context, 글로벌 컨텍스트) = {
	//웹브라우저 환경에서는 글로벌 컨텍스트의 변수수 객체가 윈도우 오브젝트이다.
	a:"글로벌";
    b:33;
    fc: fc함수가 저장된 메모리 주소
}
DO(fc function context, 함수 내부의 컨텍스트){
	x:20;
    y:"함수 컨텍스트";
}
~~~

##### Varialbe Scope(변수 스코프)
흔히 스코프라 부르는게 바로 변수 스코프이다. 변수 스코프는 변수에 접근이 가능한 유효 범위를 뜻한다. 즉 변수, 함수, 매개 변수(formal parameter)의 접근성과 생존 기간을 말하는 개념이다.
스코프는 실행 영역과 실행 영역의 lexical environment 개념과 긴밀한 관계에 있기 때문에 위의 실행 영역을 잘 이해했다면 스코프도 쉽게 이해할 수 있다.

스코프는 크게 global scope와 local scope로 나누는데 이 중 global scope는 스크립트 실행시 생성되는 실행 영역의 스코프이고, local scope는 함수 호출시 생성되는 실행 영역의 스코프이다.
참고로 스코프는 if, for문 같은 블럭에서는 생성되지 않고 오직 function 단위로만 생성된다.
하지만 with 문, try-catch 문으로 스코프를 변경할 수 있다.
~~~javascript
function calculation(){
	var numToAdd = 3;
    var numToSubtract = 1;
    
    add();
    subtract();
    
    function add(){
    	number = number + numToAdd;
    }
    
    function subtract(){
    	number = number + numToSubtract;
    }
}

var number = 0;
calculation();

console.log(number); //2
console.log(numToAdd); //에러
console.log(numToSubtract); //에러
~~~
위의 코드에서  함수 calculation은 총 2개의 variable environment object에 접근이 가능하다. 바로 본인의 영역과 연관된 variable environment object와 본인의 상위 영역인 global context의 variable environment object. 그리고 calculation함수 내부에서 선언된 함수들인 add와 subtract는 각각 3개의 variable environment object에 접근이 가능하다.
본인의 것, 그리고 2개의 상위 영역의 것들.
이러한 이유로 nember, numToAdd, numToSubtract라는 변수는 calculation함수 내부뿐만 아니라 add와 subtract함수에서도 접근이 가능해진다. 하지만 calculation함수의 실행이 끝난 후, 즉 함수에서 벗어난 이후에는 함수 영역의 variable environment object에 접근이 불가능하다.
이러한 이유로 사용자는 calculation함수의 실행이 끝난 후에 numToAdd, numToSubtract함수에 접근할 수 없게 된다.

반대로 함수의 실행이 끝나기 전이나 가장 최근 호출된 함수가 계속 사용될 경우, 즉 함수가 자신의 최상위 실행 영역을 참조하고 있는 동안에는 실행 영역은 절대로 파기되지 않는다.
바로 이 점을 이용해 자바스크립트에서 클로저(closure)라는 기법을 사용할 수 있게 된다.

이게 바로 variable scope, 변수 유효 범위이다...




