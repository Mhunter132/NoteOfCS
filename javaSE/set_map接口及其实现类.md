---
title:  set/map接口及其实现类
date: 2019-11-10
categories: 
- SE
tags: 
- 笔记
---

## 1.Map接口/Hashtable、HashMap、TreeMap实现类

![img](https://gitee.com/kirk_zhang/KirkzhangBlog/raw/master/images/set-map接口及其实现类/map接口.jpg)

Hashtable、HashMap、TreeMap 都是最常见的一些 Map 实现，是以键值对的形式存储和操作数据的容器类型。Hashtable 是早期 Java 类库提供的一个哈希表实现，本身是同步的，不支持 null 键和值，由于同步导致的性能开销，所以已经很少被推荐使用。HashMap 是应用更加广泛的哈希表实现，行为上大致上与 HashTable 一致，主要区别在于HashMap 不是同步的，支持 null 键和值等。通常情况下，HashMap 进行 put 或者 get 操作，可以达到常数时间的性能，所以它是绝大部分利用键值对存取场景的首选，比如，实现一个用户 ID 和用户信息对应的运行时存储结构。**TreeMap 则是基于红黑树**的一种提供顺序访问的 Map，和 HashMap 不同，它的 get、put、remove 之类操作都是 O（log(n)）的时间复杂度，具体顺序可以由指定的 Comparator 来决定，或者根据键的自然顺序来判断

HashMap 等其他 Map 实现则是都扩展了 AbstractMap，里面包含了通用方法抽象。不同Map 的用途，从类图结构就能体现出来，设计目的已经体现在不同接口上。大部分使用 Map 的场景，通常就是放入、访问或者删除，而对顺序没有特别要求，HashMap在这种情况下基本是最好的选择。HashMap 的性能表现非常依赖于哈希码的有效性，

遵守 hashCode 和 equals 的一些基本约定，比如：

- equals 相等，hashCode 一定要相等。
- 重写了 hashCode 也要重写 equals。
- hashCode 需要保持一致性，状态改变返回的哈希值仍然要一致。equals 的对称、反射、传递等特性。

针对有序 Map 的分析内容比较有限，我再补充一些，虽然 LinkedHashMap 和 TreeMap 都可以保证某种顺序，但二者还是非常不同的LinkedHashMap 通常提供的是遍历顺序符合插入顺序，它的实现是通过为条目（键值对）维护一个双向链表。注意，通过特定构造函数，我们可以创建反映访问顺序的实例，所谓的put、get、compute 等，都算作“访问”。这种行为适用于一些特定应用场景，例如，我们构建一个空间占用敏感的资源池，希望可以自动将最不常被访问的对象释放掉，这就可以利用 LinkedHashMap 提供的机制来实现

---------------摘自《Java核心技术36讲》

--------------------2019/05/08更新------------------------------------

- HashMap存储自定义对象需要重写equals和hashcode方法
- LindedHashMap存储自定义对象需要重写equals和hashcode方法
- TreeMap存储自定义对象需要写内/外部比较器

--------------------2019/05/08更新------------------------------------

## 2.set内/外部比较器

当一个自定义对象实现Comparable并重写compareTo方法时，通过指定具体的比较策略，此时称为内部比较器。

```text
public class Student implements Comparable<Student>{
	private String id;
	private String name;
	private int age;

	// 。。。

	@Override
	public String toString() {
		return "Student [id=" + id + ", name=" + name + ", age=" + age + "]";
	}

	@Override
	public int compareTo(Student o) {
		if(this.getAge()<o.getAge()) {
			return -1;
		}else if(this.getAge() == o.getAge()) {
			return 0;
		}else {
			return 1;
		}
	}

}
```

当实际开发过程中不知道添加元素的源代码、无权修改别人的代码，此时可以使用外部比较器。Comparator 位于java.util包中，定义了compare(o1,o2) 用于提供外部比较策略。TreeSet接受一个指定比较策略的构造方法，这些比较策略的实现类必须实现Comparator接口。

```text
 public class Test01 {
	public static void main(String[] args) {
		
		LenComparator lenComparator = new LenComparator();
		TreeSet<String> set2 = new TreeSet<String>(lenComparator);
		
		set2.add("banana");
		set2.add("coco");
		set2.add("apple");
		
		set2.add("apple");
		System.out.println(set2);
		
	}
}

class LenComparator implements Comparator<String>{

	@Override
	public int compare(String o1, String o2) {
		return o1.length() - o2.length();
	}	
}

使用匿名内部类优化
public class Test02 {
	public static void main(String[] args) {
		
		TreeSet<String> set2 = new TreeSet<String>(new Comparator<String>() {

			@Override
			public int compare(String o1, String o2) {
				return o1.length() - o2.length();
			}
			
		});
		
		set2.add("banana");
		set2.add("coco");
		set2.add("apple");
		
		set2.add("apple");
		System.out.println(set2);
		
	}
}
```

## 3.set接口/Hashset LinkedHashSet TreeSet实现类

![img](https://gitee.com/kirk_zhang/KirkzhangBlog/raw/master/images/set-map接口及其实现类/set接口.jpg)

Set是广义的集合容器，容器内数据是无序，唯一的



```java
public static void main(String[] args) {
		/**
		 * 增:add/addAll
		 * 删:clear/remove/removeAll/retainAll
		 * 改:
		 * 查:contains/containsAll
		 * 遍历:iterator
		 * 其他:size/isEmpty
		 */
		
		Set<Integer> set = new HashSet<Integer>();
		// [1]添加
		// 无序
		set.add(10);
		set.add(3);
		set.add(20);
		set.add(0);
		// 不能添加重复元素
		boolean r = set.add(1);
		System.out.println(set);
		
		// 【2】删除
//		set.remove(1);
//		set.clear();
//		System.out.println(set);
		
		// 【3】查看是否包含
		System.out.println(set.contains(1));
		
		// 【4】其他
		System.out.println(set.size());
		System.out.println(set.isEmpty());
	}

1.9.2Set接口的遍历
public static void main(String[] args) {
		
		Set<String> set = new HashSet<String>();
		set.add("banana");
		set.add("apple");
		set.add("coco");
		
		// 快速遍历
		for (String item : set) {
			System.out.println(item);
		}
		
		// 迭代器
		Iterator<String> it = set.iterator();
		while(it.hasNext()) {
			String item = it.next();
			System.out.println(item);
		}
	}
```

接口的遍历

```text
public static void main(String[] args) {
		
		Set<String> set = new HashSet<String>();
		set.add("banana");
		set.add("apple");
		set.add("coco");
		
		// 快速遍历
		for (String item : set) {
			System.out.println(item);
		}
		
		// 迭代器
		Iterator<String> it = set.iterator();
		while(it.hasNext()) {
			String item = it.next();
			System.out.println(item);
		}
	}
```

1. HashSet是Set接口的实现类，底层数据结构是哈希表。HashSet是线程不安全的(不保证同步)[1]如果向HashSet中存储元素时，**元素一定要实现hashCode方法和equals方法。优点:添加、删除、查询效率高;缺点:无序**
2. LinkedHashSet是Set接口的实现类，底层数据结构哈希表+链表哈希表用于散列元素；链表用于维持添加顺序。**如果要添加自定义对象元素，也需要重写hashCode和equals方法**。
3. TreeSet是Set接口的实现类，底层数据结构是二叉树。TreeSet 存储的数据按照一定的规则存储。存储规则让数据表现出自然顺序。之所以输出元素有顺序。是因为按照二叉树的先序遍历输出。根据TreeSet的工作原理，**向TreeSet添加自定义元素？向TreeSet中添加元素时，一定要提供比较策略（内/外部比较器），否则会出现ClassCastException。**

## 4 hashcode的作用

总结hashcode的作用

1、hashCode主要是哈希数据结构中快速查找，

2、如果两个对象相同（非相同地址），那么这两个对象的hashCode一定要相同；

3、如果对象的equals方法被重写，那么对象的hashCode也尽量重写，并且产生hashCode使用的对象，一定要和equals方法中使用的一致，否则就会违反上面提到的第2点；

4、两个对象的hashCode相同，并不一定表示两个对象就相同，也就是不一定适用于equals(java.lang.Object) 方法，只能够说明这两个对象在散列存储结构中，存在同一个位置。

