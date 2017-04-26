# Practice

```java
public class ExtendTest {

    private ExtendTest(){
        System.out.println("ExtendTest private Constructor...");
    }

    static class Child extends ExtendTest{

        public Child(){

        }

    }


    @Test
    public void test(){
        Child child = new Child();

    }

    public static void main(String[]args){
        Child child = new Child();
      	//ExtendTest private Constructor...
      	//succeed,but must static class,else compile error.
    }
}
```



---

created at 2017-04-27 01:42