# chapter 1. 디자인 패턴과 프로그래밍 패러다임

- 라이브러리
    공통으로 사용될 수 있는 특정한 기능들을 모듈화한 것을 의미한다. 폴더명, 파일명 등에 대한 규칙이 없고 프레임워크에 비해 자유롭다. 예를 들어 무언가를 자를 때 '도구'인 '가위'를 사용해서 '내가' 직접 컨트롤하여 자르는데, 라이브러리는 이와 비슷하다.

- 프레임워크
    공통으로 사용될 수 있는 특정한 기능들을 모듈화 한 것을 의미한다. 폴더명, 파일명 등에 대한 규칙이 있으며 라이브러리에 비해 좀 더 엄격하다. 다른 곳으로 이동할 때 '도구'인 비행기를 타고 이동하지만 '비행기'가 컨트롤하고 나는 가만히 앉아 있어야 한다. 프레임워크는 이와 비슷하다.

## 1.1 디자인 패턴

디자인 패턴이란 프로그램을 설계할 때 발생했던 문제점들을 객체 간의 상호 관계 등을 이용하여 해결할 수 있도록 하나의 '규약' 형태로 만들어 놓은 것을 의미한다.

## 1.1.1 싱글톤 패턴

하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴이다.

보통 데이터베이스 연결 모듈에 많이 사용한다.

### 자바에서의 싱글톤 패턴

자바로는 중첩 클래스를 이용해서 만드는 방법이 가장 많이 사용된다.

``` java
class Singleton {
    private static class singleInstance {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return singleInstance.INSTANCE;
    }
}

public class TestClass {
    public static void main(String[] args) {
        Singleton a = Singleton.getInstance();
        Singleton b = Singleton.getInstance();
        // a와 b는 동일하게 된다.
    }
}
```

### mongoose와 MySQL

Node.js에서 MongoDB를 연결할 때 쓰는 mongoose 모듈과 Node.js에서 MySQL DB를 연결할 때에도 싱글톤 패턴이 사용된다.

mongoose의 DB를 연결할 때 쓰는 connect() 함수는 싱글톤 인스턴스를 반환한다.
다음은 connect() 함수 구현 때 쓰인 실제 코드이다.
``` js
Mongoose.prototype.connect = function(uri, option, callback){
  const = _mongoose = this instanceof Mongoose ? this : mongoose;
  const conn = _mongoose.connection;
  
  return _mongoose._promiseOrCallback(callback, cb => {
    conn.openUri(uri, options, err => {
      if (err != null) {
        return cb(err);
      }
      return cb(null, _mongoose);
    });
  });
};
```

다음은 Node.js에서 MySQL DB를 연결할 때의 코드이다.
``` js
// 메인 모듈에서 DB 연결에 관한 인스턴스를 정의하고,
// 다른 모듈에서 해당 인스턴스를 기반으로 쿼리를 보내는 형식으로 쓰인다.

// 메인 모듈
const mysql = require('mysql');
const pool = mysql.createPool({
  connectionLimit: 10,
  host: 'example.org',
  user: 'ys',
  password: 'secret',
  database: 'yeseulDB',
});
pool.connect();

// 모듈 A
pool.query(query, function(error, results, fields){
  if (error) throw error;
  console.log('The solution is: ', results[0].solution);
});

// 모듈 B
pool.query(query, function(error, results, fields){
  if (error) throw error;
  console.log('The solution is: ', results[0].solution);
});
```

### 싱글톤 패턴의 단점

- TDD의 걸림돌이 된다.
    단위 테스트는 테스트가 서로 독립적이어야 하며 테스트를 어떤 순서로든 실행할 수 있어야 한다.</br>
    그러나 싱글톤 패턴의 경우 테스트마다 '독립적인' 인스턴스를 만들기가 어렵다.

- 의존성 주입
    모듈 간의 결합을 강하게 만들수 있다.(의존성이 높아진다.)</br>
    이때 의존성 주입(DI, Dependancy Injection)을 통해 모듈 간의 결합을 조금 더 느슨하게 만들어 해결할 수 있다.

의존성 주입의 장점
- 모듈들을 쉽게 교체할 수 있는 구조가 되어 테스팅이나 마이그레이션하기가 수월하다.</br>
    애플리케이션의 의존성 방향이 일관되고, 애플리케이션을 쉽게 추론할 수 있으며, 모듈 간의 관계들이 조금 더 명확해진다.

의존성 주입의 단점
- 클래스 수가 늘어나기 때문에 복잡성이 증가될 수 있다.
- 약간의 실행 시간이 증가되기도 한다.

> 의존성 주입 원칙
> 상위 모듈은 하위 모듈에서 어떠한 것도 가져오지 않아야 한다. 또한 둘 다 추상화에 의존해야 하며, 이때 추상화는 세부 사항에 의존하지 말아야 한다.

