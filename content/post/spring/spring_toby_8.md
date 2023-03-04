---
title: "[토비의 스프링 3.1] 6장 정리"
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

1) SQL 맵 XML 파일 - UserDaoJdbc에서 사용할 SQL이 담긴 XML 문서 만들기. (sqlmap.xml)

2) XML SQL 서비스 - sqlmap.xml에 있는 SQL을 가져와 DAO에 제공해주는 SqlService 인터페이스의 구현 클래스 만들기. XML 문서에서 SQL을 가져올 때는 JAXB API를 사용함. 

- 언제 JAXB를 사용해 XML을 가져올까?
    - 문제) SQL을 요청할 때마다 매번 XML 파일을 다시 읽어서 SQL찾는 것은 비효율적임.
    - 해결) 생성자에서 JAXB를 이용해 XML로 된 SQL 문서를 읽어들이고, 변환된 Sql 오브젝트들을 맵으로 옮겨서 저장해뒀다가, DAO의 요청에 따라 SQL을 찾아서 전달하는 방식으로 SqlService를 구현함. (XmlSqlService 클래스)

### 빈의 초기화 작업

- XmlSqlService 코드의 문제점
    - 생성자에서 예외가 발생할 수도 있는 복잡한 초기화 작업을 다루는 것은 좋지 않음.
    - 읽어들일 파일의 위치와 이름이 코드에 고정되어 있음.
- @PostConstruct
    - 스프링은 이 애노테이션을 빈 오브젝트의 초기화 메소드를 지정하는데 사용함.
    - 초기화 작업을 수행할 메소드에 부여하면 스프링은 XmlSqlService 클래스로 등록된 빈의 오브젝트를 생성하고 DI 작업을 마친 후에  @PostConstruct가 붙은 메소드를 자동으로 실행해줌.

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

# 참고 자료

- [[book] **토비의 스프링 3.1 Vol 1: 스프링의 이해와 원리, 이일민**](https://product.kyobobook.co.kr/detail/S000000935358)