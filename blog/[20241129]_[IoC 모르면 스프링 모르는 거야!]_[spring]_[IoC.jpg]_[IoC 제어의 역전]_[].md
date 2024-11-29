# 스프링의 핵심! IoC(제어의 역전) 컨테이너


스프링에 대해 알고 있는지 기본적인 개념을 묻고 싶다면 IoC에 대해 물으면 된다!
IoC 개념을 모르고 스프링을 사용한다? 흠.. 

* IoC 컨테이너는 객체를 생성하고, 관리하는 역할을 합니다.
* 개발자가 IoC 컨테이너에게 필요한 객체를 요청하면, 컨테이너가 해당 객체를 만들어서 제공합니다.


```
       +-----------------------+
       |    IoC Container      |
       |                       |
       |   +-------------+     |
       |   |   Object    |     |
       |   +-------------+     |
       |                       |
       |   +-------------+     |
       |   |   Object    |     |
       |   +-------------+     |
       |                       |
       |   +-------------+     |
       |   |   Object    |     |
       |   +-------------+     |
       |                       |
       +-----------------------+
```

* 이 IoC 컨테이너에 의해서 개발자가 어떤 객체를 요청하면 컨테이너에서 그 객체를 제공해주는 행위들로 제어가 개발자가 아닌 컨테이너(Spring)가 하면서 제어가 역전 되었다. 라는 것이죠. 



#### 장점

1. 객체간의 결합도를 낮춘다.
2. 코드 재사용성을 증가시킨다.
3. 개발 생산성이 향상된다. 


#### 단점

1. 초기 학습이 어렵다.
2. 런타임 오버헤드 (IoC 컨테이너가 객체를 관리하는 과정에서 약간의 런타임 오버헤드가 발생할 수 있다. 하지만 대부분의 경우 미미한 수준)


#### 결합도란?

* 모듈 또는 컴포넌트 간의 상호 의존성 정도를 나타냅니다. 
* Java의 객체들간의 관계를 의미합니다. 


#### 강결합(Tight Coupling)

* 모듈 간의 의존성이 높아, 한 모듈의 변경이 다른 모듈에 영향을 크게 미치는 상태를 말합니다. 
* 유지보수와 확장이 어렵습니다.


#### 약결합(Loose Coupling)

* 모듈 간의 의존성이 낮아, 한 모듈의 변경이 다른 모듈에 미치는 영향이 적은 상태입니다. 
* 유지보수와 확장이 용이합니다.
* `interface`, `의존성 주입(DI)`, `IoC 컨테이너` 등의 기술을 황용하여 약 결합 코드를 작성합니다.


#### 약결합의 장점

* 유지보수성이 향상됩니다.
* 확장성이 증대됩니다.
* 테스트에 용이합니다. 



### 일반 자바 코드 vs Spring IoC 컨테이너

- 일반 Java 코드에서는 객체를 직접 생성하고 관리해야 한다.
    - `new`  키워드를 이용해서 객체 생성


Java 코드:
```
public class UserService {
    private UserRepository userRepository;

    public UserService() {
        this.userRepository = new UserRepository(); // new로 객체 생성.
    }
}
```

- IoC 컨테이너는 객체의 생명주기를 컨테이너가 관리해주기 때문에 개발자는 비즈니스 로직에 더 집중할 수 있다.
    - 생성자를 통해 주입 받는다.

Java 코드:
```
@Service
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository; // new 예약어 없음.
    }
}

@Repository
public class UserRepository {
    // ...
}
```





