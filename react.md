## Redux
**Redux** 에서는 
reducer를 정의하고 createStore에 정의한 reducer를 넣어주어 store를 생성할 수 있다.
reducer에서는 반드시 원본 객체를 수정하는 것이 아닌, **새로운 객체**를 매 액션마다 리턴해주어야 한다.

반면 **RTK** 에서는 
reducer를 createSlice에 넣어주어 store를 생성하는데, 내부적으로 **immer** 라이브러리를 사용하고 있기 때문에 원본 객체를 수정하여도 자동으로 원래 상태 객체를 복제하여 새로운 객체를 리턴하도록 처리한다.
또한 createSlice를 통해 reducer를 생성하고나면 해당 메서드를 직접 호출할 필요 없이 slice.action.*methodName 와 같이 액션을 호출할 수 있다. (액션 생성자)

=> 오타를 줄이고 액션 메서드를 식별하는 if문 보일러플레이트를 줄이는 효과
