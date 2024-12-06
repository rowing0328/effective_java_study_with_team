## **item2: 생성자에 매개변수가 많다면 빌더를 고려하라**

---

## **핵심 주제**

1. 기존에는 생성자 패턴을 많이 사용 및 점층적 생성자 패턴(telescoping constructor pattern)을 즐겨 사용했다.
```
package darkoverload.raccinggame;

public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }

}

```
위 코드를 보면 매개변수가 많아지면, 각각에 해당하는 코드에 대해 점층적 생성자 패턴을 작성해주어 개발자의 실수가 높을 수 있을 것으로 보인다.

2. **자바 빈즈 패턴**
```
package darkoverload.raccinggame;

public class NutritionFacts {
    private int servingSize = -1;
    private int servings = -1;
    private int calories;
    private int fat;
    private int sodium;
    private int carbohydrate;

    public NutritionFacts() {
    }

    public void setServingSize(int val) { servingSize = val; }

    public void setServings(int val) { servings = val; }

    public void setCalories(int val) { calories = val; }

    public void setFat(int val) { fat = val; }

    public void setSodium(int val) { sodium = val; }

    public void setCarbohydrate(int val) { carbohydrate = val; }

}

```

- 자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 된다. 
- 자바빈즈 패턴에서는 불변을 만들 수 없음 <= 개인적으로 이것이 가장 큰 단점이다. 요즘 나오는 언어들은 기본적으로 불변이라는 전제하에 클래스가 작성되기 때문이다. 
<br/>
추가적인 개인적인 생각 
=> set으로 호출하여 값을 수정하였을 때, 오류 히스토리를 발견하기 굉장히 어렵다. 
=> JPA를 사용할 떄, 더티 체킹일어나는 경우가 너무 많다. 그래서 대단히 조심스럽다. setter를 써야하는 부분에서 쓰는 건 좋지만 만악의 근원이다. 

3. **빌더 패턴**

```
package darkoverload;

public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        private final int servingSize;
        private final int servings;

        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

}
```
- 위에 처럼 코드를 작성하여 불변성이 적용되었고, 생성자를 통해서 값이 잘못 들어갈 일이 없어졌다.

1. 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다. 


```
package darkoverload;

import java.util.EnumSet;
import java.util.Objects;
import java.util.Set;

public abstract class Pizza {
    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        protected  abstract T self();
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }

}
```

```
package darkoverload;

import java.util.Objects;

public class NyPizza extends Pizza {
    public enum Size {SMALL, MEDIUM, LARGE}
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override
        public NyPizza build() {
            return new NyPizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    private NyPizza(Builder builder) {
        super(builder);
        this.size = builder.size;
    }

}
```

```
package darkoverload;

public class Calzone extends Pizza {
    private final boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false;

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override
        public Calzone build() {
            return new Calzone(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }
}

```

- NyPizza.Builder는 NyPizza 반환, Calzone.Builder는 Calzone을 반환
- 하위 클래스의 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌, 그 하위 타입을 반환 기능 공변 반환 타이핑
- 빌더를 이용하면 가변인수 (varargs) 매개변수를 여러 개 사용할 수 있다. 
<br/>
**개인적인 추가 생각** 
- Builder를 이용하면 코드를 몇개 더 많이 사용해야하지만 , 추상 메소드를 사용해서 계층적인 클래스에서는 많이 유연하게 사용 할 수 있는거 같아서 고려해도 될 것 같다. 
