##排序算法
###分类结构图
![http://my.csdn.net/uploads/201207/17/1342514529_5795.jpg](http://my.csdn.net/uploads/201207/17/1342514529_5795.jpg)
###复杂度
![http://images.cnitblog.com/blog/512692/201304/12095757-775cf861406644bfad60ff2763f499e4.png](http://images.cnitblog.com/blog/512692/201304/12095757-775cf861406644bfad60ff2763f499e4.png)

![](http://img.blog.csdn.net/20170223164454109?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2d5c2NzZg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


###分类 
######交换冒泡排序


	import  java.util.Arrays;
	public class BubbleExchangeSort{
		public static void main(String[] args){
			int[] arr=new int[]{5,7,1,3,5,1,0,6};
			sort(arr);
			System.out.println(Arrays.toString(arr));
		}
		private static void sort(int[] arr){
			for(int i=0;i<arr.length-1;i++){
				for(int j=i+1;j<arr.length;j++){
					if(arr[j]<arr[i]){
						int temp=arr[j];
						arr[j]=arr[i];
						arr[i]=temp;
					}
				}
			}
		}
	}

######交换快速排序
* 动画 [http://v.youku.com/v_show/id_XMzMyODk4NTQ4.html](http://v.youku.com/v_show/id_XMzMyODk4NTQ4.html "http://v.youku.com/v_show/id_XMzMyODk4NTQ4.html")

* 徒手写代码
	* 第一次写出错地方：improt java.utils.arrays--->improt java.util.Arrays
	* sort(arr,0,arr.length);---->sort(arr,0,arr.length-1);


	import java.util.Arrays;
	
	public class QuickChangeSort{
		//切分算法
		private static int partition(int[] arr,int lo,int hi){
			int key=arr[lo];
			while(lo<hi){
				while(arr[hi]>=key&&lo<hi)
					hi--;
				arr[lo]=arr[hi];
				while(arr[lo]<=key&&lo<hi)
					lo++;
				arr[hi]=arr[lo];
			}
			arr[hi]=key;
			return hi;
		}	
		
		//排序算法
		private static void sort(int[] arr,int lo,int hi){
			//终止条件以及异常处理
			if(arr==null||lo>=hi)return;
			int index=partition(arr,lo,hi);
			sort(arr,lo,index-1);
			sort(arr,index+1,hi);
		}
			
		//test
 		public static void main(String[] args){
			int[] arr=new int[]{5,4,8,6,7,9};
			sort(arr,0,arr.length-1);
			System.out.println(Arrays.toString(arr));
		}

	}



######插入直接排序
* 思路：
	* 第一步：从第二个元素开始一次加一，依次和前面所有元素比较。
	* 第二步：若该元素小于比较的元素，前面的元素后移；接着比较前面的元素，接着后移。不满足“小于”的条件，将该元素插入到空下的位置。
	* 重复一二步骤。

* 实现

		import java.util.Arrays;
		public class DircetInsertSort{
			public static void main(String[] args){
				int[] arr=new int[]{5,3,1,4,2,6,9};
				sort(arr);
				System.out.println(Arrays.toString(arr));
			}
			
			//直接插入排序
			private static void sort(int[] arr){
				//标记待比较的值
				int temp=0;
				for(int i=1;i<arr.length;i++){
					temp=arr[i];
					int j;
					for(j=i-1;j>=0;j--){
						//注意这个比较条件，和待比较数据比较
						if(temp<arr[j]){
							//是所有大于待比较数据都要后移
							arr[j+1]=arr[j];
						}else break;
					}
					arr[j+1]=temp;
				}
			}
		
		}



######插入希尔排序排序
	
	import java.util.Arrays;
	public class ShellInsertSort{
		public static void main(String[] agrs){
			int[] arr=new int[]{4,5,2,7,6,27,2,8,2,5};
			sort(arr);
			System.out.println(Arrays.toString(arr));
		}
		/*	0. 获取数组长度：int a=数组长度;
			1.a的一半为增量值。
			2. 以该增量为基数，对比数据，将小的数据交换到前面。遍历所有数据。
			3. 重复1、2步骤。终止条件：增量值小于1。
		*/
		private static void sort(int[] arr){
			//获取初试增量值
			int incrementNum=arr.length/2;
			while(incrementNum>0){
				for(int i=0;i<arr.length;i++){
					for(int j=i+incrementNum;j<arr.length;j+=incrementNum){
						if(arr[j]<arr[i]){
							int temp=arr[i];
							arr[i]=arr[j];
							arr[j]=temp;
						}
					}
				}
				incrementNum/=2;
			}
		}
	}



######选择简单排序
* 思路：每轮比较出最小的值的索引，然后和待比较值交换位置。接着比较下一个，一直进行即可。


	import java.util.Arrays;
	public class SimpleChooseSort{
		//简单选择算法
		private static void sort(int[] arr){
			int minIndex=0;
			for(int i=0;i<arr.length-1;i++){
				minIndex=i;
				for(int j=i+1;j<arr.length;j++){
					// 这里是和arr[min]比较，不是和arr[i]比较
					if(arr[j]<arr[minIndex]){
						minIndex=j;
					}
				}
				if(minIndex!=i){
					int temp=arr[i];
					arr[i]=arr[minIndex];
					arr[minIndex]=temp;
				}
			}
		}
		public static void main(String[] args){
			int[] arr=new int[]{5,7,1,3,5,1,0,6};
			sort(arr);
			System.out.println(Arrays.toString(arr));
		}
	}


######选择堆排序

		/*
		 * 堆选择排序
		 */
		public static void heapChooseSort(int[] array) {
			buildHeap(array);// 构建堆
			int n = array.length;
			int i = 0;
			for (i = n - 1; i >= 1; i--) {
				swap(array, 0, i);
				heapify(array, 0, i);
			}
		}
	
		public static void buildHeap(int[] array) {
			int n = array.length;// 数组中元素的个数
			for (int i = n / 2 - 1; i >= 0; i--)
				heapify(array, i, n);
	
		}
	
		public static void heapify(int[] A, int idx, int max) {
			int left = 2 * idx + 1;// 左孩子的下标（如果存在的话）
			int right = 2 * idx + 2;// 左孩子的下标（如果存在的话）
			int largest = 0;// 寻找3个节点中最大值节点的下标
			if (left < max && A[left] > A[idx])
				largest = left;
			else
				largest = idx;
			if (right < max && A[right] > A[largest])
				largest = right;
			if (largest != idx) {
				swap(A, largest, idx);
				heapify(A, largest, max);
			}
	
		}
	
		public static void swap(int[] array, int i, int j) {
			int temp = 0;
			temp = array[i];
			array[i] = array[j];
			array[j] = temp;
		}

######归并排序
	
		import java.util.Arrays;
		public class MergeSort{
			
			/*
			 * 思路：以mid为分界线，左右两边依次比较，取小的放到临时数组中。直到所有全部处理完。再把临时数据放到arr[lo,hi]中。
			 * 1. 在满足lo<hi的前提下，取左开始指针lp=lo，右指针rp=mid+1;
			 * 2. 左右指针的值对比，取小的放到临时数组中，所对应指针加一，临时数组指针加一。
			 * 3. 
			 */
			private static void merge(int[] arr,int lo,int mid,int hi){
				//临时数组
				int[] tempArr=new int[hi-lo+1];
				//临时数组下标
				int index=0;
				//左右指针
				int lp=lo,rp=mid+1;
				while(lp<=mid&&rp<=hi){
					if(arr[lp]<arr[rp]){
						tempArr[index++]=arr[lp++];
					}else{
						tempArr[index++]=arr[rp++];
					}
				}
				
				while(lp<=mid)
					tempArr[index++]=arr[lp++];
				while(rp<=hi)
					tempArr[index++]=arr[rp++];
				
				//归并
				for(int i=0;i<tempArr.length;i++){
					arr[lo++]=tempArr[i];
				}
			}
			
			private static void sort(int[] arr,int lo,int hi){
				if(arr==null||lo>=hi)return;
				int mid=(lo+hi)/2;
				sort(arr,lo,mid);
				sort(arr,mid+1,hi);
				merge(arr,lo,mid,hi);
			}
			
			public static void main(String[] agrs){
				int[] arr=new int[]{4,5,2,7,6,27,2,8,2,5};
				sort(arr,0,arr.length-1);
				System.out.println(Arrays.toString(arr));
			}
		}



######基数排序

	/*
	 * 基数排序法
	 */
	public static void radixSort(int[] number, int d) {
		int k = 0;
		int n = 1;
		int m = 1;
		int[][] temp = new int[number.length][number.length];
		int[] order = new int[number.length];
		while (m <= d) {
			for (int i = 0; i < number.length; i++) {
				int lsd = ((number[i] / n) % 10);
				temp[lsd][order[lsd]] = number[i];
				order[lsd]++;
			}
			for (int i = 0; i < d; i++) {
				if (order[i] != 0)
					for (int j = 0; j < order[i]; j++) {
						number[k] = temp[i][j];
						k++;
					}
				order[i] = 0;
			}
			n *= 10;
			k = 0;
			m++;
		}
	}