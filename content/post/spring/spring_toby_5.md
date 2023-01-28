---
title: "[토비의 스프링 3.1] 5장 정리 - 1"
date: 2023-01-14T23:28:01+09:00
draft: false
tags: ["Spring", "Backend", "Java"]
categories: ["Spring"]
showToc: true
UseHugoToc: true
comments: true
---

## 5-1. 사용자 레벨 관리 기능 추가

- 정해진 조건에 따라 사용자의 레벨을 변경하는 기능 추가

### 5.1.1 필드 추가

1) Level 이늄

- 사용자 레벨을 이늄(enum)으로 정의
    
    ```java
    public enum Level {
    	Basic(1), SILVER(2), GOLD(3);
    
    	private final int value;
    	
    	Level(int value) {
    			this.value=value;
    	}
    
    	public int intValue() {
    		return value;
    	}
    
    	public static Level valueOf(int value) {
    		switch(value) {
    			case 1: return BASIC;
    			case 2: return SILVER;
    			case 3: return GOLD;
    			default: throw new AssertionError("Unknown value: "+ value");
    		}
    	}
    }
    ```
    

2) User 필드 추가

```java
public class User {
	...
	Level level;
	int login;
	int recommend;
	
	public Level getLevel() {
		return level;
	}

	public void setLevel(Level level) {
		this.level=level;
	}
}
		
```

3) UserDaoTest 테스트 수정

4) UserDaoJdbc 수정

- 빠르게 실행 가능한 포괄적인 테스트를 만드는 것이 중요하다.

### 5.1.2 사용자 수정 기능 추가

- 사용자 관리 비즈니스 로직에 따르면 사용자 정보는 여러번 수정 될 수 있음.

1) 수정 기능 테스트 추가

- 수정할 정보가 담긴 User 오브젝트를 전달하면 id를 참고해서 사용자를 찾아 필드 정보 UPDATE해주는 메소드 추가

2) UserDao와 UserDaoJdbc 수정

- update() 메소드 추가

```java
public interface UserDao {
	...
	public void update(User user1);
}
```

- 사용자 정보 수정용 update() 메소드

```java
public void update(User user) {
this.jdbcTemplate.update(
	"update users set name=?, password=?, level=?, login=?," +
	"recommend=? where id=?", user.getName(), user.getPassword()),
	user.getLevel().intValue, user.getLogin(), user.getRecommend(),
	user.getId());
}
```

3) 수정 테스트 보완

- 위의 update() 메소드의 문제점
    - UPDATE 문에서 WHERE 절을 빼먹는 경우는 검증하지 못함.
- 해결법
    - 첫번째 방법) JdbcTemplate의 update()가 돌려주는 리턴 값(영향 주는 로우 개수)이 1인지 확인.
    - 두번째 방법) 테스트를 보강해서 원하는 사용자 외의 정보는 변경되지 않았음을 확인.
        - 사용자를 2명 등록해서 하나만 수정한 뒤에 두사람의 정보를 확인해본다.

### 5.1.3 UserService.upgradeLevels()

- 사용자 관리 로직의 경우 UserDaoJdbc에 두는 것은 적합하지 않음. (DAO는 비즈니스 로직을 두는 곳이 아니다.)
- 사용자 관리 비즈니스 로직을 담을 클래스 추가. → UserService
    - UserDao 인터페이스 타입으로 UserDao 빈을 DI 받아 사용한다.
- UserService는 UserDao의 구현 클래스가 바뀌어도 영향받지 않도록 해야함.
- UserService를 위한 테스트 클래스도 추가 → UserServiceTest

 

1) UserService 클래스와 빈 등록

- UserService클래스 만들고 사용할 UserDao 오브젝트를 저장해둘 인스턴스 변수 선언
- 스프링 설정파일에 userService 아이디로 빈 추가

```xml
<bean id="userService" class="springbook.user.service.UserService">
	<property name="userDao" ref="userDao" />
</bean>

<bean id="userDao" class="springbook.dao.UserDaoJdbc">
	<property name="dataSource" ref="dataSource" />
</bean>
```

2) UserServiceTest 테스트 클래스

- 테스트 대상인 UserService 빈을 제공받을 수 있도록 @Autowired가 붙은 인스턴스 변수로 선언.

```java
package springbook.user.service;
...
@RunWith(SpringJUnit4ClassRunner.class)
@ContextCongifuration(locations="/test-applicationContext.xml")
public class UserServiceTest {
	@Autowired
	UserService userService;
}
```

- 테스트 메소드 추가

3) upgradeLevels() 메소드

```java
public void upgradeLevels() {
	List<User> users=userdao.getAll();
	for(User user: users) {
		Boolean changes=null; //레벨 변화 확인
		//BASIC 레벨 업그레이드
		if(user.getLevel()==Level.BASIC&&user.getLogin()>=50) {
			user.setLevel(Level.SILVER);
			changed=true;
		}
		//SILVER 레벨 업그레이드
		else if(user.getLevel()==Level.SILVER&&user.getRecommend()>=30) {
			user.setLevel(Level.GOLD);
			changes=true;
		}
		//GOLD 레벨 업그레이드
		else if (user.getLevel()==Level.GOLD) {changed=false;}
		if (changed) {userDao.update(user);} //변경 있는 경우에만 update() 호출
	}
}
```

4) upgradeLevels() 테스트

- 경계가 되는 값으로 설정해 테스트를 진행한다.

### 5.1.4 UserService.add()

- 처음 가입하는 사용자의 경우 BASIC 레벨로 설정하는 것 구현해야함.
- UserDaoJdbc의 add()메소드는 적합하지 않음. 비즈니스 적인 의미를 지닌 정보를 설정하는 책임을 지는 것은 적합하지 않음.
- 방법1) User클래스에서 level 필드를 Level.BASIC으로 초기화
    - 처음 가입할 떄를 제외하면 무의미한 필드이기 때문에 문제가 있어 보임.
- 방법2) 사용자 관리에 대한 비즈니스 로직을 담고 있는 UserService에 넣기
    - UserServcie의 add()를 호출하면 레벨이 BASIC으로 설정
- 테스트 케이스
    - 1) 레벨이 미리 정해진 경우에는 레벨 초기화 불필요
    - 2) 레벨이 비어있는 경우에는 BASIC 레벨로 설정

### 5.1.5 코드 개선

- 코드에 중복된 부분은 없는가?
- 코드가 무엇을 하는 것인지 이해하기 불편하지 않은가?
- 앞으로 변경이 일어난다면 어떤 것이 있을 수 있고, 그 변화에 쉽게 대응할 수 있는가?

1) upgradeLevels() 메소드 코드의 문제점

- 문제점
    - if/elseif/else 블록들이 읽기 불편함.
    - 로직을 이해하기 쉽지 않음.
- 레벨 확인 → 각 레벨별로 조건 판단하는 조건식 넣는 로직으로 바꿔야함.

2) upgradeLevels() 리팩토링

- 기본 작업 흐름만 남겨둔 upgradeLevels()

```java
public void upgradeLevels() {
	List<User> users = userDao.getAll();
	for(User user: users) {
		if( canUpgradeLevel(user)) {
			upgradeLevel(user);
		}
	}
}
```

- canUpgradeLevel() 메소드

```java
private boolean canUpgradeLevel(User user) {
	Level currentLevel=user.getLevel();
	switch(currentLevel) {
		case BASIC: return (user.getLogin() >= 50);
		case SILVER: return (user.getRecommend()>=30);
		case GOLD: return false;
		//현재 로직에서 다룰 수 없는 레벨이라면 예외 발생 시킴. 
		default: throw new IllegalArgumentException("Unknown Level:"+currentLevel);
	}
}
```

- upgradeLevel() 메소드
    
    ```java
    private void upgradeLevel(User user) {
    	if (user.getLevel()==Level.BASIC) user.setLevel(Level.SILVER);
    	else if (user.getLevel() == Level.SILVER) usre.setLevel(Level.GOLD);
    	userDao.update(user);
    }
    ```
    
    - 다음 단계가 무엇인가 하는 로직과 그때 사용자 object의 level 필드를 변경해준다는 로직이 함께 있고, 노골적으로 들어남. 예외상황 처리도 없음.

- 업그레이드 순서를 담고 있도록 수정한 Level
    - 레벨의 순서와 다음 단계 레벨이 무엇이 있는지는 Level에 정의

```java
public enum Level {
	Basic(1, SILVER), SILVER(2, GOLD), GOLD(3, null);

	private final int value;
	private final Level next;

	Level(int value, Level next) {
		this.value=value;
		this.next=next;
	}
	...
	public Level nextLevel() {
		return this.next;
	}
	...
}
```

- User의 레벨 업그레이드 작업용 메소드

```java
public void upgradeLevel(User user) {
	Level nextlevel=this.level.nextLevel();
	if(nextlvel == null) {
		throw new IllegalStateException(this.level+"은 업그레이드가 불가능합니다.");
	}
	else {
		this.level=nextLevel;
	}
}
```

- upgradeLevel()

```java
private void upgradeLevel(User user) {
	user.upgradeLevel();
	userDao.update(user);
}
```

- 오브젝트에게 데이터를 요구하지말고 작업을 요청하라는 것이 객체지향 프로그래밍의 가장 기본이 되는 원리이기도 하다.
- UserService는 User에게 ‘레벨 업그레이드 작업’ 요청하고, 또 User는 Level에게 ‘다음 레벨이 무엇인지 알려달라’고 요청하는 방식이 바람직하다.

3) User 테스트

- upgradeLevel() 테스트는 Level 이늄을 가져와서 User에 설정해두고 User의 upgraeLevel()을 실행해서 다음 레벨로 바뀌는지 확인

4) UserServiceTest 개선

- 업그레이드 조건 (로그인 횟수, 추천 횟수)숫자가 중복돼서 나타남. → 정수형 상수로 변경한다.
    - 무슨 의도로 어떤 값을 넣었는지 이해하기 쉬워짐.

## 참고 자료

- [[book] **토비의 스프링 3.1 Vol 1: 스프링의 이해와 원리, 이일민**](https://product.kyobobook.co.kr/detail/S000000935358)