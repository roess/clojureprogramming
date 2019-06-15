# clojure programming Ch.6 Datatypes and Protocols
발제

---

## dynamic Expression Problem
The dynamic Expression Problem 규정의 목적
- 이미 있는 interface를 implementation 할 수 있는 interface 및 type 시스템을 갖춘다. (OOP 언어 대부분에서 만나는 장면)
- 새 interface를, 기존에 존재하던 type이 재 컴파일 없이 준수할 수 있는 방법을 제공한다. 

---

## Protocols
java interface의 유사 개념이라고 할 수 있다.
- 하나 이상의 method
- 각 메소드는 하나 이상의 argument를 가진다
- 첫 번째 argument는 java의 this keyword에 해당한다.
- The first argument to each method in a protocol is said to be privileged because a particular protocol implementation will be chosen for each call based on it and, more specifically, on its type.
- Hence, protocols provide single type-based dispatch (효율적이고 웬만하면 요구사항 커버 됨. 더 많은 것이 필요하다면 multimethod)

### protocol definition
```
(defprotocol ProtocolName
"documentation"
(a-method [this arg1 arg2] "method docstring") 
(another-method [x] [x arg] "docstring"))
```
- privileged argument의 name은 상관없다. 위치가 규정
- protocol method는 function 정의와 다를 바 없다
- 제한사항 : 결국 JVM interface 를 생성하는 일 -> clojure의 function argument 구성의 자유를 모두 가질 수는 없다. (more 용법 등)


### Ex.
```
(defprotocol Matrix
    "Protocol for working with 2d datastructures." 
    (lookup [matrix i j])
    (update [matrix i j value])
    (rows [matrix])
    (cols [matrix])
    (dims [matrix]))
```

---

## Extending to Existing Types (p.266)
dynamic Expression Problem 의 두 번째 관심사 (기존에 존재하던 Type이 새 Protocol을 impl. 하게 함)

```
(extend-protocol Matrix clojure.lang.IPersistentVector 
(lookup [vov i j]
    (get-in vov [i j])) 
(update [vov i j value]
    (assoc-in vov [i j] value)) 
(rows [vov]
    (seq vov)) 
(cols [vov]
    (apply map vector vov)) 
(dims [vov]
    [(count vov) (count (first vov))]))
```

### 잠깐 실습

```
(def mymatrix [[1 2 3]
               [4 5 6]
               [7 8 9]])                
```

### protocol implementation (extension)
- extend-protocol
- Inline implementation
- extend
- extend-type

### extend-protocol
하나의 Protocol이 Type(들)에 확장되게 함 (extend one protocol to several types)

### extend-type
하나의 Type이 Porotocol(들)로부터 확장되게 함 (extend several protocols to one type)
```
(extend-type AType 
    AProtocol 
    (method-from-AProtocol [this x]
      (;...implementation for AType ))
    AnotherProtocol 
    (method1-from-AnotherProtocol [this x]
      (;...implementation for AType ))
    (method2-from-AnotherProtocol [this x y] 
      (;...implementation for AType ))
 )
```

### 프로그래밍 언어 별 'extention' 
- [C# Extension Methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)
- [Kotlin Extensions](https://kotlinlang.org/docs/reference/extensions.html)
- [Swift Extensions](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html)
- Python Monkey Patching <-- (질문) class의 확장인가요 instance의 확장인가요

### nil도 Protocol로부터 확장가능
- Protocol method의 NPE 방지 (<--- 매번?)

### Extending a protocol to a Java array (p.268)
- Protocol implementation이 여러 개일 때 : method 의 첫 번째 argument에 따라 해당 impementation이 선택(dispatch, choose)된다.

---

## Defining Your Own Types (p.270)
Clojure type := Java class

- defrecord (for 애플리케이션 레벨 모델 정의. 더 많이 자주 쓰게 될 것)
- deftype (for primitive data structure?)
- Types Are Not Defined in Namespaces 

### Records
- Value semantics : immutable
- Associative collection
```
(defrecord Point [x y])
(assoc (Point. 3 4) :z 5)
;= #user.Point{:x 3, :y 4, :z 5} (let [p (assoc (Point. 3 4) :z 5)]
(dissoc p :x))
;= {:y 4, :z 5}
(let [p (assoc (Point. 3 4) :z 5)]
(dissoc p :z))
;= #user.Point{:x 3, :y 4}
```
#### Metadata support ?
#### Readable representation 
```
#user.Point{:x 3, :y 4, :z 5}
```
- record literal --> makes it just as easy to use records to store and retrieve data (in a file or database or other) 

#### Auxiliary constructor
#### Constructors and factory functions
- deftype과 defrecord는 암묵적으로 '->MyType' 형태의 factory function을 정의한다
- Constructor가 public API 로 사용되기 보다는, factory function이 제공되는 것이 좋다.
```
(defrecord MyPoint [x y])

(->MyPoint 5 6

(MyPoint/create {:x 8, :y 9})
```
- 자동생성 factory function의 변경은 어떻게? 이 자동생성 factory method의 존재 의의가 뭘까?

#### When to user maps or records (p.277)
- type-based polymorphism (Protocol dispatching)
- 성능 (performance-sensitive)
- but : "records are not functions"


### Types
#### associative 하지 않은 type을 정의한다. 또, keyword를 accessor function으로 사용하는 pattern이 동작하지 않음
```
(deftype Point [x y]) ;= user.Point
(.x (Point. 3 4))
;= 3
(:x (Point. 3 4)) ;= nil
```
#### Mutable fields
- ^:volatile-mutable (java volatile)
- ^:unsynchron ized-mutable (java의 보통의 field)
- mutalbe field 는 언제나 private -> Inline method implementation으로부터만 접근 가능

```
(deftype SchrödingerCat [^:unsynchronized-mutable state] clojure.lang.IDeref
  (deref [sc]
    (locking sc 
      (or state
        (set! state (if (zero? (rand-int 2)) 
                      :dead 
                      :alive))))))
```

---

## Implementing Protocols (p.280)

### Inline Implementation

### Reusing Implementations

---
## Protocol Introspection

---
## Progocol Dispatch Edge Cases

---
## Praticipating in Clojure's Collection Abstractions

---
## Final Thoughts



