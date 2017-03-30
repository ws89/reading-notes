# Usage in Java

**Enum**

``` java
public enum Singleton{
  INSTANCE;
}
```

**Double-checked locking**

``` java
public class Singleton{
  private Singleton(){}
  private static volatile Singleton s;//volatile are supposed to be sequentially consistent,not reordered  
  
  public static Singleton getInstance(){
    if(s == null){						//single checked
      synchronized(Singleton.class){
        if(s ==null ){					//double checked
          s = new Singleton();
        }
      }
    }
    return s;
  }
}
```

**static nested class**

``` java
public class Singleton{
  private Singleton(){}
  public static Singleton getInstance(){
    return SingletonHolder.s;
  }
  
  // static nested class
  private static class SingletonHolder{
    private static Singleton s = new Singleton();
  }
}
```

**static field** 

``` java
public class Singleton{
  private Singleton(){}
  private static Singleton s = new Singleton();
  
  public static Singleton getInstance(){
    return s;
  }
}
```

**static method**

``` java
public class Singleton{
  private Singleton(){}
  private Singleton s;
  
  public static Singleton getInstance(){
    if(s == null){
      s = new Singleton();
    }
    return s;
  }  
}
```



# References

[6种单例模式的实现以及double check的剖析](http://taoxiaoran.top/2017/02/10/singleton.html)

[Double-checked locking](https://en.wikipedia.org/wiki/Double-checked_locking#Usage_in_Java)



------

created at 2017-03-30 21:45