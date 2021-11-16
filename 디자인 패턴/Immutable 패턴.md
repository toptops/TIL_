# Immutable 패턴

Immutable이란?
- Immutable 불변이란 뜻을 가지고 있다. Immutable 패턴 또한 간단히 불변한는 패턴이라고 보면 될거 같다.
즉 final, 한번 값이 정해지면 변경되지 않는 다는 점이다.
- 따라서, pojo객체로 예로 들면 내부적으로 setter메소드를 사용 못한다는 의미이며, 생성할때 딱한번 초기화되며 바뀌지 않는다.
- 보통 final 키워드를 사용하여 만든다.
    ```java
        // Pojo 객체
        public class Pojo {
            private final String immutable;
            private String mutable;

            // immutable 변수 초기화 방법1
            static {
                this.immutable = "immutable";
            }

            // immutable 변수 초기화 방법2
            public Pojo(String immutable) {
                this.immutable = immutable;
            }

            // 변경 불가X
            public void setImmutable(String change) {
                this.immutable = change; // final 메소드로 되어있기 때문에 변경이 불가능하다.
            }

        }
    ```
- java에서 wrapper객체인 String, Integer 등이 이에 해당한다.


Immutable/Mutable 비교 예)
- mutable은 불변의 반대로 변경이 가능하다는 의미.(일반적인 변수 사용)
- String와 StringBuilder를 예를 들수있다.(java8, java11에서는 내부적으로 String 변수에 변수 변경시 StringBuilder를 사용하도록 변경됨)
- String은 매번 새로운 객체(참조)를 만들지만 StringBuilder는 builder패턴을 사용하여 항상 같은 객체(참조)를 통해서 변경이 됩니다.
- String은 Immutable 매번 새로운 객체를 생성
- StringBuilder는 mutable 객체를 변경

Immutable 잘못된 예)
- Immutable 잘못된 예로는 생성자로 final 데이터를 받더라도 참조가 아닌 복사를 해야한다.(객체를 생성해야함)
    ```java
        // 인터넷에 있는 예제 date
        public static void main(String args[]) {
            Date date = new Date(); 
            Pojo pojo = new Pojo(date);
            // 현재 날짜
            System.out.println("변경 전 : " + pojo.getDate()) 
            date.setTime(date.getTime() + 1000);
            // 변경된 날짜
            // 여기서 데이트는 참조를 하고 있기 때문에 해당 데이터를 변경해버리면 final 객체로 선언을 했더라도 변경되어 버린다.
            System.out.println(pojo.getDate()) 
        }
        
        class Pojo {
            private final Date date;
            
            public Pojo(Date date) {
                // 잘못된 예
                this.date = date;
                // 잘된 예
                this.date = new Date(date.getTime());
            }

            public Date getDate() {
                return this.date;
            }
        }
    ```

생각..
- 생각보다 데이터 변경에 대한 실수가 많다. 그래서 인지 요새는 pojo 객체를 만들때 setter를 전부 만들기 보다는 필요한 필드에 대해서만 setter객체를 만드는 편이다.
- 또한, 생성자 혹은 인스턴스를 만드는 부분에서도 매개변수에 final 객체를 만들어서 실수를 방지하는 역할로도 쓰이는 것 같다.(final에 익숙하지 못하다보니 까먹음)
- 실수를 줄이기 위해서는 immutabel/mutable에 대해서도 고민해야 할듯!
- 아 그리고 위에서 잘못된 예를 보면 참조를 하는데 참조를 하게되면 참조한 값에 대해서는 어떻게 해야할지 생각을 해봐야한다.(컬렉션의 경우 실수할 가능성이 높다.. 그래서 예전에 컬렉션, 객체 복사까지 알아봤지만.. clone 방법은 좋지 않으니 새로 만들거나 함수 depth를 벗어나(지역변수) 다른 곳에서 하는 방법을 추천한다. )