# IO

## 分类
阻塞IO
非阻塞IO
多路复用IO
信号IO
异步IO

## 阻塞IO
stream:
inputStream
outputStream

read
write

## nio
SocketChannel

## 多路复用IO
selector: fd_set(1024) 内核到用户copy
poll：数组fd，os触发内核到用户copy
epoll: os 轮询fd,树状fd, 只返回就绪fd

## 信号IO
java中无具体支持，os支持

？为什么java不支持
跨平台问题，信号 I/O 是 POSIX 特定，在 Windows 等系统上根本不存在。
信号机制复杂，一些Java本身设计不支持，安全性问题
Java 已有更安全的替代方案，NIO多路复用本身就应用了信号机制，或者完全异步IO


## 异步IO

AsynchronousSocketChannel / AsynchronousServerSocketChannel
