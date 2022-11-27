---
title: "[토비의 스프링 3.1] 1.1~1.4 정리"
date: 2022-11-26T00:08:02+09:00
draft: false
tags: ["Spring", "Backend", "Java"]
categories: ["Spring"]
showToc: true
UseHugoToc: true
comments: true
---

## 스프링이란 무엇인가?
- 스프링은 자바 엔터프라이즈 애플리케이션 개발에 사용되는 애플리케이션 프레임워크다.
- 스프링을 사용한다는 것은 다음의 세 가지 요소를 적극적으로 활용해서 애플리케이션을 개발한다는 뜻이다.
- 스프링 개발 철학 중 하나는 ‘항상 프레임워크 기반의 접근 방법을 사용하라’이다.
- 프레임워크의 가장 중요한 목적은 개발자가 일정한 틀을 따라 효과적으로 애플리케이션을 개발하도록 돕는 것이다. 따라서 프레임워크를 잘 이해하려면 프레임워크를 사용했을 때 애플리케이션 코드가 어떻게 만들어지는지 자세히 살펴봐야 한다.

### 1) 애플리케이션의 기본 틀 - 스프링 컨테이너

- 스프링은 스프링 컨테이너라고 불리는 스프링 런타임 엔진을 제공한다.

### 2) 공통 프로그래밍 모델 - IoC/DI, 서비스 추상화, AOP

스프링은 세 가지 핵심 프로그래밍 모델을 지원한다. 

- IoC/DI : 오브젝트의 생명주기와 의존관계에 대한 프로그래밍 모델이다.
- 서비스 추상화 : 구체적인 기술과 환경에 종속되지 않도록 유연한 추상 계층을 두는 방법이다.
- AOP : 애플리케이션 코드에 산재해서 나타나는 부가적인 기능을 독립적으로 모듈화하는 프로그래밍 모델이다.

### 3) 기술 API

## 스프링의 성공 요인

- 단순함
    - 스프링은 이 잃어버린 객체지향 언어의 장점을 다시 개발자들이 살릴 수 있도록 도와주는 도구다.
    - 스프링이 강력하게 주장하는 것은 가장 단순한 객체지향적인 개발 모델인 POJO 프로그래밍이다.
- 유연성
    - 스프링은 다른 많은 프레임워크와 편리하게 접목돼서 사용할 수 있다.

# 1장 오브젝트와 의존관계

- 스프링이 자바에서 가장 중요하게 가치를 두는 것은 바로 객체지향 프로그래밍이 가능한 언어라는 점이다.
- 스프링이 관심을 두는 대상은 오브젝트다. 애플리케이션에서 오브젝트가 생성되고 다른 오브젝트와 관계를 맺고, 사용되고, 소멸하기까지의 전 과정을 진지하게 생각해볼 필요가 있다.
- 오브젝트에 대한 관심은 오브젝트의 설계로 발전하게 된다. 객체지향 설계의 기초와 원칙을 비롯해서, 디자인 패턴, 리팩토링, 단위테스트와 같은 오브젝트 설계와 구현에 관한 여러 가지 응용 기술과 지식이 요구된다.

## 1.2 DAO의 분리

### 1.2.1 관심사의 분리

- 개발자가 객체를 설계할 때 가장 염두에 둬야 할 사항은 바로 미래의 변화를 어떻게 대비할 것인가이다.
- 객체지향 설계와 프로그래밍 이전의 절차적 프로그래밍 패러다임에 비해 초기에 좀 더 많은, 번거로운 작업을 요구하는 이유는 객체지향 기술 자체가 지니는, 변화에 효과적으로 대처할 수 있다는 기술적인 특징 때문이다.
- 어떻게 변경이 일어날 때 필요한 작업을 최소화하고, 그 변경이 다른 곳에 문제를 일으키지 않게 할 수 있을까? 그것은 분리와 확장을 고려한 설계가 있었기 때문이다.
- 모든 변경과 발전은 한 번에 한 가지 관심사항에 집중해서 일어난다.
- 프로그래밍의 기초 개념 중에 관심사의 분리라는게 있다.
    
    → 관심이 같은 것끼리는 하나의 객체 안으로 또는 친한 객체로 모이게 하고, 관심이 다른 것은 가능한 한 따로 떨어져서 서로 영향을 주지 않도록 분리하는 것이다. 
    

### 1.2.2 커넥션 만들기의 추출

**변경사항에 대한 검증: 리팩토링과 테스트** 

- 여러 메소드에 중복돼서 등장하는 특정 관심사항이 담긴 코드를 별도의 메소드로 분리해냈다. → 리팩토링
- 공통의 기능을 담당하는 메소드로 중복된 코드를 뽑아내는 것을 리팩토링에서는 메소드 추출 기법이라고 한다.

### 1.2.3 DB 커텍션 만들기의 독립

**상속을 통한 확장**

- 템플릿 메소드 패턴 - 슈퍼클래스에 기본적인 로직의 흐름을 만들고, 그 기능의 일부를 추상 메소드나 필요에 맞게 구현해서 사용하도록 하는 방법이다.
- 팩토리 메소드 패턴 - 서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게하는 것이다.
- 디자인 패턴은 설계 전략이기도 하지만 커뮤니케이션 수단이기도 하다.

## 1.3 DAO의 확장

### 1.3.4 원칙과 패턴

**개방 폐쇄 원칙(OCP, Open-Closed Principle)**

- 개방 폐쇄 원칙은 깔끔한 설계를 위해 적용 가능한 객체지향 설계 원칙 중 하나다.
- 간단히 정의하면 ‘클래스나 모듈은 확장에는 열려 있어야 하고 변경에는 닫혀 있어야 한다.’라고 할 수 있다.

**높은 응집도와 낮은 결합도**

- 개방 폐쇄 원칙은 높은 응집도와 낮은 결합도(high coherence and low coupling)라는 소프트웨어 개발의 고전적인 원리로도 설명이 가능하다.
- 높은 응집도
    - 하나의 모듈, 클래스가 하나의 책임 또는 관심사에만 집중되어 있다.
    - 변화가 일어날 때 해당 모듈에서 변하는 부분이 크다.
- 낮은 결합도
    - 결합도란 ‘하나의 오브젝트가 변경이 일어날 때에 관계를 맺고 있는 다른 오브젝트에게 변화를 요구하는 정도’라고 할 수 있다.
    - 책임과 관심사가 다른 오브젝트 또는 모듈과는 낮은 결합도, 즉 느슨하게 연결된 형태를 유지하는 것이 바람직하다.
    - 결합도가 낮아지면 변화에 대응하는 속도가 높아지고, 구성이 깔끔해진다. 확장도 편리하다.

**전략 패턴**

- 전략 패턴은 자신의 기능 맥락에서, 필요에 따라 변경이 필요한 알고리즘을 인터페이스를 통해 통째로 외부로 분리시키고, 이를 구현한 구체적인 알고리즘 클래스를 필요에 따라 바꿔서 사용할 수 있게 하는 디자인 패턴이다.
- 스프링이란 객체지향적 설계 원칙과 디자인 패턴에 나타난 장점을 자연스럽게 개발자들이 활용할 수 있게 해주는 프레임워크다.

## 1.4 제어의 역전(IoC)

### 1.4.3 제어권의 이전을 통한 제어관계 역전

- 제어의 역적이라는 건, 간단히 프로그램의 제어 흐름 구조가 뒤바뀌는 것이라고 설명할 수 있다.
- 일반적인 프로그램의 흐름
    - main() 메소드와 같이 프로그램이 시작되는 지점에서 다음에 사용할 오브젝트 결정 → 결정한 오브젝트 생성 → 만들어진 오브젝트에 있는 메소드 호출 → 오브젝트 메소드 안에서 다음에 사용할 것을 결정하고 호출하는 식의 작업 반복
    - 모든 오브젝트가 능동적으로 자신이 사용할 클래스를 결정하고, 언제 어떻게 오브젝트를 만들지를 스스로 관장한다.
- 제어의 역전에서는 오브젝트가 자신이 사용할 오브젝트를 스스로 선택하지 않는다. 당연히 생성하지도 않는다. 모든 제어 권한을 자신이 아닌 다른 대상에게 위임한다.
- 프레임워크도 제어의 역전 개념이 적용된 대표적인 기술이다.
    - 라이브러리 vs. 프레임워크
        - 라이브러리 : 라이브러리를 사용하는 애플리케이션 코드는 애플리케이션 흐름을 제어한다.
        - 프레임워크 : 거꾸로 애플리케이션 코드가 프레임워크에 의해 사용된다.
- 스프링은 IoC를 모든 기능의 기초가 되는 기반기술로 삼고 있으며, IoC를 극한까지 적용하고 있는 프레임워크다.

# Code

- 1-1. 사용자 정보 저장용 자바빈 User 클래스

```java
package springbook1;

public class User {
	String id;
	String name;
	String password;
	
	public String getId() {
		return id;
	}
	
	public void setId(String id) {
		this.id = id;
	}
	
	public String getName() {
		return name;
	}
	
	public void setName(String name) {
		this.name = name;
	}
	
	public String getPassword() {
		return password;
	}
	
	public void setPassword(String password) {
		this.password = password;
	}

}
```

- 1-2. JDBC를 이용한 등록과 조회 기능이 있는 UserDao 클래스
    - 다음 코드의 문제점
        - add()와 get() method 부분에 DB connection 부분이 중복되어있음.
        - 예외 처리

```java
package springbook1;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class UserDao {
	public void add(User user) throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection(
				"jdbc:mysql://localhost/test", "spring", "book");
		
		PreparedStatement ps = c.prepareStatement(
				"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		ps.executeUpdate();
		
		ps.close();
		c.close();
				
	}
	
	public User get(String id) throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection(
				"jdbc:mysql://localhost/test", "spring", "book");
		
		PreparedStatement ps = c.prepareStatement(
				"select * from users where id = ?");
		ps.setString(1,id);
		
		ResultSet rs = ps.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		ps.close();
		c.close();
		
		return user;
				
	}

}
```

- 1-3. 테스트용 main() 메소드

```java
package springbook1;

import java.sql.SQLException;

public class UserDaoTest {

	public static void main(String[] args) throws ClassNotFoundException, SQLException {
		// TODO Auto-generated method stub
		UserDao dao = new UserDao();
		
		User user = new User();
		user.setId("ground");
		user.setName("");
		user.setPassword("hi2");
		
		dao.add(user);
		
		System.out.println(user.getId() + "등록 성공");
		
		User user2 = dao.get(user.getId());
		System.out.println(user2.getName());
		System.out.println(user2.getPassword());
		System.out.println(user2.getId() + "조회 성공");
	}
}
```

- [리팩토링1] 중복 코드의 메소드 추출을 통해 코드 분리
- 1-4. getConnection() 메소드를 추출해서 중복을 제거한 UserDao

```java
package springbook1;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class UserDao {
	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection c = getConnection();
		
		PreparedStatement ps = c.prepareStatement(
				"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		ps.executeUpdate();
		
		ps.close();
		c.close();
				
	}
	
	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection c = getConnection();

		PreparedStatement ps = c.prepareStatement(
				"select * from users where id = ?");
		ps.setString(1,id);
		
		ResultSet rs = ps.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		ps.close();
		c.close();
		
		return user;
				
	}
	private Connection getConnection() throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection(
				"jdbc:mysql://localhost/test", "spring", "book");
		return c; 
	}

}
```

- [리팩토링2] 상속을 통한 확장 (템플릿 메소드 패턴 & 팩토리 메소드 패턴 적용)
    - 클래스 계층구조를 통해 두 개의 관심이 독립적으로 분리되면서 변경 작업이 용이해짐.
- 1.5. 상속을 통한 확장 방법이 제공되는 UserDao
    - 다음 코드의 문제점
        - 1) 만약 이미 UserDao가 다른 목적을 위해 상속을 사용하고 있다면? 자바는 클래스의 다중상속을 허용하지 않는다.
        - 2) 상속을 통한 상하위 클래스의 관계는 생각보다 밀접하다.
        - 3) 확장된 기능인 DB 커넥션을 생성하는 코드를 다른 DAO 클래스에 적용할 수 없다.

```java
package springbook1;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public abstract class UserDao {
	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection c = getConnection();
		
		PreparedStatement ps = c.prepareStatement(
				"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		ps.executeUpdate();
		
		ps.close();
		c.close();
				
	}
	
	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection c = getConnection();

		PreparedStatement ps = c.prepareStatement(
				"select * from users where id = ?");
		ps.setString(1,id);
		
		ResultSet rs = ps.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		ps.close();
		c.close();
		
		return user;
				
	}
	// getConnection() 메소드는 Connection 타입 오브젝트를 생성한다는 기능을 정의해놓은 추상 메소드
	public abstract Connection getConnection() throws ClassNotFoundException, SQLException;

	public class NUserDao extends UserDao {
  	// 서브클래스의 getConnection() 메소드는 어떤 Connection 클래스의 오브젝트를 어떻게 생성할 것인지 결정하는 방법
		public Connection getConnection() throws ClassNotFoundException, SQLException {
			//N사 DB connection code	
		return c;
			
		}
	}
	
	public class DUserDao extends UserDao {
		public Connection getConnection() throws ClassNotFoundException, SQLException {
			//D사 DB connection code	
		return c;
			
		}
	}

}
```

- [리팩토링3] 클래스의 분리
- 1.6 독립된 SimpleConnectionMaker를 사용하게 만든 UserDao
- 1.7 독립시킨 DB연결 기능인 SimpleConnectionMaker
- 다음 코드의 문제점
    - 고객이 DB 커넥션을 가져오는 방법을 자유롭게 확장하기가 어려움.                                                 → UserDao가 SimpleConnectionMaker라는 특정 클래스와 그 코드에 종속적임.

```java
package springbook1;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public abstract class UserDao {
	private SimpleConnectionMaker simpleConnectionMaker;
	
	public UserDao() {
		simpleConnectionMaker = new SimpleConnectionMaker();
	}
	
	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection c = simpleConnectionMaker.makeNewConnection();
		
		PreparedStatement ps = c.prepareStatement(
				"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		ps.executeUpdate();
		
		ps.close();
		c.close();
				
	}
	
	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection c = simpleConnectionMaker.makeNewConnection();

		PreparedStatement ps = c.prepareStatement(
				"select * from users where id = ?");
		ps.setString(1,id);
		
		ResultSet rs = ps.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		ps.close();
		c.close();
		
		return user;
				
	}

	public class SimpleConnectionMaker {
		public Connection makeNewConnection() throws ClassNotFoundException,
		SQLException {
			Class.forName("com.mysql.jdbc.Driver");
			Connection c = DriverManager.getConnection(
					"jdbc:mysql://localhost/test", "spring", "book");
			return c; 
		}
}	
}
```

- [리팩토링4] 인터페이스 도입
- 1-8. ConnectionMaker 인터페이스
- 1-9. ConnectionMaker 구현 클래스
- 1-10. ConnectionMaker 인터페이스를 사용하도록 개선한 UserDao
- 다음 코드의 문제점
    - UserDao의 다른 모든 곳에서는 인터페이스를 이용하게 만들어서 DB 커넥션을 제공하는 클래스에 대한 구체적인 정보는 모두 제거가 가능했지만, 초기에 한 번 어떤 클래스의 오브젝트를 생성할지를 결정하는 생성자의 코드는 제거 되지 않음.

```java
package springbook1;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public abstract class UserDao {
	private ConnectionMaker connectionMaker;
	
	public UserDao() {
		connectionMaker = new DConnectionMaker();
	}
	
	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection c = connectionMaker.makeConnection();
		
		PreparedStatement ps = c.prepareStatement(
				"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		ps.executeUpdate();
		
		ps.close();
		c.close();
				
	}
	
	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection c = connectionMaker.makeConnection();

		PreparedStatement ps = c.prepareStatement(
				"select * from users where id = ?");
		ps.setString(1,id);
		
		ResultSet rs = ps.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		ps.close();
		c.close();
		
		return user;
				
	}	
	
	public interface ConnectionMaker {
		public Connection makeConnection() throws ClassNotFoundException,SQLException;
	}
	
	public class DConnectionMaker implements ConnectionMaker {
		public Connection makeConnection() throws ClassNotFoundException, SQLException {
			//D사 connection code
			return null;
		}
	}
}
```

- [리팩토링5] 관계설정 책임의 분리
    - 인터페이스를 도입하고 클라이언트의 도움을 얻는 방법은 상속을 사용해 비슷한 시도를 했을 경우에 비해서 유연하다.
- 1-11. 수정한 생성자
- 1-12. 관계 설정 책임이 추가된 UserDao 클라이언트인 main() 메소드
- 다음 코드의 문제점
    - UserDaoTest는 기존에 UserDao가 담당하던 기능, 즉 어떤 ConnectionMaker 구현 클래스를 사용할지를 결정하는 기능을 떠맡았다.
        
        → 이렇게 분리될 기능은 UserDao와 ConnectionMaker 구현 클래스의 오브젝트를 만드는 것과, 그렇게 만들어진 두 개의 오브젝트가 연결돼서 사용될 수 있도록 관계를 맺어주는 것이다. 
        

```java
package springbook1;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class UserDao {
	private ConnectionMaker connectionMaker;
	
	public UserDao(ConnectionMaker connectionMaker) {
		this.connectionMaker = connectionMaker;
	}
	
	public void add(User user) throws ClassNotFoundException, SQLException {
		Connection c = connectionMaker.makeConnection();
		
		PreparedStatement ps = c.prepareStatement(
				"insert into users(id, name, password) values(?,?,?)");
		ps.setString(1, user.getId());
		ps.setString(2, user.getName());
		ps.setString(3, user.getPassword());
		
		ps.executeUpdate();
		
		ps.close();
		c.close();
				
	}
	
	public User get(String id) throws ClassNotFoundException, SQLException {
		Connection c = connectionMaker.makeConnection();

		PreparedStatement ps = c.prepareStatement(
				"select * from users where id = ?");
		ps.setString(1,id);
		
		ResultSet rs = ps.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		ps.close();
		c.close();
		
		return user;
				
	}	
}
```

```java
package springbook1;

import java.sql.SQLException;

public class UserDaoTest {
	public static void main(String[] args) throws ClassNotFoundException, SQLException {
		// TODO Auto-generated method stub
		ConnectionMaker connectionMaker = new DConnectionMaker();
		
		UserDao dao = new UserDao(connectionMaker);
		
		User user = new User();
		user.setId("ocean");
		user.setName("zz");
		user.setPassword("hi3");
		
		dao.add(user);
		
		System.out.println(user.getId() + "등록 성공");
		
		User user2 = dao.get(user.getId());
		System.out.println(user2.getName());
		System.out.println(user2.getPassword());
		System.out.println(user2.getId() + "조회 성공");
	}
}
```

- [리팩토링6] 오브젝트 팩토리
    - 팩토리 클래스의 역할은 객체의 생성 방법을 정하고 그렇게 만들어진 오브젝트를 돌려주는 것이다.
- 1-14. UserDao의 생성 책임을 맡은 팩토리 클래스
- 1-15. 팩토리를 사용하도록 수정한 UserDaoTest
- 1-16. DAO 생성 메소드의 추가로 인해 발생하는 중복
- 1-17. 생성 오브젝트 코드 수정

```java
package springbook1;

public class DaoFactory {
	public UserDao userDao() {
		return new UserDao(connectionMaker());
	}
	public AccountDao accountDao() {
		return new AccountDao(connectionMaker());
	}
	public MessageDao messageDao() {
		return new MessageDao(connectionMaker());
	}
	public ConnectionMaker connectionMaker() {
		return new DConnectionMaker();
	}
}
```

```java
package springbook1;

import java.sql.SQLException;

public class UserDaoTest {
	public static void main(String[] args) throws ClassNotFoundException, SQLException {
		// TODO Auto-generated method stub		
		UserDao dao = new DaoFactory().userDao();
		
		User user = new User();
		user.setId("ocean");
		user.setName("zz");
		user.setPassword("hi3");
		
		dao.add(user);
		
		System.out.println(user.getId() + "등록 성공");
		
		User user2 = dao.get(user.getId());
		System.out.println(user2.getName());
		System.out.println(user2.getPassword());
		System.out.println(user2.getId() + "조회 성공");
	}
}
```

# 용어
- Application Framework : 애플리케이션 개발을 빠르고 효율적으로 할 수 있도록 애프리케이션의 바탕이 되는 틀과 공통 프로그래밍 모델, 기술 API 등을 제공해준다.
- AOP : 애플리케이션 코드에 산재해서 나타나는 부가적인 기능을 독립적으로 모듈화하는 프로그래밍 모델이다.
- DAO (Data Access Object) : DB를 사용해 데이터를 조회하거나 조작하는 기능을 전담하도록 만든 오브젝트다.
- IoC/DI : 오브젝트의 생명주기와 의존관계에 대한 프로그래밍 모델이다.
- Refactoring : 기존의 코드를 외부의 동작방식에는 변화 없이 내부 구조를 변경해서 재구성하는 작업 또는 기술을 말한다.
- Service Abstraction : 구체적인 기술과 환경에 종속되지 않도록 유연한 추상 계층을 두는 방법이다.
- Framework : 애플리케이션을 구성하는 오브젝트가 생성되고 동작하는 방식에 대한 틀을 제공한다. 애플리케이션 코드가 어떻게 작성돼야 하는지에 대한 기준도 제시한다. 이런 틀을 프로그래밍 모델이라고 한다.
- SOLID : 객체지향 설계 원칙
    - SRP(The Single Responsibility Principle) - 단일 책임 원칙
    - OCP(The Open Closed Principle) - 개방 폐쇄 원칙
    - LSP(The Liskov Substitution Principle) - 리스코프 치환 원칙
    - ISP(The Interface Segregation Principle) - 인터페이스 분리 원칙
    - DIP(The Dependency Inversion Principle) - 의존관계 역전 원칙

## 책 속 자료

- 디자인 패턴
    - GoF의 디자인 패턴, 에릭 감마 외
    - Head First Design Patterns, 에릭 프리먼
- 객체지향 설계 원칙(SOLID)
    - Java 프로그래머를 위한 UML 실전에서는 이것만 쓴다 (UML for Java Programmer), 밥 마틴
    - 소프트웨어 개발의 지혜: 원칙, 디자인 패턴, 실천 방법 (Agile Software Development, Principles, Patterns, and Practices), 밥 마틴
    - [SOLID 소개, 로버트 마틴](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod)

## 참고 자료

- [[book] **토비의 스프링 3.1 Vol 1: 스프링의 이해와 원리, 이일민**](https://product.kyobobook.co.kr/detail/S000000935358)