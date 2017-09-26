 
####1. accept在三次握手之后
####2. 静态链接库（在编译时）和动态链接库（在运行时）的区别
####3. 模拟加减法
####4. 索引
####5. epoll
####6. 设计模式
####7. Socket过程：
####8. 流水线
####9. *p++与*++p，int* p = (int*)(&a+1) , char ** a = {"hello","wordld","hi","bye"}
p是个数组，所以 sizeof(p) 返回数组的占用大小，64 位情况下
```
【char **p[4] 】 表示 【char** 数组】 返回 4 * 8 = 32

【char *(*p)[4] 】 表示 【指向 char* 数组的指针】 返回 8

【char （**p） [4]?】 表示 【指向?char 数组的指针的指针】?返回 8
```

####10. 字符串复制函数：springtf, memcpy, strcpy, strstr（strstr是查找子串函数）
####11. 多线程的那点儿事（之读写锁）:copyOnWrite，分区锁的问题

#### 12. grep命令 
统计文件或者文本中包含匹配字符串的行数 -c 选项：` grep -c "text" file_name `
输出包含匹配字符串的行数 -n 选项：` grep "text" -n file_name ` 或 ` cat file_name | grep "text" -n `
多个文件：` grep "text" -n file_1 file_2 `
#### find命令

#### sizeof运算符
int a[10]={...} ; int* p = a;
数组名a是一个常量指针，sizeof(a)返回数组总个数
sizeof(*p)返回第一个元素所占用的字节数

#### strlen 和 sizeof
strlen()计算字符数组的字符数，以"\0"为结束判断，不计算为'\0'的数组元素。
sizeof计算数据（包括数组、变量、类型、结构体等）所占内存空间，用字节数表示。
##### sizeof(指针)经典问题：         
所有指针都是占4字节 (64位8字节)
```C++
double* (*a)[3][6];         
cout<<sizeof(a)<<endl; // 4 a为指针        
cout<<sizeof(*a)<<endl; // 72 *a为一个有3*6个指针元素的数组       
cout<<sizeof(**a)<<endl; // 24 **a为数组一维的6个指针       
cout<<sizeof(***a)<<endl; // 4 ***a为一维的第一个指针       
cout<<sizeof(****a)<<endl; // 8 ****a为一个double变量
```
##### 使用sizeof时string的注意：string s="hello";     
sizeof(s)等于string类的大小，sizeof(s.c_str())得到的是与字符串长度

##### union 与struct的空间计算    
总体上遵循两个原则：     
(1)整体空间是 占用空间最大的成员（的类型）所占字节数的整倍数    
(2)数据对齐原则----内存按结构成员的先后顺序排列，当排到该成员变量时，其前面已摆放的空间大小必须是该成员类型大小的整倍数，如果不够则补齐，以此向后类推
注意：数组按照单个变量一个一个的摆放，而不是看成整体。如果成员中有自定义的类、结构体，也要注意数组问题。

##### 结构体的陷阱
这里有个陷阱，对于结构体中的结构体成员，不要认为它的对齐方式就是他的大小，看下面的例子： struct s1 {  char a[8]; }; 
```C++
struct s2 {  double d; };   
struct s3 { s1 s; char a; };   
struct s4 { s2 s; char a;  };  
cout<<sizeof(s1)<<endl; // 8 
cout<<sizeof(s2)<<endl; // 8 
cout<<sizeof(s3)<<endl; // 9 
cout<<sizeof(s4)<<endl; // 16;    
```
s1和s2大小虽然都是8，但是s1的对齐方式是1，s2是8（double），所以在s3和s4中才有这样的差异。

#### 指针常量与常量指针
##### 常量指针
常量指针限制了通过这个指针修改变量的值的可能
1）const int *p;
2）int const *p;
指针指向的对象不能通过这个指针来修改，可是仍然可以通过原来的声明修改，也就是说常量指针可以被赋值为变量的地址
> 特点是，const的位置在指针声明运算符*的左侧。只要const位于*的左侧，无论它在类型名的左边或右边，都声明了一个指向常量的指针，叫做常量指针。

##### 指针常量
指针常量的本质是一个常量，说明这个常量的值应该是一个指针
指针常量的值是指针，这个值因为是常量，所以不能被赋值。
 
> int *const b = &a; //const放在指针声明操作符的右侧
声明了一个常量b，它的值是变量a的地址（变量a的地址，不就是指向变量a的指针吗）。
因为指针常量是一个常量，在声明的时候一定要给它赋初始值。一旦赋值，以后这个常量再也不能指向别的地址。
虽然指针常量的值不能变，可是它指向的对象是可变的，因为我们并没有限制它指向的对象是常量。

##### 指向常量的指针常量
顾名思议，指向常量的指针常量就是一个常量，且它指向的对象也是一个常量。
> const int * const b = &a;
左边有一个const，说明声明的是一个指向常量的指针
右边有一个const，说明声明的是一个指针常量
前后都锁死了，那么指向的对象不能变，指针常量本身也不能变。

#### 数组指针和指针数组
##### 数组指针（也称行指针）
- `	int (*p)[n];` 数组指针也称指向一维数组的指针，亦称行指针 
()优先级高，首先说明p是一个指针，指向一个整型的一维数组，这个一维数组的长度是n，也可以说是p的步长。也就是说执行p+1时，p要跨过n个整型数据的长度

- 如要将二维数组赋给一指针，应这样赋值：
```C++
	int a[3][4];  
	int (*p)[4]; //p只是一个变量，变量的值是指向含4个元素的一维数组。说明它的步长是4
	 p=a;        //将该二维数组的首地址赋给p，也就是a[0]或&a[0][0]
	 p++;       //该语句执行过后，也就是p=p+1;p跨过行a[0][]指向了行a[1][]
```


##### 指针数组
- `	int *p[n]; `
[]优先级高，先与p结合成为一个数组，再由int*说明这是一个整型指针数组，它有n个指针类型的数组元素。这里执行p+1时，则p指向下一个数组元素
> 这样赋值是错误的：p=a；因为p是个不可知的表示，只存在p[0]、p[1]、p[2]...p[n-1],而且它们分别是指针变量可以用来存放变量地址
>> 但可以这样 *p=a; 这里*p表示指针数组第一个元素的值，a的首地址的值

- 如要将二维数组赋给一指针数组:
```C++
	int *p[3];
	int a[3][4];
	p++; //该语句表示p数组指向下一个数组元素。注：此数组每一个元素都是一个指针
	for(i=0;i<3;i++)
		p[i]=a[i];
```
这里int *p[3] 表示一个一维数组内存放着三个指针变量，分别是p[0]、p[1]、p[2]
所以要分别赋值。
##### a 与 &a
设 ` char a[5] = {'a'....'e'}; `
&a是整个数组的首地址，a是数组首元素的首地址，其值相同但意义不同
a的类型是char *
&a的类型是char (*)[5]，可以强制转换 (char (*)[5])a;
> 所以a+1是下一个元素，而&a+1是数组全部元素后的地址（即&a的步长是数组长度5）

##### 地址强制转换
设p的值为0x100000,指向一个结构体Test，结构体大小为20byte
- ` p + 0x1 的值为0x100000+sizof（Test）*0x1 ` = 0x100014

- (unsigned long)p + 0x1 = 0x100001
这里涉及到强制转换，将指针变量p 保存的值强制转换成无符号的长整型数
任何数值一旦被强制转换，其类型就改变了。所以这个表达式其实就是一个无符号的长整型数加上另一个整数

- (unsigned int*)p + 0x1 = 0x100004
p 被强制转换成一个指向无符号整型的指针，sizeof(指针)为4

```C++
   int *ptr1=(int *)(&a+1);//指向a数组后面的内存单元，&a+1表示向后移16个存储单元
   //ptr1将&a+1 的值强制转换成int*类型，赋值给int* 类型的变量ptr，ptr1 肯定指到数组a 的下一个int 类型数据了。ptr1[-1]被解析成*(ptr1-1)，即ptr1 往后退4 个byte。所以其值为0x4。

   int *ptr2=(int *)((int)a+1);//表示a的存储单元的地址增加一个字节
   //ptr2：按照上面的讲解，(int)a+1 的值是元素a[0]的第二个字节的地址。然后把这个地址强制转换成int*类型的值赋给ptr2，也就是说*ptr2 的值应该为元素a[0]的第二个字节开始的连续4 个byte 的内容

   printf("%x,%x",ptr1[-1],*ptr2);
//ptr1[-1]其实指向的是a数组的最后一个单元，*ptr1则表示a数组的地址后移一个字节之后的4个连续存储单元所存储的值
```
![解析](http://images.cnitblog.com/blog/256716/201310/22201259-c35f74944aa94ee4a893047ee9c23ded.jpg)




##### 两者总结 
` 优先级：() > [] > * == ++ `
- 数组指针只是一个指针变量，似乎是C语言里专门用来指向二维数组的，它占有内存中一个指针的存储空间
- 指针数组是多个指针变量，以数组形式存在内存当中，占有多个指针的存储空间。
- 还需要说明的一点就是，同时用来指向二维数组时，其引用和用数组名引用都是一样的。
比如要表示数组中i行j列一个元素：
` *(p[i]+j)、*(*(p+i)+j)、(*(p+i))[j]、p[i][j] `
> *(*(p+i)+j) 可以看出p本身移动步长是j，即是一行一行移动的

#### 大小端模式
大端模式，是指数据的高字节保存在内存的低地址中，而数据的低字节保存在内存的高地址中，这样的存储模式有点儿类似于把数据当作字符串顺序处理：地址由小向大增加，而数据从高位往低位放
> Big-Endian: 低地址存放高位，如下：
高地址
　　---------------
　　buf[3] (0x78) -- 低位
　　buf[2] (0x56)
　　buf[1] (0x34)
　　buf[0] (0x12) -- 高位
　　---------------
　　低地址
> Little-Endian: 低地址存放低位，如下：
高地址
　　---------------
　　buf[3] (0x12) -- 高位
　　buf[2] (0x34)
　　buf[1] (0x56)
　　buf[0] (0x78) -- 低位
　　--------------
低地址

##### 监测大小端
```C++
short int x；
char x0,x1;
x=0x1122;
x0=((char*)&x)[0]; //低地址单元
x1=((char*)&x)[1]; //高地址单元
```
若x0=0x11,则是大端; 若x0=0x22,则是小端 




#### 快速排序：递归与非递归
##### 递归法
```C++
template <typename Comparable>
int partition(vector<Comparable> & vec,int low,int high){
	Comparable pivot = vec[low] //任意index当基准
	while( low < high ){
		while( low < high && vec[high] >= pivot ){ // find first lower than pivot
			high--;
		vec[low] = vec[high];
		while( low < high )
			low++;
		vec[high] = vec[low];
	}
	//当low == high
	vec[low] = pivot;
	return low;  //返回最后基准位置
}
template <typename Comparable>
void QuickSort_Recursion(vector<Comparable> & vec, int low ,int high){
	if( low < high ){
		int mid = partition(vec,low,high);
		QuickSort_Recursion(vec,low,mid-1);
		QuickSort_Recursion(vec,mid+1,high);
	}
}

```
##### 非递归法
```C++
template <typename Comparable>
void QuickSort_Recursion(vector<Comparable> & vec, int low ,int high){
	if( low < high ){
		stack<int> s;
		if( low < high ){
			int mid = partitio(vec,low,high);
			if( low < mid - 1 ){
				s.push(low);
				s.push(mid-1);			
			}
			if( mid + 1 < high ){
				s.push(mid+1);
				s.push(high);			
			}
		}
		while(!s.empty()){
			// 取栈头的两个数p,q
			//
		QuickSort_Recursion(vec,mid+1,high);
			int q = s.top();
			s.pop();
			int p = s.top();
			s.pop()
			mid = partition(vec,p,q);
			if( p < mid-1 ){
				s.push(p);
				s.push(mid-1);				
			} 
			if( mid + 1 < q){
				s.push(mid+1);
				s.push(q);				
			}
		}
	}	
}
```
##### 时间复杂度
最差情况，已排好序，排序树退化成链表，每次一个子表为空，另一个为n-1，这样需要n-1趟，每趟n-i次比较，所以一共需要 ( n * ( n-1 )) / 2 次，退化到冒泡水平
一般情况，递归调用需要 O(NlogN) = cn + 2*T(n/2) 每次平分为两个表

##### 空间复杂度
最差情况，已排好序，排序树退化成链表，需要O(N)
一般情况，递归调用需要O(logN)

#### 归并排序
##### 空间复杂度降到O(1)：手摇算法
但是最差情况时间复杂度会变成O(N^2)

#### 堆排序
每次从堆里选最大/最小，与末尾元素交换（固定到末尾），再调整
O(NlogN)堆排序复杂性，
  
#### `*p++` 与 `*++p`
```C++
*p++ 先取指针p指向的值（数组第一个元素1），再将指针p自增1；
        cout << *p++; //  结果为 1
        cout <<(*p++);  // 1
(*p)++ 先去指针p指向的值（数组第一个元素1），再将该值自增1（数组第一个元素变为2
        cout << (*p)++; //  1
        cout <<((*p)++)  //2
*++p   先将指针p自增1（此时指向数组第二个元素），* 操作再取出该值
        cout << *++p; //  2
        cout <<(*++p)  //2
++*p  先取指针p指向的值（数组第一个元素1），再将该值自增1（数组第一个元素变为2）
      cout <<++*p; //   2    
        cout <<(++*p)  //2
```
注意，上面的每条cout输出，要单独输出才能得到后面的结果
### C++ vector必须先push_back
vector必须先push_back再用vec[i]去取，不然会运行报错，当然vec.get也是可以的

#### C++ 注意 gets与scanf交替，cin与getline交替时可能会吞字
```C++
	scanf("%d\n",&m); //注意'\n'
	cin>>k;
```
gets与scanf交替，cin与getline交替时可能会吞字


#### C++ String与char[]与`char*`
##### string转char[]
首先不可以printf("%s",string)，因为打印寻找的是地址，而string不是一个对象，运行会出错
###### string注意事项
- string可以直接比较 ==，而char[] 需要strcmp(const char *,const char *)
- string不能和NULL进行比较，判断string为空有：
```C++
    string test_string;
    string test_string_empty = "";	

    if (test_string == "") 
    if (test_string_empty.empty()) 
    if (test_string_empty.length() == 0)   

```

###### string.c_str()与string.data()
- data()是末尾不会跟'\0'的
- 不能直接 char[] a = string.c_str()，a只是获得了一个引用，在string被销毁后就不知道指到哪了
- 为内容赋值才可以，需要 char[] a = strcpy(string.c_str())

##### char[] 转 string
直接赋值

#### 字符串复制
sprintf 可以进行额外的格式化
strcpy 会复制直到出现 '' 为止，可能溢出
strncpy 会复制一个以 '' 结束的字符串，但是如果字符串长度超过指定数量则被截断，但结果可能不包含 '' 表示结束
memcpy 只负责复制指定数量的 bytes，不处理 '' 的情况
memmove 在 memcpy 的基础上对 overlap 的情况进行了处理

#### CP握手讲到 listen fd 的两个队列了吗?(就绪队列,未就绪队列), syn-flood问题, accept需要几次握手

#### C++面试
http://www.cnblogs.com/fangyukuan/archive/2010/09/18/1829871.html

#### 数据库中的左连接(left join)和右连接(right join)区别

#### 数据库索引的B Tree和Hash实现的区别

#### B树与B+树区别

####高精度浮点数

####p2p服务器


####udp怎么保证能收到数据


