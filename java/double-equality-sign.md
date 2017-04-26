# Practice

```java
public class DoubleEqualitySign {

    @Test
    public void test(){
        Integer i1 = 1000;
        Integer i2 = 1000;

        System.out.println( i1 == i2 );//false
        System.out.println( i1.equals(i2) );//true
        System.out.println( i1.hashCode() + "\t" + i2.hashCode());//1000   1000

        i1 = 127; i2 = 127;
        System.out.println( i1 == i2 );//true

        i1 = 128; i2 = 128;
        System.out.println( i1 == i2 );//false

        i1 = -128; i2 = -128;
        System.out.println( i1 == i2 );//true

        i1 = -129; i2 = -129;
        System.out.println( i1 == i2 );//false

        Long l1=127L,l2=127L;
        System.out.println( l1 == l2 );//true

        l1=128L; l2=128L;
        System.out.println( l1 == l2 );//false
      
      	int i3=1000,i4=1000;
        System.out.println( i3 == i4 );//true

        i1=1000;//i1 is an Integer
        System.out.println( i1 == i3 );//true
    }
}
```



```java
@Test
public void test2(){
    Double d1 = new Double(12.0);
    Double d2 = d1;
    System.out.println( d1 == d2 );//true
    d1 = d1 + 1;
    System.out.println( d1 == d2 );//false //!!!

    Integer i1 = new Integer(1000);
    Integer i2 = i1;
    System.out.println( i1 == i2 );//true

    i1 = i1 + 1;
    System.out.println( i1 == i2 );//false //!!!
  	//!!!
    System.out.println( i1 + "\t" + i2);//1001 1000
}
```





---

created at 2017-04-27 00:11