# clojure programming Ch.6 Datatypes and Protocols
발제

## dynamic Expression Problem
The dynamic Expression Problem 규정의 목적
- 이미 있는 interface를 implementation 할 수 있는 interface 및 type 시스템을 갖춘다. (OOP 언어 대부분에서 만나는 장면)
- 새 interface를, 기존에 존재하던 type이 재 컴파일 없이 준수할 수 있는 방법을 제공한다. 

## Protocols
java interface의 유사 개념이라고 할 수 있다.
- 하나 이상의 method
- 각 메소드는 하나 이상의 argument를 가진다
- 첫 번째 argument는 java의 this keyword에 해당한다.
- Hence, protocols provide single type-based dispatch (효율적이고 웬만하면 요구사항 커버 됨. 더 많은 것이 필요하다면 multimethod)

### protocol definition
```
(defprotocol ProtocolName
"documentation"
(a-method [this arg1 arg2] "method docstring") 
(another-method [x] [x arg] "docstring"))
```
