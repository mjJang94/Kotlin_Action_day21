# 자바 메소드에 람다를 인자로 전달
함수형 인터페이스를 인자로 원하는 자바 메소드에 코틀린 람다를 전달할 수 있다. 예를 들어 다음 메소드는 Runnable 타입을 파라미터로 받는다.
<pre>
<code>
void postponeComputation(int delay, Runnable computation);
</pre>
</code>
코틀린에서는 이 람다를 함수에 넘길 수 있다. 컴파일러는 자동으로 람다를 Runnable 인스턴스로 변환해준다.
<pre>
<code>
postponeComputation(1000) {println(42)}//--- 프로그램 전체에서 Runnable의 인스턴스는 단 하나만 만들어진다.
</pre>
</code>
여기서 말하는 Runnable 인스턴스라는 말은 실제로는 Runnable을 구현한 무명 클래스의 인스턴스라는 뜻이다. 컴파일러는 자동으로 그런 무명 클래스와 인스턴스를 만들어준다.
예를들어 Runnable의 run이 그런 추상 메소드이다.   
하지만 람다와 무명 객체 사이에는 차이가 있는게 객체를 명시적으로 선언하는 경우 메소드를 호출할 때마다 새로운 객체가 생성된다.(추가생성)  
람다는 정의가 들어있는 함수의 변수에 접근하지 않는 람다에 대응하는 무명 객체를 메소드를 호출할 때마다 반복사용한다.(재활용)   
명시적인 object 선언을 사용하면서 람다와 동일한 코드는 아래와 같다. 이 경우 Runnable 인스턴스를 변수에 저장하고 메소드를 호출할 때마다 그 인스턴스를 사용한다.
<pre>
<code>
val runnable = Runnable{ println(42) } //---- 전역 변수로 컴파일되므로 프로그램 안에 단 하나의 인스턴스만 존재함
fun handleComputation() {
  postponeComputation(1000, runnable) 
}
</pre>
</code>
람다가 주변 영역의 변수를 포획하면 매 호출마다 같은 인스턴스를 재활용 할 수 없으므로 그런 경우 컴파일러는 매번 주변 영역의 변수를 포획한 새로운 인스턴스를 생성해준다.
<pre>
<code>
fun handleComputation(id: String) { //----람다 안에서 id변수를 포획한다.
  postponeComputation(1000) { print(id) } //----handleComputation을 호출할 때마다 새로 Runnable 인스턴스를 만듬
}
</pre>
</code>

# SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경
SAM 생성자는 람다를 함수형 인터페이스의 인스턴스로 변환할 수 있게 컴파일러가 자동으로 생성한 함수를 말한다. 컴파일러가 자동으로 람다를 함수형 인터페이스 무명 클래스로 바꾸지 못할 경우에는 SAM 생성자를 사용할 수 있다.
<pre>
<code>
fun createAllDoneRunnable(): Runnable{
  return Runnable{ println("All done!") }
}
>>> createAllDoneRunnable().run()
All done!
</pre>
</code>

람다로 생성한 함수형 인터페이스 인스턴스를 변수에 저장해야 하는 경우에도 SAM 생성자를 사용할 수 있다. 여러 버튼에 같은 리스너를 적용하고 싶으면 다음 리스트 처럼 SAM 생성자를 통해 람다를 함수형 인터체이스 인스턴스로  만들어서 변수에 저장해 활용할 수 있다.
<pre>
<code>
val listener = OnclickListener{ view ->
  val text = when(view.id){
    R.id.button1 -> "First button"
    R.id.button2 -> "Second button"
    else -> "Unknown button"
   }
  toast(text)
}

button1.setOnclickListener(listener)
button2.setOnclickListener(listener)
</pre>
</code>
