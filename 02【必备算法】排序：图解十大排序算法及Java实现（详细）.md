十种常见排序算法可以分为两大类：

- **比较类排序**：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O(nlogn)，因此也称为非线性时间比较类排序。

- **非比较类排序**：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此也称为线性时间非比较类排序。 

  注：这里说的整体上不是通过比较实现，但局部还是要经过比较或者调用比较排序算法

<img src="https://img-blog.csdnimg.cn/20201007212415819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center" width="70%">

<img src="https://img-blog.csdnimg.cn/20201007232257944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center" width="70%">

下面我们就按照时间复杂度的分成三部分进行图解（下面的动图取自[这篇文章](https://www.cnblogs.com/onepixel/p/7674659.html)），并给出相应Java代码示例..

## 1.O（n²）
### 1.1 冒泡排序
原理：**相邻元素进行比较然后交换**，类似冒泡。在每一轮冒泡中会有许多次交换，最后产生一个最值（在数组最后）
![img](https://img-blog.csdnimg.cn/img_convert/9a152a7e13500cf688efd8e5297f9f6e.png)
```java
 public void sort(int[] arr) {
     	// 找到len-1个最值其实就完成了排序
        for (int i = 0; i < arr.length - 1; i++) {
            // 因为最值元素已经排在后面了，所以要-i；-1是因为arr[j+1]
            for (int j = 0; j < arr.length - 1 - i; j++) {
                if (arr[j] > arr[j + 1]) {
                    int tmp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = tmp;
                }
            }
        }
 }
```
### 1.2 选择排序
原理：不断**寻找最小元素的索引**。每完成一轮寻找，就会找到一个最小值元素索引，然后手动交换到头部
![img](https://img-blog.csdnimg.cn/img_convert/c7322fccb46e3cc0ea3c107781d1e880.png)
```java
public void sort(int[] arr) {
	// 同冒泡，找到len-1个最值就完成了排序
    for (int i = 0; i < arr.length - 1; i++) {
        int minIdx = i; // 用来存储最小元素的索引
        // i+1 是因为前 i 个元素已经是排好序的
        for (int j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[minIdx]) {
                minIdx = j;
            }
        }
		// 交换到前面
        if (minIdx != i) {
            int tmp = arr[i];
            arr[i] = arr[minIdx];
            arr[minIdx] = tmp;
        }
    }
}
```

### 1.3 插入排序

原理：前面的元素都是有序的，对后面元素**寻找合适位置插入到前面**。
1. 保存：记录当前元素
2. 定位：寻找合适位置，同时将前面元素进行不断后移；注：冒泡与选择是交换
3.  插入：找到位置后放入即可
![img](https://img-blog.csdnimg.cn/img_convert/6d867acd56e40fb32975f9e91d041fa9.png)
```java
public void sort(int[] arr) {
	// i从1开始是因为，默认前面元素都是有序的
    for (int i = 1; i < arr.length; i++) {
    	// cur 保存索引i的元素，因为本轮循环就是为了给它寻找正确的位置
    	// preIdx 表示要插入位置的前一位，是在寻找过程中不断变化的
        int cur = arr[i], preIdx = i - 1; 
        
        // 寻找插入位置，同时大于cur的元素后移
        // 要找的位置：前一个元素 arr[pre] <= cur 所以 while（arr[P]>cur）
        while (arr[preIdx] > cur && preIdx >= 0) {
        	// 若没找到，就将当前元素后移一位
            arr[preIdx + 1] = arr[preIdx];
            // preIdx--继续向前寻找
            preIdx--;
        }
        // 找到插入位置了，将cur放入即可
        arr[preIdx + 1] = cur;
    }
}
```

### 1.4 希尔排序
原理：先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序

- 选择一个增量序列t1，t2，…，tk，其中ti>tj，tk=1；
- 按增量序列个数k，对序列进行k 趟排序；
- 每趟排序，根据对应的增量ti，将待排序列分割成若干长度为m 的子序列，分别对各子表进行直接插入排序。仅增量因子为1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

![img](https://img-blog.csdnimg.cn/img_convert/1ee595797336e53d3a40a3e708c2ddf6.png)

```java
	public void sort(int[] arr){
		int len =array.length;
		// 增量
		for(int gap = len/2; gap > 0; gap/=2){
			for(int i = 0; i < gap; i++){
				// 组内采用直接插入法 进行排序
				for (int j = i + gap; j < len; j += gap){
					
					int t = array[j];
					int k = j - gap;
					while(k >= 0 && arr[k] > t){
						arr[k+gap] = arr[k];
						k -= gap;
					}
					arr[k+gap] = t;	
				}
			}
		}
	}
```



## 2.O（n logn）
### 2.1 归并排序

原理:

* **分解**：将大数组不断对半分解，直到单元素数组（如{1}，{2}，{3}）
* **合并**：将小数组进行**排序合并**
  1. 临时数组：创建一个临时数组，存放两个小数组合并后的结果
  2. 比较放入：将小的元素放入，并将当前小元素数组的索引后移，直到一个数组全部放入完毕
  3. 剩余放入：由于结束条件是一个数组放完就行，所以有一个数组还没放入，直接加到临时数组后即可
  4. 数组还原：将临时数组的元素，按原位置放回待排序数组

	![img](https://img-blog.csdnimg.cn/img_convert/2f9f62d486be19b87a3f37f13eb75786.png)
	![在这里插入图片描述](https://img-blog.csdnimg.cn/20201008155226893.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)

```java
public class MergeSort {

    public void sort(int[] arr) {
        if (arr.length < 2) return;
        // 这里是索引，所以right = length-1
        sort(arr, 0, arr.length - 1);
    }

    private void sort(int[] arr, int left, int right) {
        if (left >= right) return;
        // 将当前数组从中间分成2部分
        int mid = (left + right) >> 1;
        sort(arr, left, mid);
        sort(arr, mid + 1, right);
        // 有分治的感觉 --> 数组分解完后，对数组们进行合并
        merge(arr, left, mid, right);
    }
	
    private void merge(int[] arr, int left, int mid, int right) {
        // 1.临时数组  
        int[] tmp = new int[right - left + 1];
        int i = left, j = mid + 1, k = 0;
		// 2.比较放入
        while (i <= mid && j <= right)
            tmp[k++] = arr[i] <= arr[j] ? arr[i++] : arr[j++];
		// 3.剩余放入
        while (i <= mid) tmp[k++] = arr[i++];
        while (j <= right) tmp[k++] = arr[j++];
		// 4.数组还原
        for (int p = 0; p < tmp.length; p++)
            arr[left + p] = tmp[p];
    }
}
```

### 2.2 快速排序
原理：

* **变形-->分区点**：将当前数组变成三部分（小于分区元素、分区元素、大于分区元素），然后返回分区点。
  1. 分区元素：一般分区元素选取数组最后一个元素（end）
  2. 分区移动：依次遍历数组，将比 pivot 小的元素交换到数组前面即可 -> 因此需要counter记录最后小元素位置
  3. 分区点：遍历完数组后的counter就是分区点，将 pivot 与 counter上的元素交换后返回
* **分解**：将当前数组根据分区点进行分解，然后一直这么分解完就排好序了

	![img](https://img-blog.csdnimg.cn/img_convert/eaca66d26efe27519e8c462d3a7d4593.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201008163045749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)




```java
public class QuickSort {
    
    public void sort(int[] arr) {
        if (arr.length < 2) return;
        sort(arr, 0, arr.length - 1);
    }

    private void sort(int[] arr, int begin, int end) {
        if (begin >= end) return;
        // 1.变形 --> 分区点
        int pivot = partition(arr, begin, end);
        // 2.分解：有DFS的感觉 --> 递归树成型就排序好了
        sort(arr, begin, pivot - 1);
        sort(arr, pivot + 1, end);
    }

    private int partition(int[] arr, int begin, int end) {
        // 2.1 分区元素
        // pivot是基准点，counter是分区点
        int pivot = end, counter = begin;
        // 2.2 分区移动
        // 要保证一边比arr[pivot]一边比arr[pivot]大：1.移小的到左边(*) 2.移大的到右边 
        for (int i = begin; i < end; i++) {
            // 这里采用的是只要比arr[pivot]小，就进行交换
            // counter记录要交换位置，会不断++，也是遍历完后的分区点位置
            if (arr[i] < arr[pivot]) {
                int tmp = arr[counter]; arr[counter] = arr[i]; arr[i] = tmp;
                counter++;
            }
        }
        // 2.3 分区点
        // 将最后的基准值（pivot）与分区点（counter）进行交换后返回
        int tmp = arr[pivot]; arr[pivot] = arr[counter]; arr[counter] = tmp;
        return counter;
    }
}
```

### 2.3 堆排序

原理：堆是一个用数组存储的，近似完全二叉树的结构。它一般可以分为最大堆和最小堆，最大堆就是在任何一棵（子）树中，根节点的值最大，最小堆相反。

堆排序原理简言之就是，利用大顶堆和小顶堆的特性，不断取出堆顶（堆中元素的最值），然后再使堆平衡。
1. 构建最大堆：从最后一个不是叶子节点的节点开始下沉
2. 取出堆顶：不是真取出，而是通过交换堆顶（arr[0]）到堆尾（arr[i]）实现出堆顶 ==> **大顶堆的排序结果是从小到大**
3. 新堆再平衡：由于上一步已经将堆尾换到了堆首，所以直接再下沉就行
	![img](https://img-blog.csdnimg.cn/img_convert/93824bbcbb3c16dbfd58a0e64d2c7c3c.png)
	
再说一句，这里采取的是数组从0开始放，即 idx 位置的 parent = idx / 2，leftChild = idx * 2，rightChild = idx * 2 + 1
```java
	public void sort(int[] arr) {
        int length = arr.length;
		
		// 1.构建最大堆：从最后一个不是叶子节点的节点开始下沉
		// i=(length-1)/2，表示获取最后一个叶子节点的父亲，即最后一个不是叶子节点的节点
        for (int i = (length - 1) / 2; i >= 0; i--) {
            siftDown(arr,length, i);
        }
		
        for (int i = length - 1; i > 0; i--) {
        	// 2.取出堆顶：通过交换堆顶（arr[0]）到堆尾（arr[i]），实现出堆顶
            swap(arr, 0, i);
            // 3.新堆再平衡：上一步已经将堆尾换到了堆首，所以直接再下沉就行
            // 注：可以看到这里新堆的长度变成了i（比之前少了1）
            siftDown(arr, i, 0);
        }
    }

    private void siftDown(int[] arr, int length, int idx) {
    	// 下沉到叶节点，就没有儿子了，就不用下沉了
    	// 这里用的leftChild（2*idx），因为如果左儿子都超过length了，右儿子也一定超过了
        while (2 * idx < length) {
        	
        	// 判断左儿子和右儿子哪个大
            int j = 2 * idx;
            if (j + 1 < length && arr[j+1] > arr[j])
                j++; // 如果右儿子大，就j++
			
			// 判断父亲是否比儿子大
            if (arr[idx] >= arr[j])
                break;
			
			// 父亲小于儿子，那么就交换父子
            swap(arr, idx, j);
            // 更新父亲idx，继续去下一棵子树判断下沉
            idx = j;
        }
    }

    private void swap(int[] arr, int idx1, int idx2) {
        int t = arr[idx1];
        arr[idx1] = arr[idx2];
        arr[idx2] = t;
    }
```
更多相关操作和Java实现可以参考[二叉堆：Java实现最大堆及堆排序](https://blog.csdn.net/weixin_43935927/article/details/108859896)...

## 3.O（n）
### 3.1 桶排序
原理：有 n 个桶且桶间是有序的，所有元素按大小关系入桶，桶内排序后总体有序

1. 桶个数 = 数组范围 / 桶大小 + 1；注：+1是为了考虑余数也要放入一个桶
2. 创建桶
   1. 桶（buckets）：二维数组，i 表示桶号，j 表示桶内序号
   2. 桶容量数组（idxArr）：一维数组，用来记录每个桶存了多少个元素了，下次就放在它后面，满了就扩容
3. 将数组放入桶
   1. 计算桶号：元素 / 桶个数；注：这里必须是 / ，因为要保证小数进小桶，大数进大桶
   2. 判断当前桶是否满了，满了就扩容（即新建一个大的一维数组放入当前索引）
   3. 将元素放入，并将 idxArr[bucketIdx]++
4. 桶内排序，并返回原数组
   1. 排序
   2. 将排好序的桶内元素放回，因为小数小桶而且所有元素都在桶内存过了，所以直接放回即可

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201008201231889.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
```java
public class BucketSort {

    public void sort(int[] arr, int bucketSize) throws Exception {
        // 1.计算桶个数
        // 1.1 寻找数组最大值与最小值
        if (arr.length < 2) return;
        int minValue = arr[0], maxValue = arr[0];
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] < minValue) minValue = arr[i];
            if (arr[i] > maxValue) maxValue = arr[i];
        }
        // 1.2 计算桶个数，+1是为了保证余数也能进桶
        int bucketCount = (maxValue - minValue) / bucketSize + 1;
        // 2.创建桶
        // 2.1 buckets：i表示桶号，j表示桶内序号
        int[][] buckets = new int[bucketCount][bucketSize];
        // 2.2 idxAarr：用来存每个桶内现在存到哪了，即j实际到哪了
        int[] idxArr = new int[bucketCount];
        // 3.将数组元素放入桶
        for (int i = 0; i < arr.length; i++) {
            // 3.1 计算桶号：这里切记不能取余，除是大的数结果就大，符合小数进小号，大数进大号
            int bucketIdx = (arr[i] - minValue) / bucketSize;
            // 3.2 判断扩容：当前桶满了，要扩容当前桶
            if (idxArr[bucketIdx] == buckets[bucketIdx].length) {
                ensureCapacity(buckets, bucketIdx);
            }
            // 3.3 放入
            buckets[bucketIdx][idxArr[bucketIdx]++] = arr[i];
        }
        // 4.桶内排序,并放回原数组
        int k = 0;
        for (int i = 0; i < buckets.length; i++) {
            if (idxArr[i] == 0) {
                continue;
            }
            //3.1 排序
            // 注：这里不能Arrays.sort(buckets[i])，因为sort排的是所有元素（length个），
            // 包括未放入元素位置的默认0，而实际要排的元素个数是idxArr[i]
            // 所以这里采用快排，所以 end = idxArr[i] - 1，而不是lenth-1
            quickSort(buckets[i], 0, idxArr[i] - 1);
            // 3.2 放回原数组：这里直接依次往后放就行（k），因为桶间是有序的
            for (int j = 0; j < idxArr[i]; j++) {
                arr[k++] = buckets[i][j];
            }
        }
    }

    private void ensureCapacity(int[][] buckets, int bucketIdx) {
        int[] tmpArr = buckets[bucketIdx];
        int[] newBucket = new int[tmpArr.length * 2];
        for (int i = 0; i < tmpArr.length; i++) {
            newBucket[i] = tmpArr[i];
        }
        // 将新建的大一维数组放入原位置
        buckets[bucketIdx] = newBucket;
    }

    private void quickSort(int[] bucket, int begin, int end) {
        if (begin >= end) return;

        int pivot = partition(bucket, begin, end);
        quickSort(bucket, begin, pivot - 1);
        quickSort(bucket, pivot + 1, end);
    }

    private int partition(int[] bucket, int begin, int end) {
        int pivot = end, counter = begin;
        for (int i = begin; i < end; i++) {
            if (bucket[i] < bucket[pivot]) {
                int tmp = bucket[i]; bucket[i] = bucket[counter]; bucket[counter] = tmp;
                counter++;
            }
        }
        int tmp = bucket[counter]; bucket[counter] = bucket[pivot]; bucket[pivot] = tmp;
        return counter;
    }
}
```
Time = O（n）原理：桶排序也借助了比较的排序算法（快排，O(nlogn)），就是因为每个桶里的元素太少了那个logn 可以被忽略，而桶间又不用排序，所以时间复杂度是O（n）
### 3.2 计数排序
原理：计数排序是特殊的桶排序，所有桶中存的元素一样（索引值），因此桶只用记录有多少个元素即可

1. 桶个数：数组最大值个；因为要用索引值（桶号）表示数组元素大小
2. 创建桶
3. 将数组元素放入桶们
   1. 将数组元素按照大小匹配下标（桶号），然后将对应桶记录的元素个数++
   2. 放入完成后，将桶中的数据累加 --> 桶号=数组元素，桶元素=小于等于当前桶号的元素个数
4. 通过临时数组完成排序
   1. 计算下标：有序数组下标 = 桶元素 - 1
   2. 放入临时数组：`tmp[桶元素 - 1] = 桶号 = arr[i]`
   3. 对应桶元素--
5. 拷贝临时数组到待排序数组

![img](https://img-blog.csdnimg.cn/img_convert/25fdb31acd81f931cd443cb932140ab9.png)
```java
public void sort(int[] arr) {
        if (arr.length < 2) return;
        // 1.计算桶个数，数组元素最大值 + 1
        int maxValue = arr[0];
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] > maxValue) maxValue = arr[i];
        }
    	// 2.创建桶
        int[] buckets = new int[maxValue + 1];
        // 3.将原数组元素放入桶们
    	// 3.1 匹配对应桶号，桶元素++
        for (int i = 0; i < arr.length; i++) {
            buckets[arr[i]]++;
        }
        // 3.2 将桶元素依次累加 --> 桶号=数组元素，桶元素=小于等于当前桶号的元素个数
    	// --> 有序数组下标 = 桶元素 - 1，-1因为记录的是个数
        for (int i = 1; i < buckets.length; i++) {
            buckets[i] = buckets[i] + buckets[i - 1];
        }
        // 4.通过临时数组完成排序
        int[] tmp = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            // 4.1 计算数组下标：有序数组下标 = 桶元素 - 1； 
            int idx = buckets[arr[i]] - 1;
            // 4.2 放入临时数组，tmp[桶元素 - 1] = 桶号 = arr[i]
            tmp[idx] = arr[i];
            // 4.3 对应桶元素--
            buckets[arr[i]]--;
        }
        // 5. 拷贝临时数组到待排序数组
        for (int i = 0; i < arr.length; i++) {
            arr[i] = tmp[i];
        }
}
```

### 3.3 基数排序

原理：基数排序是增强的桶排序，将待排数据按位进桶，先排低位再排高位（前提：**基于稳定排序算法**）

1. 寻找最大位数：通过找到待排数组的最大值来确定最大位数
2. 初始化10个桶：初始化10个桶，代表每位的0-9
3. 基数排序：以位数作为外层循环
   1. 按当前位的大小，将所有元素放入相应桶（如按第1位：1234进4桶，789进9桶）
   2. 将所有元素按桶取出，放回原数组
   3. 进行按下一位排序
   
![img](https://img-blog.csdnimg.cn/img_convert/88cb17eb223c8838182dcaff3dfcde17.png)
这里再放一个示例把：排序【219,180,247,192,561,254,732,639,548,294,91,596,162】
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201008200739247.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
```java
// Time：O（k*n），k是数字位数（虽然是嵌套循环，但外层k与数据规模无关）
public void radixSort(int[] arr) {
    // 寻找最大值
    int max = arr[0];
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] > max) 
            max = arr[i];
    }    	
	// 寻找arr中最大数字的位数
    int keyNum = 0;
    while (max > 0) {
        max /= 10;
        keyNum++;
    }    
    // 初始化10个小桶，代表位数0-9
    List<List<Integer>> buckets = new ArrayList<>();
    for (int i = 0; i < 10; i++) {
        buckets.add(new ArrayList<Integer>());
    }
    
    // 开始基数排序，位数作为外层循环
    for (int i = 0 ; i < keyNum; i++) {
        // 1.遍历数组，按i+1位放入相应桶（因为i从0开始）
        for (int j = 0; j < arr.length; j++) {
            // 例：1234的第3位 = 1234 / 100 % 10 = 12 % 10 = 2
            int key = arr[j] / (int)Math.pow(10, i + 1) % 10;
            buckets.get(key).add(arr[j]);
        }
        
        // 2.将按i+1位排序的结果放回原数组，准备进入下一轮排序
        // 注：这里counter是为了记录原数组下标
        int counter = 0;
        for (int j = 0; j < 10; j++) {
            List<Integer> bucket = buckets.get(j);
            while (bucket.size() > 0) {
                arr[counter++] = bucket.remove(0);
            }
        }
    }
}
```

