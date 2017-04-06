

# Bubble Sort

Bubble sort, sometimes referred to as **sinking sort**, is a simple sorting algorithm that repeatedly steps through the list to be sorted, compares each pair of adjacent items and swaps them if they are in the wrong order. The pass through the list is repeated until no swaps are needed, which indicates that the list is sorted. The algorithm, which is a comparison sort, is named for **the way smaller or larger elements "bubble" to the top of the list**. Although the algorithm is simple, **it is too slow and impractical for most problems even when compared to insertion sort**. **It can be practical if the input is usually in sorted order but may occasionally have some out-of-order elements nearly in position.**

![](https://upload.wikimedia.org/wikipedia/commons/c/c8/Bubble-sort-example-300px.gif)

### Time Complexity

 O($n^2$)

bubble sort is not a practical sorting algorithm when *n* is large.

When the list is already sorted (best-case), the complexity of bubble sort is only *O*(*n*).

### Example

**First Pass**

( **5 1** 4 2 8 ) →  ( **1 5** 4 2 8 ), Here, algorithm compares the first two elements, and swaps since 5 > 1. 

( 1 **5 4** 2 8 ) →  ( 1 **4 5** 2 8 ), Swap since 5 > 4 

( 1 4 **5 2** 8 ) →  ( 1 4 **2 5** 8 ), Swap since 5 > 2 

( 1 4 2 **5 8** ) →  ( 1 4 2 **5 8** ), Now, since these elements are already in order (8 > 5), algorithm does not swap them. 


**Second Pass**

( **1 4** 2 5 8 ) →  ( **1 4** 2 5 8 ) 

( 1 **4 2** 5 8 ) →  ( 1 **2 4** 5 8 ), Swap since 4 > 2 

( 1 2 **4 5** 8 ) →  ( 1 2 **4 5** 8 ) 

( 1 2 4 **5 8** ) →  ( 1 2 4 **5 8** ) ,Now, the array is already sorted, but the algorithm does not know if it is completed. The algorithm needs one whole pass without any swap to know it is sorted.

**Third Pass**

( **1 2** 4 5 8 ) →  ( **1 2** 4 5 8 )

( 1 **2 4** 5 8 ) →  ( 1 **2 4** 5 8 )

( 1 2 **4 5** 8 ) →  ( 1 2 **4 5** 8 )

( 1 2 4 **5 8** ) →  ( 1 2 4 **5 8** )

### Using in java

``` java
public void bubbleSort(Integer[] array){
  int temp = 0;
  for(int i=0; i<array.length-1; i++){
    for(int j=i+1; j<array.length; j++){
      if(array[i] > array[j]){
        temp = array[i];
        array[i] = array[j];
        array[j] = temp;
      }
    }
  }
}
```



# Insertion Sort

Insertion sort iterates, consuming one input element each repetition, and growing a sorted output list. Each iteration, insertion sort removes one element from the input data, finds the location it belongs within the sorted list, and inserts it there. It repeats until no input elements remain.

![](https://upload.wikimedia.org/wikipedia/commons/0/0f/Insertion-sort-example-300px.gif)

### Time Complexity

 O($n^2$)

### Example

We color a sorted part in green(**bold**), and an unsorted part in black. Here is an insertion sort step by step. We take an element from unsorted part and compare it with elements in sorted part, moving form right to left.

29,  20,  73,  34,  64 

**29**,  20,  73,  34,  64 

**20, 29**,  73,  34,  64 

**20, 29,  73**,  34,  64 

**20, 29,  34,  73**,  64 

**20, 29,  34,  64,  73**

### Using in java

``` java
public void insertionSort(Integer[] array){
        for(int i=1; i<array.length; i++){
            int j = i-1;
            while (j>=0){
                if(array[i] < array[j]){
                    j--;
                }else{
                    break;
                }
            }
            j += 1;
            int p = i;
            // move elements
            if(p > j){
                int pValue = array[p];
                while (p>j){
                    array[p] = array[p-1];
                    p--;
                }
                array[j] = pValue;//insert current element
            }
        }
    }
```



# References

[Bubble sort](https://en.wikipedia.org/wiki/Bubble_sort)

[Sorting Algorithms Animations](https://www.toptal.com/developers/sorting-algorithms)

[Sorting](https://www.cs.cmu.edu/~adamchik/15-121/lectures/Sorting%20Algorithms/sorting.html)



---

created at 2017-04-06 19:56