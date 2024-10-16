
# 阅读笔记

- 主线程中创建listen socket
- 主线程中创建内核事件表m_epollfd。  epoll_create
- 将 listen socket 的读事件 添加到 m_epollfd
- 主线程中执行 epoll_wait

### 可读事件
```
http_conn: 
	char read_buf[READ_BUFFER_SIZE]; //  2048
	read最多2048字节到buffer中。 若是read到字节
	
	添加任务到线程池的请求队列中

线程池分发请求队列，在分发到的线程中调用 http_conn::process

http_conn::process
    解析read_buf中的数据 为 http请求数据

    若是数据不够，则继续接受数据。 modfd(m_epollfd, m_sockfd, EPOLLIN); // ET
    若是解析出错，则直接：写入 http的错误返回到 write_buf； modfd(m_epollfd, m_sockfd, EPOLLOUT);
    若是解析确定为一个完整的http请求：
        回调应用层处理，应用层的函数应该设置 http返回信息
        modfd(m_epollfd, m_sockfd, EPOLLOUT);
```

### 可写事件
```
bool http_conn::write()
    发送数据。
        若发送结束，则：
            modfd(m_epollfd, m_sockfd, EPOLLIN);
            init();
        若有数据可发，则发送
            部分发送，则： modfd(m_epollfd, m_sockfd, EPOLLOUT, m_TRIGMode);
            全部发送，则： modfd(m_epollfd, m_sockfd, EPOLLIN, m_TRIGMode); 然后init();
```
	