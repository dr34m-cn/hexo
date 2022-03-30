---
title: 将对象List中的某个字段放到新的List中[转载]
date: 2022-03-22 16:38:34
tags: [Java,编程]
index_img: /assets/headImg/java.png
---

# 目标

将对象List中的某个字段放到新的List中

<!--more-->

转载自：[list stream:将对象List中的某个字段放到新的List中](https://blog.csdn.net/zhangzehai2234/article/details/88432386)

# 实现

```java
public class Test {
	public static void main(String[] args) {
		List<TAdslot> userList = new ArrayList<>();
		TAdslot adslot1 = new TAdslot();
		adslot1.setName("One");
		adslot1.setNameList(Lists.newArrayList("A","B","C"));
		TAdslot adslot2 = new TAdslot();
		adslot2.setName("Zero");
		adslot2.setNameList(Lists.newArrayList("D","E","F"));
		TAdslot adslot3 = new TAdslot();
		adslot3.setName("Two");
		adslot3.setNameList(Lists.newArrayList("H","I","J"));
		TAdslot adslot4 = new TAdslot();
		adslot4.setName("Four");
		userList.add(adslot1);
		userList.add(adslot2);
		userList.add(adslot3);
		userList.add(adslot4);
	
		List<String> stringList = userList.stream().map(TAdslot::getName).collect(Collectors.toList());
		System.out.println(stringList);

		List<String> userListStr = userList.stream().map(TAdslot::getNameList).filter(Objects::nonNull).flatMap(List::stream).collect(Collectors.toList());
		System.out.println(userListStr);
	}
}

@Data
class TAdslot {
	public String name;
	public List<String> nameList;
}
```

输出结果：

> [One, Zero, Two, Four]
> [A, B, C, D, E, F, H, I, J]
