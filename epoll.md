## kernel的epoll

同步，非阻塞
多路复用

## client kernel Thread演变历史


```
BIO 阻塞
同步非阻塞
select 减少内核态和用户态的切换
epoll多路复用技术
```
![image](95F60C51E18B4925B6C1F9A1EED4A242)


- 多路（每一个IO）复用（一次调用返回多条IO路状态）：

> 多条路（IO）通过一个系统调用，获得其中的IO**状态**，然后由程序对着有状态的IO进行R/W

- 只要程序自己读写，你的IO模型就是同步的。

> - 只关注IO，不关注IO读写完之后的事情
> - 同步：app自己R/W
> - 异步：kernel完成R/W，好像程序没有访问IO，读写的buffer。win：IOCP
> - 阻塞：blocking
> - 非阻塞：nonblocking
> - linux以及成熟的框架netty
> - 同步阻塞
> - 同步非阻塞

- SELECT POSIX（规范） 解耦性更强 出现较早，移植性比较高，在此基础上，不同的内核有不同的优化。
- POLL
- linux EPOLL
- unix kqueue
> 类比阿里的P5 -》P6 -》P7

**其实无论BIO，NIO，POLL，EPOLL都是要遍历所有的IO，询问状态**
只不过
- BIO：遍历的过程成本需要在用户态和内核态切换，成本开销很大
- （多路复用器-1）POLL，Select：遍历过程中触发了一次系统调用（用户态和内核态的切换，过程中把fds传给内核，**内核重新根据用户调用传过来的fds遍历（速度快）**，修改状态）
- （多路复用器-2）
> 解决POLL的问题，**POLL的弊端**是什么？  
> 问题1:select poll 每次都要重新传递fds  
> 问题2:内核被调用了之后，触发一次调用传递fds全量的复杂度。

![image](EB89E05F6E74448EB04861F70E811F3D)

# 单调用
## SELECT (synchronous I/O multiplexing)
### 使用方法
![image](C9A5C18901E145CC88759B3EEF7B1957)

### FD_SETSIZE(1024) 弊端之一
![image](5D5F2259E6BD43CF94234157E843DBC3)

## POLL（里面没有1024限制，相对使用较多）
![image](5683DAA8A06C47F8B5CBAFAC4DDE7C84)
![image](53F22214B0C945E0A4E19575AA4FF952)