# Guarded Suspension 패턴

Guarded Suspension 패턴 이란?
- wait(), notify(), notifyAll()을 이용하여 Thread판 if문으로 생각하면 될 것같다.
- 그냥 if문 혹은 while() 등을 통해서 **`Guard`** 라는 조건을 생성 후 해당 조건이 안맞으면 wait()를 통해 대기하고 맞으면 notify()을 통해서 wait를 해제하고 로직을 수행한다.
- 조건이 맞을때 항상 notify() 해주는게 뭔가 공통화를 시키고 싶다.

Guarded Suspension 간단 예)
```java
    class Message {
        private int id;
        private String message;

        public Message(int id, String message) {
            this.id = id;
            this.message = message;
        }

        public int getId() {
            return this.id;
        }

        public String getMessage() {
            return this.message;
        }
    }

    class Worker {
        private final Queue<Message> queue;
        public Worker() {
            queue = new LinkedList<Message>();
        }

        // 큐는 쓰레드 세이프 하지 않기 때문에 synchronized를 걸어줘야함. 
        public synchronized Message getMessage() {
            while(queue.peek() == null) {
                try {
                    wait();
                } catch(Exception e) {}
            }
            return queue.poll();
        }

        // 큐는 쓰레드 세이프 하지 않기 때문에 synchronized를 걸어줘야함. 
        // Concurrent pkage에서 쓰레드 세이프한 큐를 만들 수 있다.
        public synchronized void putMessage(String message) {
            queue.offer(message)
            notifyAll();
        }
    }

    public static void main(String args[]) throws Exception{
        Worker worker = new Worker();

        ...
        // a쓰레드에서 메세지를 계속 넣고 있다고 가정
        worker.pushMessage(message);
        ... 

        // b쓰레드를 통해서 동작한다고 가정(while)
        ...
        while(true) {
            Message message = worker.getMessage();
            System.out.println("Message : " + message);
            Thread.sleep(1000);
        }
        ...
    }
``` 

- 예제는 a라는 가상의 쓰레드에서 worker 큐에 지속적으로 메세지를 넣고, b라는 쓰레드에서 해당 메세지를 소비하여 출력해주는 동작을 하고 있습니다.
    1. 먼저, b쓰레드 내부에 worker.getMessage()에 while()문 내에 queue.peek()라는 함수를 통해서 데이터가 존재하는지를 체크합니다. 
    2. 이후 데이터가 있으면 while 내부를 타지 않고 큐를 하나 빼서 리턴합니다.
    3. 만약 데이터가 존재하지 않으면 while()문 내부로 들어와서 wait()를 호출합니다.(이때 wait()를 안타고 무한으로 돌아도 된다고 생각이됨..)
    4. 리턴 된 데이터를 소비하고 다시 데이터를 가져와서 처리하는 형식
- Guarded Suspension 패턴에서 **`Guard`** 부분은 while(queue.peek() == null) 부분이다. Guard부분을 통해서 조건을 체크하고 로직을 실행한다. 이부분이 Guarded Suspension 패턴...? 그냥 상황을 패턴이라고 설명한거 같다.
- 사실 굳이 위에서 언급했지만 wait를 통해서 멈추지 않고 배치나 그냥 무한루프를 사용해서 로직을 구현해도 될거 같다.
- 큐에 데이터를 넣는 pushMessage()와 데이터를 가져오는 getMessage()는 현재 synchronized가 걸려있는데 멀티 쓰레드 환경에서 해당 부분에 동시에 접근할 경우 중복문제 등 문제가 발생할 수 있기 때문에 synchronized를 설정(싱글 쓰레드 패턴 참고)


생각.. 
- 패턴이 아닌거 같다...