---
title: "[토비의 스프링 3.1] 7장 정리"
date: 2023-03-05T03:12:21+09:00
draft: false
tags: ["Spring", "Backend", "Java"]
categories: ["(book)토비의 스프링3.1"]
showToc: true
UseHugoToc: true
comments: true
---

- 스프링의 3대 핵심 기술인 IoC/DI, 서비스 추상화, AOP를 애플리케이션 개발에 활용해보자.

## SQL과 DAO의 분리

- SQL을 적절히 분리해 DAO 코드와 다른 파일이나 위치에 두고 관리하자
- 분리 방법
  - XML 설정을 이용한 분리 - SQL을 스프링의 XML 설정파일로 빼기
  - SQL 맵 프로퍼티 방식 - SQL을 하나의 컬렉션으로 담아두기. 맵의 키 값을 이용해 SQL 문장을 가져오게함.
- 스프링의 설정파일 안에 SQL을 두면 SQL과 DI 설정정보가 섞여 관리하기 어렵다는 문제가 있음.
  - 해결) 독립적인 SQL 제공 서비스가 필요함.

### SQL 서비스 인터페이스

- SQL 서비스의 인터페이스를 설계함. (SqlService)
  - SQL에 대한 키 값을 전달하면 그에 해당하는 SQL 돌려주는 기능
- code (SqlService 인터페이스)

```java
package springbook.user.sqlservice;

public interface SqlService {
	string getSql(String key) throws SqlRetrievalFailureException;
}
```

- UserDaoJdbc는 SqlService 인터페이스를 통해 필요한 SQL을 사용할 수 있도록 함. SqlService 타입의 빈을 DI 받을 수 있도록 프로퍼티를 정의함.
- code (SqlService 프로퍼티 추가)

```java
public class UserDaoJdbc implements UserDao {
	...
	private SqlService sqlService;

	public void setSqlService(SqlService sqlService) {
		this.sqlService = sqlService;
	}
}
```

### 스프링 설정을 사용하는 단순 SQL 서비스

- SqlService 인터페이스를 구현하는 클래스를 만들고 Map 타입 프로퍼티를 추가함.
- 맵에서 SQL을 읽어서 돌려주도록 SqlService의 getSql() 메소드 구현하기
- code (맵을 이용한 SqlService의 구현)

```java
package springbook.user.sqlservice;
...
public class SimpleSqlService implements SqlService {
	private Map<String, String> sqlMap;

	public void setSqlMap(Map<String, String> sqlMap) {
		this.sqlMap = sqlMap;
	}

	public String getSql(String key) throws SqlRetrievalFailureException {
		String sql = sqlMap.get(key);
		if (sql == null)
			throw new SqlRetrievalFailureException;
		else
			return sql;
	}
}
```

- SimpleSqlService 클래스를 빈으로 등록하고 UserDao가 DI 받아 사용하도록 설정함.

## 인터페이스의 분리와 자기참조 빈

- 이제 SqlService 인터페이스의 구현 방법을 고민하자.
- JAXB(Java Architecture for XML Binding)
  - XML에 담긴 정보를 파일에서 읽어오는 방법.
  - XML 정보를 오브젝트처럼 다룰 수 있게 함.
  - XML 문서의 구조를 정의한 스키마를 이용해서 매핑할 오브젝트의 클래스를 자동으로 만들어주는 컴파일러를 제공함.
    - XML 파일 매핑
      - 검색용 키와 SQL 문장을 담을 수 있는 XML 문서를 설계한다.
      - code
        ```xml
        <sqlmap>
        	<sql key="userAdd">insert into users(...) ...</sql>
        	...
        </sqlmap>
        ```
    - XML 스키마 파일을 만들어서 sqlmap.xsd라는 이름으로 프로젝트 루트에 저장하고, JAXB 컴파일러로 컴파일을 함.
  - 언마샬링(unmarshalling) - XML 문서를 읽어서 자바의 오브젝트로 변환
  - 마샬링(marshalling) - 바인딩 오브젝트를 XML 문서로 변환

### XML 파일을 이용하는 SQL 서비스

1. SQL 맵 XML 파일 - UserDaoJdbc에서 사용할 SQL이 담긴 XML 문서 만들기. (sqlmap.xml)

2. XML SQL 서비스 - sqlmap.xml에 있는 SQL을 가져와 DAO에 제공해주는 SqlService 인터페이스의 구현 클래스 만들기. XML 문서에서 SQL을 가져올 때는 JAXB API를 사용함.

- 언제 JAXB를 사용해 XML을 가져올까?
  - 문제) SQL을 요청할 때마다 매번 XML 파일을 다시 읽어서 SQL찾는 것은 비효율적임.
  - 해결) 생성자에서 JAXB를 이용해 XML로 된 SQL 문서를 읽어들이고, 변환된 Sql 오브젝트들을 맵으로 옮겨서 저장해뒀다가, DAO의 요청에 따라 SQL을 찾아서 전달하는 방식으로 SqlService를 구현함. (XmlSqlService 클래스)

### 빈의 초기화 작업

- XmlSqlService 코드의 문제점
  - 생성자에서 예외가 발생할 수도 있는 복잡한 초기화 작업을 다루는 것은 좋지 않음.
  - 읽어들일 파일의 위치와 이름이 코드에 고정되어 있음.
- @PostConstruct
  - 스프링은 이 애노테이션을 빈 오브젝트의 초기화 메소드를 지정하는데 사용함.
  - 초기화 작업을 수행할 메소드에 부여하면 스프링은 XmlSqlService 클래스로 등록된 빈의 오브젝트를 생성하고 DI 작업을 마친 후에 @PostConstruct가 붙은 메소드를 자동으로 실행해줌.

### 변화를 위한 준비: 인터페이스 분리

- XML 대신 다른 포맷의 파일에서 SQL을 읽어오게 하려면 어떻게 해야할까?
- 책임에 따른 인터페이스 정의
  - 분리 가능한 관심사를 구분해보자.
    - 첫번째 책임) SQL 정보를 외부의 리소스로부터 읽어오기
    - 두번째 책임) 읽어온 SQL을 보관해두고 있다가 필요할 때 제공
- Sqlservice를 구현해서 DAO에 서비스를 제공해주는 오브젝트가 이 두 가지 책임을 가진 SqlReader, SqlRegistry 오브젝트와 협력해서 동작하도록 만든다.
- SQL을 SqlReader에서 SqlRegistry로 전달하는 과정과 전달되는 형식을 어떻게 해야할까?
  - SqlReader에게 SqlRegistry 전략을 제공해주면서 이를 이용해 SQL 정보를 SqlRegistry에 저장하라고 요청하자.
- SqlRegistry 인터페이스
  - code
    ```java
    package springbook.user.sqlservice;
    ...
    public interface sqlRegistry {
    	//1) SQL 등록
    	void registerSql(String key, String sql);
    	//2) SQL 검색
    	String findSql(String key) throws SqlNotFoundException;
    }
    ```
- SqlReader 인터페이스
  - SqlRegistry 오브젝트를 메소드 파라미터로 DI 받아서 읽어들인 SQL을 등록하는 데 사용하도록 만들어야 함.
  - code
    ```java
    public interface SqlReader {
    	void read(SqlRegistry sqlRegistry);
    }
    ```

### 자기참조 빈으로 시작하기

- 다중 인터페이스 구현과 간접 참조
  - XmlSqlService 클래스 하나가 SqlService, SqlRegistry, SqlReader 인터페이스를 구현하게 만든다.
- 인터페이스를 이용한 분리
  - 같은 클래스의 코드이지만 책임이 다른 코드는 직접 접근하지 않고 인터페이스를 통해 간접적으로 사용하는 코드로 변경함.
- 자기참조 빈 설정
  - 자기참조 빈은 책임과 관심사가 복잡하게 얽혀 있어서 확장히 힘들고 변경에 취약한 구조의 클래스를 유연한 구조로 만들려고 할 때 시도할 수 있는 방법임.

### 디폴트 의존관계

- 확장 가능한 기반 클래스
  - SqlRegistry와 SqlReader를 이용하는 SqlService 구현 클래스 만들기. (BaseSqlService)
- 디폴트 의존관계를 갖는 빈 만들기
  - 특정 의존 오브젝트가 대부분의 환경에서 거의 디폴트라고 해도 좋을만큼 기본적으로 사용될 가능성이 있다면, 디폴트 의존관계를 만드는 것을 고려해볼 필요가 있음.
  - 디폴트 의존관계 - 외부에서 DI 받지 않는 경우 기본적으로 자동 적용되는 의존관계임.

## 서비스 추상화 적용

### OXM 서비스 추상화

- OXM(Object-XML Mapping) : XML과 자바오브젝트를 매핑해서 상호 변환해주는 기술
- OXM 서비스 인터페이스
  - 스프링이 제공하는 OXM 추상화 서비스 인터페이스
    - Jaxb2Marshaller: Unmarshaller 인터페이스와 Marshaller 인터페이스를 구현한 클래스
    - Castor : 여러 가지 XML/오브젝트 변환 방법 지원함.
- OXM 서비스 추상화 적용 (OxmSqlService)
  - 스프링의 OXM 추상화 기능을 이용하는 OxmSqlService
  - sqlRegistry는 DI 받을 수 있게 만들지만 SqlReader는 스프링의 OXM 언마샬러를 이용하도록 OxmSqlService 내에 고정시킴. → SQL을 읽는 방법을 OXM으로 제한해서 사용성을 극대화하는게 목적임.

### 리소스 추상화

- SQL 매핑 정보가 담긴 XML 파일 이름은 프로퍼티로 외부에서 지정할 수는 있지만 UserDao 클래스와 같은 클래스패스에 존재하는 파일로 제한됨. → 리소스에 접근하는 통일된 방법이 필요함.
- 스프링은 자바에서 존재하는 일관성 없는 리소스 접근 API를 추상화해서 Resource라는 추상화 인터페이스를 정의함.
- 리소스 로더 : 스프링에는 URL 클래스와 유사하게 접두어를 이용해 Resource 오브젝트를 선언하는 방법이 있음. 문자열 안에 리소스의 종류와 리소스의 위치를 함께 표현하게 해주는 것임.
  - ResourceLoader: 문자열로 정의된 리소스를 실제 Resource 타입 오브젝트로 변환해주는 인터페이스
    - 애플리케이션 컨텍스트가 구현해야 하는 인터페이스인 ApplicationContext는 ResourceLoader인터페이스를 상속함.
- 스프링 애플리케이션에서 파일을 읽거나 참조하는 기능을 만들 때는 Resource 타입의 추상화 기능을 사용하자.

## 인터페이스 상속을 통한 안전한 기능확장

- 기존에 설계하고 개발했던 기능이 발전돼야 할 경우에 스프링답게 접근하는 방법은?

### DI와 기능의 확장

- DI는 런타임 시에 의존 오브젝트를 다이내믹하게 연결해줘서 유연한 확장을 꾀하는 게 목적이기 때문에 항상 확장을 염두에 두고 오브젝트 사이의 관계를 생각해야함.

### DI와 인터페이스 프로그래밍

- DI를 적용할 때는 가능한 한 인터페이스를 사용해야 함.
- 인터페이스를 사용하는 이유
  - 다형성을 얻기 위함. 하나의 인터페이스를 통해 여러 개의 구현을 바꿔가면서 사용할 수 있게 하는 것이 DI가 추구하는 첫번째 목적임.
  - 인터페이스 분리 원칙을 통해 클라이언트와 의존 오브젝트 사이의 관계를 명확하게 해줌.
- 인터페이스 분리 원칙 - 오브젝트가 그 자체로 충분히 응집도가 높은 작은 단위로 설계됐더라도, 목적과 관ㅅ미이 각기 다른 클라이언트가 있다면 인터페이스를 통해 적절하게 분리해줄 필요가 있음.
- 인터페이스 상속
  - 인터페이스 분리 원칙이 주는 장점은 모든 클라이언트가 자신의 관심에 따른 접근 방식을 불필요한 간섭 없이 유지할 수 있다는 점임.
  - 새롭게 추가할 기능을 사용하는 클라이언트를 위해 새로운 인터페이스를 정의하거나 기존 인터페이스를 확장하는게 바람직함.
- 잘 적용된 DI는 잘 설계된 오브젝트 의존관계에 달려 있음. 인터페이스를 적절하게 분리하고 확장해 오브젝트 사이의 의존관계를 명확하게 해주고, 유연한 확장성을 얻는 방법이 무엇인지 고민해야함.

## DI를 이용해 다양한 구현 방법 적용

- 운영 중인 시스템에서 사용하는 정보를 실시간으로 변경하는 작업을 만들 때 고려해야할 점은 동시성 문제임.

### ConcurrentHashMap을 이용한 수정가능한 SQL 레지스트리

- HashMap은 멀티스레드 환경에서 동시에 수정하는 작업을 수행하면 예상치 못한 결과가 발생 가능함.
  → 동기화된 해시 데이터 조작에 최적화되도록 만들어진 ConcurrentHashMap을 사용하는 방법이 권장됨.

### 내장형 데이터베이스를 이용한 SQL 레지스트리

- 내장형 DB를 이용해 SQL을 저장하고 수정할 수 있음.
- 내장형 DB : 애플리케이션에 내장돼서 애플리케이션과 함께 시작되고 종료되는 DB임.
- 스프링은 내장형 DB를 초기화하는 작업을 지원하는 편리한 내장형 DB 빌더를 제공함.
  - 애플리케이션을 통해 DB가 시작될 때마다 매번 테이블을 새롭게 생성함.
    → 테이블 생성 SQL 스크립트 필요함.
- EmbeddedDatabaseBuilder : 스프링이 제공하는 내장형 DB 빌더
  - EmbeddedDatabaseBuilder가 최종적으로 만들어주는 오브젝트는 DataSource 인터페이스를 상속한 EmbeddedDatabase 타입임.

## 스프링 3.1의 DI

### 자바 언어의 변화와 스프링

- 애노테이션의 메타정보 활용
  - 자바 코드의 일부를 리플렉션 API 등을 이용해 어떻게 만들었는지 살펴보고 그에 따라 동작하는 기능이 점점 많이 사용되고 있다.
  - 애노테이션을 이용하는 표준 기술과 프레임워크가 증가함. 애노테이션은 프레임워크가 참조하는 메타정보로 사용되기에 여러가지 유리한 점이 많다.
- 정책과 관례를 활용한 프로그래밍
  - 애노테이션 같은 메타정보를 활용하는 프로그래밍 방식은 미리 약속한 규칙 또는 관례를 따라서 프로그램이 동작하도록 만드는 프로그래밍 스타일을 적극적으로 포용하게 만들었음.

### 자바 코드를 이용한 빈 설정

- 애노테이션과 자바 코드로 XML을 대체하는 작업을 진행한다.
- 1. 테스트 컨텍스트의 변경 - 테스트 코드에서 DI 정보가 XML에 담겨 있다고 정의한 부분을 찾아 DI 정보를 담은 자바 코드를 사용하도록 바꾼다.
  - 자바 클래스로 만들어진 DI 설정정보에서 XML의 설정 정보를 가져오게 만든다. → @ImportResource 애노테이션 이용
- 2. <context:annotation-config/> 제거 - @Configuration이 붙은 자바 클래스를 DI 정보로 사용한다.
- 3. <bean>의 전환 - <bean>을 이용해서 작성한 빈 정보를 자바코드로 전환
  - XML에서 자바 클래스를 동시에 DI 정보로 사용하는 경우 자바 코드로 정의한 datasource 빈은 XML에서 <property>를 이용해 참조할 수 있음. 반대로 자바 코드에서는 XML에서 정의한 빈을 참조하려면 어떻게 해야할까?
    - 클래스에 @Autowired가 붙은 필드를 선언해서 XML에 정의된 빈을 컨테이너가 주입해주게 해야함.
- 4. 전용 태그 전환
  - 스프링3.1은 XML에서 자주 사용되는 전용 태그를 @Enable로 시작하는 애노테이션으로 대체가능하게 하는 애노테이션 제공함.
    - @EnableTransactionManagement

## 빈 스캐닝과 자동와이어링

### @Autowired를 이용한 자동와이어링

- @Autowired는 자동와이어링 기법을 이용해서 조건에 맞는 빈을 찾아 자동으로 수정자 메소드나 필드에 넣어줌.
  - 자동와이어링을 이용하면 빈의 프로퍼티 설정을 직접해주는 자바 코드나 XML을 줄일 수 있음.
  - 스프링은 @Autowired가 붙은 수정자 메소드가 있으면 파라미터 타입을 보고 주입 가능한 타입의 빈을 모두 찾음.
  - 주입 가능한 타입의 빈이 하나라면 스프링이 수정자 메소드를 호출해서 넣어줌.
  - 두 개 이상이라면 프로퍼티와 동일한 이름의 빈이 있는지 찾음.
  - 빈 설정정보를 보고 다른 빈과 의존관계가 어떤지에 대해서 파악하기 어렵다는 단점도 있음.

### @Component를 이용한 자동 빈 등록

- @Component가 붙은 클래스는 빈 스캐너를 통해 자동으로 빈으로 등록됨.
- @Component는 빈으로 등록될 후보 클래스에 붙여주는 일종의 마커임.
- @Component 애노테이션이 달린 클래스를 자동으로 찾아서 빈을 등록해주게 하려면 빈 스캔 기능을 사용하겠다는 애노테이션 정의가 필요함. → @ComponentScan
  - @ComponentScan의 basePackages 엘리먼트는 @Component가 붙은 클래스를 스캔할 기준 패키지를 지정할 때 사용함.
- 그 외의 스프링의 자동 빈 등록 기능
  - 애노테이션 포인트컷 (@Transactional)
- 메타 애노테이션 - 여러 개의 애노테이션에 공통적인 속성을 부여할 때 사용함.

## 컨텍스트 분리와 @Import

- 성격이 다른 DI 정보를 분리해보자.

### 테스트용 컨텍스트 분리

- DI 설정 클래스를 추가하고 관련된 빈 설정 애노테이션, 필드, 메소드를 옮겨 설정정보를 분리하자.

### @Import

- SQL 서비스와 관련된 빈들을 분리해 독립적인 모듈로 취급하자.
- SQL 서비스와 관련된 빈 설정은 애플리케이션이 동작할 때 항상 필요한 정보임. 파일을 구분하더라도 애플리케이션 설정 정보의 중심이 되는 AppContext와 긴밀하게 연결하는게 좋음.
- @Import - 자바 클래스로된 설정정보를 가져올 때 사용함.

## 프로파일

- 테스트환경과 운영환경에서 각기 다른 빈 정의가 필요한 경우가 종종 있음.
  - 운영환경에서는 반드시 필요하지만 테스트 실행 중에는 배제돼야 하는 빈 설정을 별도의 설정 클래스로 만들어 관리할 필요가 있음.

### @Profile과 @ActiveProfiles

- 스프링 3.1은 환경에 따라서 빈 설정정보가 달라지는 경우 간단히 설정정보를 구성할 수 있는 방법을 제공함.
  → 실행 환경에 따라 빈 구성이 달라지는 내용을 프로파일로 정의해서 만들어두고, 실행 시점에 어떤 프로파일의 빈 설정을 사용할지 지정하는 것임.
- 프로파일은 간단한 이름과 빈 설정으로 구성됨.
- 프로파일은 설정 클래스 단위로 지정함.
- @Profile 애노테이션을 클래스 레벨에 부여하고 프로파일 이름을 넣어주면 됨.
- 프로파일을 적용하면 모든 설정 클래스를 부담 없이 메인 설정 클래스에서 @Import해도 된다는 장점이 있음.

### 중첩 클래스를 이용한 프로파일 적용

- 프로파일에 따라 분리했던 설정정보를 하나의 파일로 모아보자.
  - 프로파일이 지정된 독립된 설정 클래스의 구조는 그대로 유지한 채로 소스코드의 위치만 통합해보자.
    → 스태틱 중첩 클래스 사용

## 프로퍼티 소스

- DB 연결정보는 환경에 따라 다르게 설정되어야 함. 같은 종류의 환경이더라도 필요에 따라 손쉽게 변경가능하면 좋음.
  - 외부 서비스 연결에 필요한 정보는 자바 클래스에서 제거하고 편집할 수 있고 빌드 작업이 필요없는 XML이나 프로퍼티 파일 같은 텍스트 파일에 저장하는게 좋음.
- 프로퍼티의 확장자는 properties임.
- 키=값 형태로 프로퍼티를 정의함.
- 프로퍼티 소스 - 컨테이너가 프로퍼티 값을 가져오는 대상
  - 프로퍼티 소스 등록에는 @PropertySource 애노테이션을 이용함.

### PropertySourcesPlaceholderConfigurer

- @Value - 값을 주입받을 때 사용함.
- @Value를 이용하면 driverClass처럼 문자열을 그대로 사용하지 않고 타입 변환이 필요한 프로퍼티를 스프링이 알아서 처리해준다는 장점이 있음.

## 빈 설정의 재사용과 @Enable

- SQL 서비스 빈은 인터페이스에 노출하면 되고 나머지 구현 기술이나 방법은 내부에 감춰두고 필요에 따라 자유롭게 변경할 수 있어야함.
- SQL 서비스 관련 빈 설정을 독립적인 자바 클래스로 만들어두면 빈 설정정보도 라이브러리에 함께 패키징해서 제공할 수 있게 됨. → @Import로 서비스 관련 빈 등록 가능함.
- SqlMapConfig을 구현해 SQL 매핑 파일의 위치 지정

### @Enable 애노테이션

- 스프링3.1은 모듈화된 빈 설정을 가져올 때 사용하는 @Import를 다른 애노테이션으로 대체하는 방법을 제공함.
- @Component는 빈 자동등록 대상을 지정할 때 사용함.
  - @Component를 직접 사용하기보다는 @Repository나 @Service처럼 좀 더 의미 있는 이름의 애노테이션을 만들어 사용함.
  - 자동등록되는 빈의 종류나 계층이 무엇인지 나타낼 수 있음.
  - AOP를 이용해 특정 애노테이션이 달린 빈만 선정해 부가 기능을 제공하게 만들 수도 있음.

# 참고 자료

- [[book] **토비의 스프링 3.1 Vol 1: 스프링의 이해와 원리, 이일민**](https://product.kyobobook.co.kr/detail/S000000935358)
