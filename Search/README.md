# 七大查找算法

一、查找分类
* 静态查找：查找过程中，查找的表中，没有插入或者删除的动作。
* 动态查找：查找过程中，查找的表中，有插入或者删除的动作。
* 无序查找：查找的表是无序的表
* 有序查找：查找的表是有序的表

二、平均查找长度ASL（Average Search Length）
* ASL=sum(Pi * Ci)
* Pi：查找表中第i个元素的概率
* Ci：查找表中第i个元素时已经比较的次数

# 1、顺序查找
顺序查找，也就是一个个比对过去，当发现相等时就查找成功，到末尾还没有就查找失败
* 适用范围：线性结构(无序)，当然有序的也行
* 平均查找长度ASL= (1/n) * (1+2+3+4+5...+n) = (1+n)/2
* 时间复杂度为O(n)

```
public class SequentialSearch {
    public static int search(int[] arr, int value){
        for(int i = 0 ; i < arr.length ; i++){
            if(arr[i] == value){
                return i;
            }
        }
        return -1;
    }

    public static void main(String[] args){
        int[] a = {8,5,6,9,8,4,2,2,0,5,6,3,1,0,7};
        int result = search(a, 7);
        if(result > 0){
            System.out.println("The first Index of 7 in the array: " + result);
        }
    }
}
```

# 2、二分查找(折半查找)
二分查找，也就是在有序的线性表中，跟中间的节点值进行比对，然后根据比对结果去左右子表递归进行中间比对操作，直到出现值相等或者查询完全部节点
* 适用范围：线性结构(有序)
* 时间复杂度为O(logn)
* 折半查找的前提条件是需要有序表顺序存储，对于静态查找表，一次排序后不再变化，折半查找能得到不错的效率。但对于需要频繁执行插入或删除操作的数据集来说，维护有序的排序会带来不小的工作量，那就不建议使用

```
import java.util.Arrays;
import java.util.Collections;

public class BinarySearch {
    public static int search(int[] arr, int value){
        return search(arr, 0, arr.length-1, value);
    }

    private static int search(int[] arr, int start, int end, int value){
        int mid = (start + end) / 2;
        if(arr[mid] == value)
            return mid;
        //若已经到了只剩一个元素，但是还是不等
        //那就证明不存在了
        if(start == end)
            return -1;

        if(arr[mid] > value)
            return search(arr, start, mid-1, value);
        else
            return search(arr, mid+1, end, value);
    }

    public static void main(String[] args){
        int[] a = {8,5,6,9,8,4,2,2,0,5,6,3,1,0,7};
        Arrays.sort(a);
        int result = search(a, 8);
        if(result > 0){
            System.out.println("The first Index of 8 in the array: " + result);
        }
        for(int i = 0 ; i < a.length ; i++){
            System.out.print(a[i] + " ");
        }
    }
}
```
注：以上代码并没有处理重复元素，也就是返回的可能是重复元素的任一个索引值而不是最小索引值

# 3、插值查找
插值查找是二分查找的一个改进，就比如查字典，若是要查apple，我们肯定会去查找字典的比较前面的部分，而不是从中间分开往左边找<br>
所以插值查找实际就是对二分查找的中间点改进为<br>
```
mid=low+(key-a[low])/(a[high]-a[low])*(high-low)
```
也就是将原本是固定的中间点改为自适应的，让mid的变化更靠近key，这样也就减少了比较次数
* 适用范围:线性结构(有序)
* 时间复杂度:查找成功或者失败的时间复杂度均为O(log2(log2n))
* 对于表长较大，而关键字分布又比较均匀的查找表来说，插值查找算法的平均性能比折半查找要好的多。反之，数组中如果分布非常不均匀，那么插值查找未必是很合适的选择

```
public class InsertSearch {
    public static int search(int[] arr, int value){
        return search(arr, 0, arr.length-1, value);
    }
    private static int search(int[] arr, int start, int end, int value){
        int mid = start+(value-arr[start])/(arr[end]-arr[start])*(end-start);
        if(arr[mid] == value)
            return mid;
        //若已经到了只剩一个元素，但是还是不等
        //那就证明不存在了
        if(start == end)
            return -1;

        if(arr[mid] > value)
            return search(arr, start, mid-1, value);
        else
            return search(arr, mid+1, end, value);
    }

    public static void main(String[] args){
        int[] a = {1,2,3,5,8,9,11,13,15,19};
        int result = search(a, 15);
        if(result > 0){
            System.out.println("The first Index of 15 in the array: " + result);
        }
        for(int i = 0 ; i < a.length ; i++){
            System.out.print(a[i] + " ");
        }
    }
}
```

# 4、斐波那契查找
![无法加载图片](https://github.com/Ywfy/algorithm/blob/master/Search/20150323100632467.jpg)<br>
斐波那契查找同样是二分查找的针对中间点的改进<br>
```
斐波那契查找要求开始表中记录的个数为某个斐波那契数小1，及n=F(k)-1;

开始将k值与第F(k-1)位置的记录进行比较(及mid=low+F(k-1)-1),比较结果也分为三种

1）相等，mid位置的元素即为所求

2）>，low=mid+1,k-=2;

说明：low=mid+1说明待查找的元素在[mid+1,high]范围内，k-=2 说明范围[mid+1,high]内的元素个数为n-(F(k-1))= Fk-1-F(k-1)=Fk-F(k-1)-1=F(k-2)-1个，所以可以递归的应用斐波那契查找。

3）<，high=mid-1,k-=1。

说明：low=mid+1说明待查找的元素在[low,mid-1]范围内，k-=1 说明范围[low,mid-1]内的元素个数为F(k-1)-1个，所以可以递归 的应用斐波那契查找。
```
