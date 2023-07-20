## 1.단순한 메서드 호출
싱글톤 패턴의 생성 여부를 확인하고, 인스턴스가 없는 경우에만 새로 생성하며, 이미 존재한다면 기존 인스턴스를 반환합니다. 그러나 이 코드는 메서드의 원자성이 보장되지 않아 멀티스레드 환경에서 싱글톤 인스턴스가 2개 이상 생성될 가능성이 있습니다.

### 코드
```
public class Singleton {
	private static Singleton instance;
	private Singleton() {
}
	public static Singleton getInstance() {
		if (instance == null) {
			instance = new Singleton();
		}
		return instance;
	}
}

```

## 2.동기화(synchronized)

synchronized 키워드를 사용하여 인스턴스 반환 전까지 공유 자원에 대한 접근을 제한할 수 있습니다. 최초로 접근한 스레드가 해당 메서드를 호출할 때, 다른 스레드가 접근하지 못하도록 잠금(lock)을 걸어줍니다. 
하지만 이렇게 되면 getInstance() 메서드를 호출할 때마다 lock이 걸려 성능 저하가 발생합니다. 또한, 인스턴스가 이미 생성된 상태에서도 getInstance() 호출이 가능하므로 이 부분 역시 문제가 될 수 있습니다.

### 코드
```
public class Singleton {
	private static Singleton instance;
    
	private Singleton() {
	}
	public static synchronized Singleton getInstance() {
		if (instance == null) {
		instance = new Singleton();
		}
	return instance;
	}
}

```
## 3.정적 멤버

정적(static) 멤버나 블록은 런타임이 아닌, 최초에 JVM이 클래스를 로드할 때 인스턴스를 생성할 수 있습니다.
클래스 로딩 시에 바로 싱글톤 인스턴스가 생성되므로, 다른 모듈들이 싱글톤 인스턴스를 요청할 때 이미 생성된 인스턴스를 반환할 수 있습니다.
그러나 이 방법은 불필요한 자원 낭비라는 문제가 있습니다. 싱글톤 인스턴스가 필요하지 않은 경우에도 싱글톤 클래스를 호출하면 인스턴스가 생성되기 때문입니다.

### 코드
```
public class Singleton {
	private final static Singleton instance = new Singleton();
    
	private Singleton() {
	}
	public static Singleton getInstance() {
		return instance;
	}
}

```

## 4.정적 블록
### 코드
```
public class Singleton {
	private static Singleton instance = null;
	static {
		instance = new Singleton();
	}
	private Singleton() {
	}
	public static Singleton getInstance() {
	return instance;
}

```

## 5.정적 멤버와 Lazy Holder(중첩 클래스)

singleInstanceHolder라는 내부클래스를 하나 더 만듬으로써, Singleton클래스가 최초에 로딩되더라도 함께 초기화가 되지 않고. getInstance()가 호출될 때 singleInstanceHolder 클래스가 로딩되어 인스턴스를 생성하게 됩니다.

### 코드
```
class Singleton {
	private static class singleInstanceHolder {
		private static final Singleton INSTANCE = new Singleton();
}
public static Singleton getInstance() {
	return singleInstanceHolder.INSTANCE;
	}
}

```

## 6.이중 확인 잠금(DCL)
이중 확인 잠금(DCL, Double Checked Locking)도 있습니다. 인스턴스 생성 여부를 싱글톤 패턴 잠금 전에 한번. 객체를 생성하기 전에 한 번 2번 체크하면 인스턴스가 존재하지 않을 때만 잠금을 걸 수 있기 때문에 앞서 생겼던 문제점을 해결할 수 있습니다.

### 코드
```
public class Singleton {
	private volatile Singleton instance;
	private Singleton() {
}
public Singleton getInstance() {
	if (instance == null) {
		synchronized (Singleton.class) {
			if (instance == null) {
				instance = new Singleton();
				}
			}
		}
		return instance;
	}
}
```

## 7.enum
enum의 인스턴스는 기본적으로 스레드세이프(thread safe)한 점이 보장되기 때문에 이를 통해 생성할 수 있습니다.

### 코드
```
public enum SingletonEnum {
	INSTANCE;
	public void oortCloud() {
	}
}

```
