## 생성자 대신 정적 팩터리 메서드를 고려하라

- 클래스는 클라이언트 public 생성자 대신 정적 팩터리 메서드 제공 가능

### Boolean(boxed class) docs 발췌
    public static Boolean valueOf(boolean b) {
      return b ? Boolean.TRUE : Boolean.FALSE;
    }

> 디자인 패턴-Factory Method와 다름

### 장점

1. 이름을 가질 수 있음.
  - ex. new BigInteger(int, int, Random) vs BigInteger.probablePrime (어느 쪽이 더 '값이 소수인 BigInteger를 반환' 하는 의미로 와닿을까)
  - 생성자의 역할, 의도를 명확하게 알 수 있음.
2. __* 호출될 때마다 인스턴스를 새로 생성할 필요 없음__
  - 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용 -> 불필요한 객체 생성을 피함.
  - 인스턴스 통제 클래스(instance-controlled class)
3. 반환 타입의 하위 타입 객체를 반환
  - 반환 객체의 클래스를 자유롭게 선택 '유연성'
  - 구현 클래스를 공개하지 않고도 객체 반환 -> API를 작게 유지
  - 인터페이스를 정적 팩터리 메서드의 반환타입으로 사용하는 인터페이스 기반 프레임워크 (자바8 이후 인터페이스에 정적 메서드 선언 가능)
4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환
5. 정적 팩터리 메서드를 작성하는 시점에 반환할 객체의 클래스가 존재하지 않아도 됨.
  - 대표적인 프레임워크 JDBC(Java Database Connectivity)

### 단점

1. 상속을 하려면 public, protected 생성자가 필요 -> 정적 팩터리 메서드만 제공하면 하위 클래스 생성 불가
2. 생성자처럼 API 설명에 명확히 드러나지 않음. 따라서 사용자는 직접 클래스를 인스턴스화할 방법을 찾아야 함.

### 명명 방식 (규약)

- from : 매개변수를 1개 받고, 인스턴스를 반환하는 형변환 메서드

      Date d = Date.from(instant);

- of : 여러 매개변수를 받고, 인스턴스를 반환하는 집계 메서드

      Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
  
- valueOf : from, of 의 자세한 버전

      BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);

- instance/getInstance : 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지 않음.

      StackWalker luke = StackWalker.getInstance(options);

- create/newInstance : instance/getInstance와 같지만, 매번 새로운 인스턴스를 생성하여 반환

      Object newArray = Array.newInstance(classObject, arrayLen);

- getType : getInstance와 같으나, 생성한 클래스가 아닌 다른 클래스에 Factory Method 정의할 때 사용.

  > "Type" : Factory Method가 반환할 객체 타입

      BufferedReader br = Files.newBufferedReader(path);

- type : getType과 newType의 간결한 버전

      List<Complaint> litany = Collections.list(legacyLitany);

``` 핵심정리 ```

        정적 팩터리 메서드의 public 생성자는 각자의 쓰임새가 있음. 상대적인 장단점을 파악할 것
        그렇다고 하더라도 '정적 팩터리'를 사용하는 게 유리한 경우가 더 많음.
        
### 진짜 궁금한 점

팩터리 메서드를 통해 여러번 객체 생성할 때 메모리 관점에서 왜, 어떻게 이점이 있는 것일까?

1. 객체 재사용

