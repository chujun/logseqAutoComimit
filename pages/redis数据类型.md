- 对各种数据结构的极致优化
- String
  String数据结构
  并没有使用C语言的字符串表示，而是自己实现了一种 简单动态字符串（simple dynamic string，SDS）
  ![截屏2022-06-19 下午9.53.37.png](../assets/截屏2022-06-19_下午9.53.37_1655646832344_0.png) 
  优势：
  1. 相比于 C 的原生字符串，Redis 的 SDS 不光可以保存文本数据还可以保存二进制数据，并且获取字符串长度复杂度为 O(1)（C 字符串为 O(N))
  2. 除此之外，Redis 的 SDS API 是安全的，不会造成缓冲区溢出。
  常用命令： set,get,strlen,exists,decr,incr,setex 等等。
-