- 对象堆内存布局
  HotSpot虚拟机中主要分为三部分
  1. 对象头(Header)
  对象头主要有两部分
  a.
  b.MarkWord
  ![32位虚拟机对象头MarkWord.png](../assets/截屏2022-06-02_下午9.11.26_1654175648260_0.png)
  2 .实例数据(Instance Data)
  3. 对齐填充(Padding)