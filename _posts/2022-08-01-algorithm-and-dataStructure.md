# 排序-冒泡排序

##  简介

排序可能是所有的算法中最最基础和最最常用的了。排序是一个非常经典的问题，它以一定的顺序对一个数组（或一个列表）中的项进行重新排序。

排序算法有很多种，每个都有其自身的优点和局限性。

今天我们来学习最最简单的冒泡排序算法。

## 冒泡排序的原理

冒泡排序的原理很简单，我们想象一下一个一个的气泡上浮的过程。

假设我们有八个数字 29,10,14,37,20,25,44,15 要进行排序。

我们先用一个动画图来直观的观察一下整个冒泡排序的过程：

![20200708155735461](../../../../../Download/Typora/image/20200708155735461.gif)

排序共进行八轮，每一轮都会做两两比较，并将较大的元素右移，就像冒泡一下。

一轮结束之后，八个元素中最大的那个元素44将会移动到最右边。

然后再重复其他的几轮。最终得到一个完全排序的数组。

也可以这样看：

第一轮是将八个元素中的最大值44交换移动到最右位置。
第二轮是将八个元素中的次大值37交换移动到最右位置。
以此类推。

## 冒泡排序算法的java实现

先看一个最简单的冒泡算法：

```java
public class BubbleSort {

    public void doBubbleSort(int[] array){
        log.info("排序前的数组为:{}",array);
        //外层循环,遍历所有轮数
        for(int i=0; i< array.length-1; i++){
            //内层循环，两两比较，选中较大的数字，进行交换
            for(int j=0; j<array.length-1; j++){
                if(array[j]>array[j+1]){
                    //交换两个数字
                    int temp = array[j];
                    array[j] = array[j+1];
                    array[j+1] = temp;
                }
            }
            log.info("第{}轮排序后的数组为:{}", i+1, array);
        }
    }

    public static void main(String[] args) {
        int[] array= {29,10,14,37,20,25,44,15};
        BubbleSort bubbleSort=new BubbleSort();
        bubbleSort.doBubbleSort(array);
    }
}
```

这个算法就是两层遍历，外层遍历表示的是进行的轮数。内层遍历表示的是每一轮的排序。

我们看下输出结果：

![image-20220801210925501](../../../../../Download/Typora/image/image-20220801210925501.png)

## 冒泡算法的第一次改进

分析上面的遍历过程，我们可以发现，第一次排序之后，44已经放到最右边的位置了，已经排好序了。

第二次排序之后，37也已经排好序了。每过一轮，内部循环需要比较的次数就可以减一。

这就意味着，在内部循环的时候，我们只需要进行array.length-i-1次比较就可以了。

修改代码如下：

```java
public class BubbleSort1 {

    public void doBubbleSort(int[] array){
        log.info("排序前的数组为:{}",array);
        //外层循环,遍历所有轮数
        for(int i=0; i< array.length-1; i++){
            //内层循环，两两比较，选中较大的数字，进行交换, 最后的i个数字已经排完序了，不需要再进行比较
            for(int j=0; j<array.length-i-1; j++){
                if(array[j]>array[j+1]){
                    //交换两个数字
                    int temp = array[j];
                    array[j] = array[j+1];
                    array[j+1] = temp;
                }
            }
            log.info("第{}轮排序后的数组为:{}", i+1, array);
        }
    }

    public static void main(String[] args) {
        int[] array= {29,10,14,37,20,25,44,15};
        BubbleSort1 bubbleSort=new BubbleSort1();
        bubbleSort.doBubbleSort(array);
    }
}
```

可以看到运行结果其实没什么不同，只不过我们少做了几次比较。

## 冒泡算法的第二次改进

从上面的结果，我们可以看到实际上第5轮排序过后就已经排序完成了。但是我们仍然进行了第6，7次排序。

有没有什么办法可以判断排序是不是已经完成了呢？

我们考虑一下，在内部循环中，我们是进行两两比较，然后交换位置。

如果在某一次遍历中，没有进行交互，这就意味着排序已经完成了。

所以我们可以再引入一个flag来做判断。

```java
public class BubbleSort2 {

    public void doBubbleSort(int[] array){
        log.info("排序前的数组为:{}",array);
        //外层循环,遍历所有轮数
        for(int i=0; i< array.length-1; i++){
            //添加一个flag，如果这一轮都没有排序，说明排序已经结束，可以提前退出
            boolean flag=false;
            //内层循环，两两比较，选中较大的数字，进行交换, 最后的i个数字已经排完序了，不需要再进行比较
            for(int j=0; j<array.length-i-1; j++){
                if(array[j]>array[j+1]){
                    //交换两个数字
                    int temp = array[j];
                    array[j] = array[j+1];
                    array[j+1] = temp;
                    flag = true;
                }
            }
            log.info("第{}轮排序后的数组为:{}", i+1, array);
            if(!flag)
            {
                log.info("本轮未发生排序变化，排序结束");
                return;
            }
        }
    }

    public static void main(String[] args) {
        int[] array= {29,10,14,37,20,25,44,15};
        BubbleSort2 bubbleSort=new BubbleSort2();
        bubbleSort.doBubbleSort(array);
    }

}
```

运行结果如下：

<img src="../../../../../Download/Typora/image/image-20220801211404844.png" alt="image-20220801211404844" style="zoom:80%;" />

从结果我们可以看到少了一轮排序，提升了速度。

## 冒泡排序的时间复杂度

虽然我们可以在冒泡的时候进行一些性能优化，但是基本上还是要进行嵌套的两次遍历。遍历次数近似的=n*n，所以冒泡排序算法的时间复杂度是O(n²)。

# 排序-插入排序

## 简介

插入排序就是将要排序的元素插入到已经排序的数组中，从而形成一个新的排好序的数组。

这个算法就叫做插入排序。

## 插入排序的例子

同样的，假如我们有一个数组：29,10,14,37,20,25,44,15，怎么对它进行插入排序呢？

先看一个插入排序的动画，对它有个直观的了解：

![20200709212934477](../../../../../Download/Typora/image/20200709212934477.gif)

我们来分析一下排序的流程。

八个数字，我们分为7轮。

第一轮，假设29是已经排好序的数组，从第二个元素开始，向排好序的数组插入新的元素。 也就是说向数组[29]插入10。得到[10,29]。

第二轮，[10,29]已经排好序了，选择数组中的第三个元素14，插入排好序的数组[10,29]。

先将29和14比较，发现29>14,则将29右移一位[10, ,29],然后比较10和14，发现10小于14，那么将14插入10后面[10,14,29]，以此类推。

## 插入排序的java程序

```java
public class InsertionSort {
    public void doInsertSort(int[] array){
        log.info("排序前的数组为:{}",array);
        int n = array.length;
        //从第二个元素开始插入
        for(int i = 1;i < n;i++){
            int key = array[i];
            int j = i-1;
            //j表示的是要插入元素之前的已经排好序的数组
            //已经排好序的数组，从后向前和要插入的数据比较，如果比要插入的数据大，需要右移一位
            while (j >= 0 && array[j] > key) {
                array[j + 1] = array[j];
                j = j - 1;
            }
            //最后的j+1的位置就是需要插入新元素的位置
            array[j + 1] = key;
            log.info("第{}轮排序后的数组为:{}", i+1, array);
        }
    }
    public static void main(String[] args) {
        int[] array= {29,10,14,37,20,25,44,15};
        InsertionSort insertionSort=new InsertionSort();
        insertionSort.doInsertSort(array);
    }
}
```

运行结果：

<img src="../../../../../Download/Typora/image/image-20220801212758041.png" alt="image-20220801212758041" style="zoom:80%;" />

## 插入排序的时间复杂度

从代码中我们可以看到，插入排序有一个for循环，在for循环中还有一个while循环。

所以插入排序的时间复杂度也是O(n²)。

# 排序-选择排序

## 简介

选择排序就是从数组中选择出来最大或者最小的元素，然后将其和队首或者队尾的元素进行交互。

因为首先做的是一个选择的过程，所以叫做选择排序。

## 选择排序的例子

假如我们有一个数组：29,10,14,37,20,25,44,15，怎么对它进行选择排序呢？

先看一个动画：

![2020070819571632](../../../../../Download/Typora/image/2020070819571632.gif)

选择排序的原理如下：

8个数字，我们需要进行7轮排序。

以第一轮为例，我们对对所有的数据进行比较，找到其中最小的那个10，然后把10放在数组的第一个。

当第二轮时，因为数组的第一个元素10已经排好序了，我们只需要从第二个位置开始就行了，同样的，第二轮我们找到后面几个元素中最小的那个14，将其放在数组的第二个位置。

以此类推进行7轮排序就得到了最后的结果。

## 选择排序的java代码实现

```java
public class SelectionSort {
    public void doSelectionSort(int[] array){
        log.info("排序前的数组为:{}",array);
        //外层循环,遍历所有轮数
        for(int i=0; i< array.length-1; i++){
            //内层循环，找出最小的那个数字
             int minIndex=i;
            for(int j=i+1;j<array.length;j++){
                 if(array[j] < array[minIndex]){
                     minIndex = j;
                 }
            }
            //每次选择完成后，将minIndex所在元素和第i个元素互换
            int temp = array[minIndex];
            array[minIndex] = array[i];
            array[i] = temp;
            log.info("第{}轮排序后的数组为:{}", i+1, array);
        }
    }
    public static void main(String[] args) {
        int[] array= {29,10,14,37,20,25,44,15};
        SelectionSort selectionSort=new SelectionSort();
        selectionSort.doSelectionSort(array);
    }
}
```

运行结果：

<img src="../../../../../Download/Typora/image/image-20220801213535450.png" alt="image-20220801213535450" style="zoom:80%;" />

## 选择排序的第二种java实现

上面的代码中，我们每次查找的是最小的那个元素，同样的，我们也可以查找最大的那个元素。

```java
public class SelectionSort1 {

    public void doSelectionSort(int[] array){
        log.info("排序前的数组为:{}",array);
        //外层循环,遍历所有轮数
        for(int i=0; i< array.length-1; i++){
            //内层循环，找出最大的那个数字
            int maxIndex=0;
            for(int j=0;j<array.length-i;j++)
            {
                if(array[j] > array[maxIndex])
                {
                    maxIndex = j;
                }
            }
            //每次选择完成后，将maxIndex所在元素和length-i-1的元素互换
            int temp = array[array.length-i-1];
            array[array.length-i-1] = array[maxIndex];
            array[maxIndex] = temp;
            log.info("第{}轮排序后的数组为:{}", i+1, array);
        }
    }

    public static void main(String[] args) {
        int[] array= {29,10,14,37,20,25,44,15};
        SelectionSort1 selectionSort=new SelectionSort1();
        selectionSort.doSelectionSort(array);
    }
}
```

运行结果：

<img src="../../../../../Download/Typora/image/image-20220801213800501.png" alt="image-20220801213800501" style="zoom:80%;" />

两种排序大家要注意内部循环的比较条件是不一样的。

## 选择排序的时间复杂度

选择排序和冒泡排序一样，都需要进行n*n的循环，所以其时间复杂度也是O(n²)。

# 排序-归并排序

## 简介

归并排序简称Merge sort是一种递归思想的排序算法。这个算法的思路就是将要排序的数组分成很多小的部分，直到这些小的部分都是已排序的数组为止（只有一个元素的数组）。

然后将这些排序过的数组两两合并起来，组成一个更大一点的数组。接着将这些大一点的合并过的数组再继续合并，直到排序完整个数组为止。

## 归并排序的例子

假如我们有一个数组：29,10,14,37,20,25,44,15，怎么对它进行归并排序呢？

先看一个动画：

![20200709135228799](../../../../../Download/Typora/image/20200709135228799.gif)

我们来详细分析一下上面例子的运行过程：

首先将数组分为两部分，[29,10,14,37]和[20,25,44,15]。

[29,10,14,37]又分成两部分[29,10]和[14,37]。

[29,10]又被分成两部分[29]和[10]，然后对[29]和[10]进行归并排序生成[10,29]。

同样的对[14,37]进行归并排序得到[14,37]。

将[10,29]和[14,37]再次进行归并排序得到[10,14,29,37]，以此类推，得到最后的结果。

## 归并排序算法思想

归并排序主要使用了分而治之的思想。将一个大的数组分成很多很多个已经排序好的小数组，然后再对小数组进行合并。

这个Divide的过程可以使用递归算法，因为不管是大数组还是小数组他们的divide逻辑是一样的。

而我们真正做排序的逻辑部分是在合并这一块。

## 归并排序的java实现

先看一下最核心的merge部分：

```java
   /**
     *合并两部分已排序好的数组
     * @param array 待合并的数组
     * @param low   数组第一部分的起点
     * @param mid   数组第一部分的终点，也是第二部分的起点-1
     * @param high  数组第二部分的终点
     */
    private void  merge(int[] array, int low, int mid, int high) {
        // 要排序的数组长度
        int length = high-low+1;
        // 我们需要一个额外的数组存储排序过后的结果
        int[] temp= new int[length];
        //分成左右两个数组
        int left = low, right = mid+1, tempIdx = 0;
        //合并数组
        while (left <= mid && right <= high) {
            temp[tempIdx++] = (array[left] <= array[right]) ? array[left++] : array[right++];
        }
        //一个数组合并完了，剩下的一个继续合并
        while (left <= mid) temp[tempIdx++] = array[left++];
        while (right <= high) temp[tempIdx++] = array[right++];
        //将排序过后的数组拷贝回原数组
        for (int k = 0; k < length; k++) array[low+k] = temp[k];
    }
```

大家需要注意的是，我们的元素是存在原始数组里面的，方法的第一个参数就是原始数组。

后面的三个参数是数组中需要归并排序的index。三个index将数组划分成了两部分：array[low to mid], array[mid+1 to high]。

merge的逻辑就是对这两个数组进行合并。

因为我们的数组本身是存放有原始的，所以要想进行归并排序，我们需要借助一个额外的数组空间int[] temp。

通过比较array[low to mid], array[mid+1 to high]中的元素大小，一个个将元素插入到int[] temp中，最后将排序过后的数组拷贝回原数组，merge完成。

然后我们再看一下divide的部分，divide部分实际上就是递归调用，在递归的最后，我们需要调用merge方法即可：

```java
    public void doMergeSort(int[] array, int low, int high){
        // 要排序的数组 array[low..high]
        //使用二分法进行递归，当low的值大于或者等于high的值的时候，就停止递归
        if (low < high) {
            //获取中间值的index
            int mid = (low+high) / 2;
            //递归前面一半
            doMergeSort(array, low  , mid );
            //递归后面一半
            doMergeSort(array, mid+1, high);
            //递归完毕，将排序过后的数组的两部分合并
            merge(array, low, mid, high);
            log.info("merge之后的数组:{}",array);
        }
    }
```

array是原数组，low和high标记出了要递归排序的数组起始位置。

运行下上面的结果：

<img src="../../../../../Download/Typora/image/image-20220801214802972.png" alt="image-20220801214802972" style="zoom:80%;" />

可以看到输出结果和我们动画展示的结果是一致的。

## 归并排序的时间复杂度

我们看下归并排序的时间复杂度是怎么样的。

首先看merge方法，merge方法实际是遍历了两个数组，所以merge方法的时间复杂度是O(N)。

再看一下divide方法：

<img src="../../../../../Download/Typora/image/image-20220801214845233.png" style="zoom:80%;" />

divide方法将排序分成了logN层，每层都可以看做是对N个元素的合并排序，因此每层的时间复杂度是O(N)。

加起来，总的时间复杂度就是O(N logN)。

# 排序-快速排序

## 简介

快熟排序也采用分而治之的思想。那么快速排序和归并排序的区别在什么地方呢？

归并排序是将所有的元素拆分成一个个排好序的数组，然后将这些数组再进行合并。

而快速排序虽然也是拆分，但是拆分之后的操作是从数组中选出一个中间节点，然后将数组分成两部分。

左边的部分小于中间节点，右边的部分大于中间节点。

然后再分别处理左边的数组合右边的数组。

## 快速排序的例子

假如我们有一个数组：29,10,14,37,20,25,44,15，怎么对它进行快速排序呢？

先看一个动画：

![20200709213056298](../../../../../Download/Typora/image/20200709213056298.gif)

我们再分析一下快速排序的步骤。

我们选择的是最左边的元素29作为中间点元素，然后将数组分成三部分：[0, 14, 15, 20, 25],[29],[44, 37]。

中间节点29已经排好序了，不需要处理。

接下来我们再对左右分别进行快速排序。最后就得到了一个所有元素都排序的数组。

## 快速排序的java代码实现

我们先来看最核心的部分partition，如何将数组以中间节点为界，分成左右两部分呢？

我们的最终结果，是要将array分割成为三部分。

首先我们选择最左侧的元素作为中间节点的值。然后遍历数组中的其他元素。

假如m=middleIndex，k=要遍历的元素index

考虑两种情况，第一种情况是数组中的元素比中间节点的值要大。

<img src="../../../../../Download/Typora/image/image-20220811202909045.png" alt="image-20220811202909045" style="zoom:80%;" />

这种情况下，m不需要移动，k+1继续遍历即可。

第二种情况下，数组中的元素比中间节点的值要小。

<img src="../../../../../Download/Typora/image/image-20220811203116914.png" alt="image-20220811203116914" style="zoom:80%;" />

因为m左边的元素都要比中间节点的值要小，所以这种情况下m需要+1，即右移一位。

现在m+1位置的元素要么还没有进行比较，要么就是比中间节点的值要大，我们可以巧妙的将m+1位置的元素和k位置的元素互换位置，这样仍然能够保证m左侧的元素要比中间节点的值要小。

将上面的分析总结成java代码如下：

```java
private int partition(int[] array, int i, int j) {
    //选择最左侧的元素作为中心点,middleValue就是中心点的值
    int middleValue = array[i];
    int middleIndex = i;
    //从i+1遍历整个数组
    for (int k = i+1; k <= j; k++) {
        //如果数组元素小于middleValue，表示middleIndex需要右移一位
        //右移之后，我们需要将小于middleValue的array[k]移动到middleIndex的左边，
        // 最简单的办法就是交换k和middleIndex的值
        if (array[k] < middleValue) {
            middleIndex++;
            //交换数组的两个元素
            swap(array, k , middleIndex);
        }//如果数组元素大于等于middleValue,则继续向后遍历,middleIndex值不变
    }
    // 最后将中心点放入middleIndex位置
    swap(array, i, middleIndex);
    return middleIndex;
}
```

最后我们需要将最左侧的元素和中间节点应该在的index的元素互换下位置，这样就将中间节点移动到了中间位置，并返回中间位置。

再来看下divide的代码：

```java
public void doQuickSort(int[] array, int low, int high) {
    //递归的结束条件
    if (low < high) {
        //找出中心节点的值
        int middleIndex = partition(array, low, high);
        //数组分成了三部分：
        // a[low..high] ~> a[low..m–1], pivot, a[m+1..high]
        //递归遍历左侧部分
        doQuickSort(array, low, middleIndex-1);
        // a[m] 是中心节点，已经排好序了，不需要继续遍历
        //递归遍历右侧部分
        doQuickSort(array, middleIndex+1, high);
        log.info("QuickSort之后的数组:{}",array);
    }
}
```

divide的代码就很简单了，找到中间节点的位置之后，我们再分别遍历数组的左右两边即可。最后得到排好序的数组。

## 随机快速排序的java实现

上面的例子中，我们的中间节点的选择是数组的最左元素，为了保证排序的效率，我们可以从数组中随机选择一个元素来作为中间节点。

```java
 private int partition(int[] array, int i, int j) {
        //随机选择一个元素作为中心点,middleValue就是中心点的值
        int randomIndex=i+new Random().nextInt(j-i);
        log.info("randomIndex:{}",randomIndex);
        //首先将randomIndex的值和i互换位置,就可以复用QuickSort的逻辑
        swap(array, i , randomIndex);
        int middleValue = array[i];
        int middleIndex = i;
        //从i遍历整个数组
        for (int k = i+1; k <= j; k++) {
            //如果数组元素小于middleValue，表示middleIndex需要右移一位
            //右移之后，我们需要将小于middleValue的array[k]移动到middleIndex的左边，
            // 最简单的办法就是交换k和middleIndex的值
            if (array[k] < middleValue) {
                middleIndex++;
                //交换数组的两个元素
                swap(array, k , middleIndex);
            } //如果数组元素大于等于middleValue,则继续向后遍历,middleIndex值不变
        }
        // 最后将中心点放入middleIndex位置
        swap(array, i, middleIndex);
        return middleIndex;
    }
```

上面的代码，我们在分区的时候，先选择出一个随机的节点，然后将这个随机的节点和最左侧的元素交换位置，后面的代码就可以重用上面的QuickSort的代码逻辑了。

## 快速排序的时间复杂度

从上面的分析我们可以看出，每次分区的时间复杂度应该是O(N)，而divide又近似二分法，所以总的时间复杂度是O(N logN)。

# 排序-count排序

## 简介

介绍一种不需要作比较就能排序的算法：count排序。

count排序是一种空间换时间的算法，我们借助一个外部的count数组来统计各个元素出现的次数，从而最终完成排序。

## count排序的例子

count排序有一定的限制，因为外部的count数组长度是和原数组的元素范围是一致的，所以count排序一般只适合数组中元素范围比较小的情况。

我们举一个0-9的元素的排序的例子：3,4,2,5,6,2,4,9,1,3,5。

先看一个动画，看看是怎么排序的：

![20200709212450630](../../../../../Download/Typora/image/20200709212450630.gif)

count数组里面存放的是从0到9这些元素出现的次数。

我们遍历原始数组，遇到相应的数字就给相应的count+1。

等所有的元素都count之后，再根据count数组中的值还原排序过后的数组。

## count排序的java实现

count排序很简单，我们主要掌握下面两个大的步骤：

1. 遍历原始数组，构建count数组。
2. 根据count数组中的count值，重新构建排序数组。

```java
public class CountingSort {

    public void doCountingSort(int[] array){
        int n = array.length;

        // 存储排序过后的数组
        int output[] = new int[n];

        // count数组，用来存储统计各个元素出现的次数
        int count[] = new int[10];
        for (int i=0; i<10; ++i) {
            count[i] = 0;
        }
        log.info("初始化count值:{}",count);

        // 将原始数组中数据出现次数存入count数组
        for (int i=0; i<n; ++i) {
            ++count[array[i]];
        }
        log.info("count之后count值:{}",count);

        //遍历count，将相应的数据插入output
        int j=0;
        for(int i=0; i<10; i++){
            while(count[i]-- > 0){
                output[j++]=i;
            }
        }
        log.info("构建output之后的output值:{}",output);

        //将排序后的数组写回原数组
        for (int i = 0; i<n; ++i)
            array[i] = output[i];
    }

    public static void main(String[] args) {
        int[] array= {3,4,2,5,6,2,4,9,1,3,5};
        CountingSort countingSort=new CountingSort();
        log.info("countingSort之前的数组为:{}",array);
        countingSort.doCountingSort(array);
    }
}
```

上面的注释应该很清楚了。

运行的结果如下：

<img src="../../../../../Download/Typora/image/image-20220811210540177.png" alt="image-20220811210540177" style="zoom:80%;" />

## count排序的第二种方法

在我们获得count数组中每个元素的个数之后，其实我们还有另外一个生成结果数组的办法：

```java
// 这里是一个小技巧，我们根据count中元素出现的次数计算对应元素第一次应该出现在output中的下标。
        //这里的下标是从右往左数的
        for (int i=1; i<10; i++) {
            count[i] += count[i - 1];
        }
        log.info("整理count对应的output下标:{}",count);
        // 根据count中的下标，构建排序后的数组
        //插入一个之后，相应的count下标要减一
        for (int i = n-1; i>=0; i--)
        {
            output[count[array[i]]-1] = array[i];
            --count[array[i]];
        }
        log.info("构建output之后的output值:{}",output);
```

主要分为两步:

第一步我们根据count中元素出现的次数计算对应元素第一次应该出现在output中的下标。这里的下标是从右往左数的。

第二步根据count中的下标，构建排序后的数组，插入一个之后，相应的count下标要减一。

<img src="../../../../../Download/Typora/image/image-20220811210854891.png" alt="image-20220811210854891" style="zoom:80%;" />

可能不是很好理解，大家可以结合输出结果反复琢磨一下。

## count排序的时间复杂度

从上面的代码我们可以看到，count排序实际上只做了少量次数的遍历。所以它的时间复杂度是O(n)。

# 排序-基数排序

之前的文章我们讲了count排序，但是count排序有个限制，因为count数组是有限的，如果数组中的元素范围过大，使用count排序是不现实的，其时间复杂度会膨胀。

而解决大范围的元素排序的办法就是基数排序。

## 基数排序的例子

什么是基数排序呢？

考虑一下，虽然我们不能直接将所有范围内的数字都使用count数组进行排序，但是我们可以考虑按数字的位数来进行n轮count排序，每一轮都只对数字的某一位进行排序。

最终仍然可以得到结果，并且还可以摆脱count数组大小的限制，这就是基数排序。

假如我们现在数组的元素是：1221, 15, 20, 3681, 277, 5420, 71, 1522, 4793。

先看动画，看下最直观的基数排序的过程：

![20200709234431411](../../../../../Download/Typora/image/20200709234431411.gif)

在上面的例子中，我们先对个位进行count排序，然后对十位进行count排序，然后是百位和千位。

最后生成最终的排序结果。

## 基数排序的java代码实现

因为基数排序实际上是分别按位数的count排序。所以我们可以重用之前写的count排序的代码，只是需要进行一些改造。

doCountingSort方法除了传入数组外，还需要传入排序的位数digit，我们用1，10，100，1000来表示。

看一下改造过后的doCountingSort方法：

```java
   public void doRadixSort(int[] array, int digit){
        int n = array.length;

        // 存储排序过后的数组
        int output[] = new int[n];

        // count数组，用来存储统计各个元素出现的次数
        int count[] = new int[10];
        Arrays.fill(count,0);
        log.info("初始化count值:{}",count);

        // 将原始数组中数据出现次数存入count数组
        for (int i=0; i<n; ++i) {
            count[(array[i]/digit)%10]++;
        }
        log.info("count之后count值:{}",count);

        // 这里是一个小技巧，我们根据count中元素出现的次数计算对应元素第一次应该出现在output中的下标。
        //这里的下标是从右往左数的
        for (int i=1; i<10; i++) {
            count[i] += count[i - 1];
        }
        log.info("整理count对应的output下标:{}",count);
        // 根据count中的下标，构建排序后的数组
        //插入一个之后，相应的count下标要减一
        for (int i = n-1; i>=0; i--)
        {
            output[count[(array[i]/digit)%10]-1] = array[i];
            count[(array[i]/digit)%10]--;
        }
        log.info("构建output之后的output值:{}",output);

        //将排序后的数组写回原数组
        for (int i = 0; i<n; ++i)
            array[i] = output[i];
    }
```

跟count排序变化不大，区别就是这里我们需要使用count[(array[i]/digit)%10]，来对每一位进行排序。

另外，为了计算出位数digit的值，我们还需要拿到数组中最大元素的值：

```java
public int getMax(int[] array)
    {
        int mx = array[0];
        for (int i = 1; i < array.length; i++)
            if (array[i] > mx){
                mx = array[i];
            }
        return mx;
    }
```

看下怎么调用：

```java
    public static void main(String[] args) {
        int[] array= {1221, 15, 20, 3681, 277, 5420, 71, 1522, 4793};
        RadixSort radixSort=new RadixSort();
        log.info("radixSort之前的数组为:{}",array);
        //拿到数组的最大值，用于计算digit
        int max = radixSort.getMax(array);
        //根据位数，遍历进行count排序
        for (int digit = 1; max/digit > 0; digit *= 10){
            radixSort.doRadixSort(array,digit);
        }
    }
```

看下输出结果：

<img src="../../../../../Download/Typora/image/image-20220811211218271.png" alt="image-20220811211218271" style="zoom:80%;" />

很好，结果都排序了。

## 基数排序的时间复杂度

从计算过程我们可以看出，基数排序的时间复杂度是O(d*(n+b)) ，其中b是数字的进制数，比如上面我们使用的是10进制，那么b=10。

d是需要循环的轮数，也就是数组中最大数的位数。假如数组中最大的数字用K表示，那么d=logb(k)。

综上，基数排序的时间复杂度是O((n+b) * logb(k))。

当k <= nc，其中c是常量时，上面的时间复杂度可以近似等于O(nLogb(n))。

考虑下当b=n的情况下，基数排序的时间复杂度可以近似等于线性时间复杂度O(n)。

# linkedList

## 简介

linkedList应该是一种非常非常简单的数据结构了。节点一个一个的连接起来，就成了linkedList。今天我们使用动画的方法一起来看看linkedList是怎么插入和删除的。

## linkedList的构建

linkedList是由一个一个的节点构成的。而每个节点只需要存储要保存的数据和下一个节点的引用即可。

<img src="../../../../../Download/Typora/image/image-20220811211404144.png" alt="image-20220811211404144" style="zoom:60%;" />

linkedList本身需要一个head节点，所以我们的linkedList可以这样构建：

```java
public class LinkedList {

    Node head; // head 节点

    //Node表示的是Linked list中的节点，包含一个data数据和下一个节点的引用
    class Node {
        int data;
        Node next;
        //Node的构造函数
        Node(int d) {
            data = d;
        }
    }
}
```

## linkedList的操作

先看一下linkedList怎么插入数据，插入数据有三种方式，头部插入，尾部插入，中间插入。

## 头部插入

先看一个头部插入的例子：

<img src="../../../../../Download/Typora/image/20200711205530567.gif" alt="20200711205530567" style="zoom:80%;" />

头部插入的逻辑是什么呢？

新插入的节点作为head节点，然后将原来的head节点指向当前head节点的next引用即可。

```java
//插入到linkedList的头部
public void push(int newData) {
    //构建要插入的节点
    Node newNode = new Node(newData);
    //新节点的next指向现在的head节点
    newNode.next = head;
    //现有的head节点指向新的节点
    head = newNode;
}
```

## 尾部插入

再看一下尾部插入的例子：

<img src="../../../../../Download/Typora/image/20200711210506686.gif" alt="20200711210506686" style="zoom:80%;" />

插入的逻辑是什么呢？

找到最后一个节点，然后将最后一个节点的next指向新插入的节点。

```java
//新节点插入到list最后面
public void append(int newData) {
    //创建新节点
    Node newNode = new Node(newData);
    //如果list是空，则新节点作为head节点
    if (head == null) {
        head = newNode;
        return;
    }
    newNode.next = null;
    //找到最后一个节点
    Node last = head;
    while (last.next != null) {
        last = last.next;
    }
    //插入
    last.next = newNode;
    return;
}
```

## 中间插入

再看一下中间插入的例子：

<img src="../../../../../Download/Typora/image/2020071121075628.gif" alt="2020071121075628" style="zoom:80%;" />

这个例子中，我们在第三个节点的位置插入了一个93。

插入逻辑就是先找到第二个节点，将第二个节点的next指向新节点，然后将新节点的next指向原先的第三个节点。

看下java代码如何实现：

```java
//插入在第几个元素之后 index=2
public void insertAfter(int index, int newData) {
    Node prevNode = head;
    for (int i = 1; i < index; i++) {
        if (prevNode == null) {
            System.out.println("输入的index有误，请重新输入");
            return;
        }
        prevNode = prevNode.next;
    }
    //创建新的节点
    Node newNode = new Node(newData);
    //新节点的next指向prevNode的下一个节点
    newNode.next = prevNode.next;
    //将新节点插入在prevNode之后
    prevNode.next = newNode;	
}
```

## 删除节点

再看一下怎么删除某个位置的节点：

<img src="../../../../../Download/Typora/image/20200711211322969.gif" alt="20200711211322969" style="zoom:80%;" />

上面的例子中，我们要删除第5个节点。

删除的逻辑就是找到第4个节点和第6个节点。然后将第四个节点的next指向第6个节点即可。

看下相应的java代码如下：

```java
    //删除特定位置的节点
    void deleteNode(int index)
    {
        // 如果是空的，直接返回
        if (head == null)
            return;

        // head节点
        Node temp = head;

        // 如果是删除head节点
        if (index == 1)
        {
            head = temp.next;
            return;
        }

        // 找到要删除节点的前一个节点
        for (int i=1; temp!=null && i<index-1; i++)
            temp = temp.next;

        // 如果超出范围
        if (temp == null || temp.next == null)
            return;

        // temp->next 是要删除的节点，删除节点
        Node next = temp.next.next;
        temp.next = next;
    }
```

# doublyLinkedList

## 简介

今天我们来学习一下复杂一点的LinkedList：doublyLinkedList。

和LinkedList相比，doublyLinkedList中的节点除了next指向下一个节点之外，还有一个prev之前的一个节点。所以被称为doublyLinkedList。 doublyLinkedList是一个双向链表，我们可以向前或者向后遍历list。

## doublyLinkedList的构建

和linkedList一样，doublyLinkedList是由一个一个的节点构成的。而每个节点除了要存储要保存的数据之外，还需要存储下一个节点和上一个节点的引用。

<img src="../../../../../Download/Typora/image/image-20220814184457994.png" alt="image-20220814184457994" style="zoom:80%;" />

doublyLinkedList需要一个head节点，我们看下怎么构建：

```java
public class DoublyLinkedList {
    Node head; // head 节点
    //Node表示的是Linked list中的节点，包含一个data数据，上一个节点和下一个节点的引用
    class Node {
        int data;
        Node next;
        Node prev;
        //Node的构造函数
        Node(int d) {
            data = d;
        }
    }
}
```

## 头部插入

<img src="../../../../../Download/Typora/image/20200711231151829.gif" alt="20200711231151829" style="zoom:80%;" />

头部插入的逻辑是：将新插入的节点作为新的head节点，并且将newNode.next指向原来的head节点。同时需要将head.prev指向新的插入节点。

```java
//插入到linkedList的头部
public void push(int newData) {
    //构建要插入的节点
    Node newNode = new Node(newData);
    //新节点的next指向现在的head节点
    //新节点的prev指向null
    newNode.next = head;
    newNode.prev = null;
    if (head != null) head.prev = newNode;
    //现有的head节点指向新的节点
    head = newNode;
}
```

## 尾部插入

<img src="../../../../../Download/Typora/image/20200711231502635.gif" alt="20200711231502635" style="zoom:80%;" />

尾部插入的逻辑是：找到最后一个节点，将最后一个节点的next指向新插入的节点，并且将新插入的节点的prev指向最后一个节点。

```java
//新节点插入到list最后面
public void append(int newData) {
    //创建新节点
    Node newNode = new Node(newData);
    //如果list是空，则新节点作为head节点
    if (head == null) {
        newNode.prev = null;
        head = newNode;
        return;
    }
    newNode.next = null;
    //找到最后一个节点
    Node last = head;
    while (last.next != null) {
        last = last.next;
    }
    //插入
    last.next = newNode;
    newNode.prev = last;
    return;
}
```

## 插入给定的位置

<img src="../../../../../Download/Typora/image/202007112317512.gif" alt="202007112317512" style="zoom:80%;" />

如果要在给定的位置插入节点，我们需要先找到插入位置的前一个节点，然后将前一个节点的next指向新节点。新节点的prev指向前一个节点。

同时我们需要将新节点的next指向下一个节点，下一个节点的prev指向新的节点。

```java
//插入在第几个元素之后 index=4 
public void insertAfter(int index, int newData) {
    Node prevNode = head;
    for (int i = 1; i < index; i++) {
        if (prevNode == null) {
            System.out.println("输入的index有误，请重新输入");
            return;
        }
        prevNode = prevNode.next;
    }
    //创建新的节点
    Node newNode = new Node(newData);
    //新节点的next指向prevNode的下一个节点
    newNode.next = prevNode.next;
    //将新节点插入在prevNode之后
    prevNode.next = newNode;
    //将新节点的prev指向prevNode
    newNode.prev = prevNode;
    
    //newNode的下一个节点的prev指向newNode
    if (newNode.next != null)  newNode.next.prev = newNode;
}
```

## 删除指定位置的节点

<img src="../../../../../Download/Typora/image/20200711232055223.gif" alt="20200711232055223" style="zoom:80%;" />

删除节点的逻辑是：找到要删除节点的前一个节点，和下一个节点。前一个节点的next指向下一个节点，下一个节点的prev指向前一个节点。

```java
    //删除特定位置的节点
    void deleteNode(int index)
    {
        // 如果是空的，直接返回
        if (head == null)
            return;

        // head节点
        Node temp = head;

        // 如果是删除head节点
        if (index == 1)
        {
            head = temp.next;
            return;
        }

        // 找到要删除节点的前一个节点
        for (int i=1; temp!=null && i<index-1; i++)
            temp = temp.next;

        // 如果超出范围
        if (temp == null || temp.next == null)
            return;

        // temp->next 是要删除的节点，删除节点
        Node next = temp.next.next;
        temp.next = next;
        Node prev = temp.next.prev;
        prev.prev = prev;
    }
```





