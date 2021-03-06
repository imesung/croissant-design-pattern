# Factory Method Pattern

### 학습목표
> - 팩토리 메소드 패턴에서 템플릿 메소드 패턴이 사용됨을 안다.
> - 팩토리 메소드 패턴에서 구조와 구현의 분리를 이해하고 구조와 구현의 분리의 장단점을 안다.

### 팩토리 메소드 패턴이란?
- 객체 생성 처리를 서브 클래스로 분리 해 처리하도록 캡슐화하는 패턴
- 즉, 객체의 생성 코드를 별도의 클래스/메서드로 분리함으로써 객체 생성의 변화에 대비하는 데 유용하다

### UML
<img width="412" alt="스크린샷 2020-05-06 오후 10 27 54" src="https://user-images.githubusercontent.com/38370976/81182628-027b2c00-8fe9-11ea-8245-df6723a8514e.png">

1. Product : 팩토리 메서드로 생성될 객체의 공통 인터페이스
2. DefultProduct : 구체적으로 객체가 생성되는 클래스
3. Creator : 팩토리 메서드를 갖는 클래스
4. DefultProductCreator : 팩토리 메서드를 구현하는 클래스로 DefultProductCreator 객체를 생성

### 예제코드

요구 사항
- 게임 아이템과 아이템 생성을 구현해라.
  - 아이템을 생성하기 전에 데이터베이스에서 아이템 정보를 요청한다.
  - 아이템을 생성한 후 아이템 복제 등의 불법을 방지하기 위해 데이터베이스에 아이템 생성 정보를 남긴다.
- 아이템을 생성하는 주체를 ItemCreator로 짓는다.
- 아이템은 item이라는 interface로 다룰 수 있도록 한다.
  - item은 use함수를 기본 함수로 갖고 있다.
- 현재 아이템의 종류는 체력 회복 물약, 마력 회복 물약이 있다.


```java
//팩토리 메서드로 생성될 객체의 공통 인터페이스
public interface Item {
    public void use();
}
```

```java
//팩토리 메서드를 갖는 클래스
public abstract class ItemCreator {

    // 템플릿 메소드 페턴
    public Item create() {
        Item item;
        
        requestItemInfo();
        item = createItem();
        createItemLog();

        return item;
    }

    //아이템을 생성하기 전에 데이터 베이스에서 아이템 정보를 요청
    abstract protected void requestItemInfo();
    //아이템 생성 후 아이템 복제 등의 불법을 방지하기 위해 데이터 베이스에 아이템 생성
    abstract protected void createItemLog();
    //아이템 생성 알고리즘
    abstract protected Item createItem();

}
```

```java
//구체적으로 객체가 생성되는 클래스
public class HpPotion implements Item {
    @Override
    public void use() {
        System.out.println("체력 회복");
    }
}

//팩토리 메서드를 구현하는 클래스
public class HpCreator extends ItemCreator {
    @Override
    protected void requestItemInfo() {
        System.out.println("데이터 베이스에서 체력 회복 물약의 정보 조회.");
    }

    @Override
    protected void createItemLog() {
        System.out.println("체력 회복 물약을 생성 "+new Date());
    }

    // 객체를 하위클래스에서 생성
    @Override
    protected Item createItem() {
        return new HpPotion();
    }
}

public class MpPotion implements Item {
    @Override
    public void use() {
        System.out.println("마력 회복");
    }
}

public class MpCreator extends ItemCreator {
    @Override
    protected void requestItemInfo() {
        System.out.println("데이터 베이스에서 마력 회복 물약의 정보조회.");
    }

    @Override
    protected void createItemLog() {
        System.out.println("마력 회복 물약을 생성."+new Date());
    }

    @Override
    protected Item createItem() {
        return new MpPotion();
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Item item;
        ItemCreator itemCreator;

        itemCreator = new HpCreator();
        item = itemCreator.create();
        item.use();

        itemCreator = new MpCreator();
        item = itemCreator.create();
        item.use();

    }
}
```

