---
title: "Udp Socket Connection Missed Buffer"
date: 2019-06-16T00:29:10+09:00
draft: true
categories: [studylog]
tags: [c++,boost,socket,udp,network,cpp,asio,boost-asio]
---

When I connect via udp socket, I got some issue to lose buffer. and I don't know why.

Let's find out why.

Warning. I use heavyly broken english. I let you know. :P 

<!--more-->

I use following code.

Of receiver.

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <thread>
#include <cstdint>
#include <cassert>
#include <boost/make_shared.hpp>
#include <boost/asio.hpp>
#include <boost/bind.hpp>

const int port = 7003;

void on_recv(
    boost::shared_ptr<boost::asio::ip::udp::socket> socket,
    boost::shared_ptr<boost::asio::ip::udp::endpoint> ep_from,
    boost::shared_ptr<std::vector<uint8_t>> buffer,
    boost::system::error_code const& ec,
    std::size_t const& bytes_tranferred);

int main(void)
{
    std::cout<<"Enter"<<std::endl;

    boost::asio::io_service io_service;
    boost::asio::io_service::work worker(io_service);

    auto thread = std::shared_ptr<std::thread>(new std::thread([&io_service](){ io_service.run(); }));
    assert(thread != nullptr);


    auto socket = boost::make_shared<boost::asio::ip::udp::socket>(
        io_service,
    boost::asio::ip::udp::endpoint(
        boost::asio::ip::udp::v4(),
        port));
    assert(socket != nullptr);

    auto buffer = boost::make_shared<std::vector<uint8_t>>(10); // 10 byte length
    assert(buffer != nullptr);

    auto ep_from = boost::make_shared<boost::asio::ip::udp::endpoint>();
    assert(ep_from != nullptr);

    socket->async_receive_from(
        boost::asio::buffer(*buffer),
        *ep_from,
        boost::bind(on_recv,
            socket,
            ep_from,
            buffer,
            boost::asio::placeholders::error,
            boost::asio::placeholders::bytes_transferred));


    thread->join();

    std::cout<<"Exit"<<std::endl;
    return EXIT_SUCCESS;
}


void on_recv(
    boost::shared_ptr<boost::asio::ip::udp::socket> socket, // local socket
    boost::shared_ptr<boost::asio::ip::udp::endpoint> ep_from, // ep to response
    boost::shared_ptr<std::vector<uint8_t>> buffer, // received buffer
    boost::system::error_code const& ec,
    std::size_t const& bytes_tranferred)
{
    if (ec)
    {
            std::cerr<<__func__<<"() error code : "<<ec.message()<<std::endl;
            return ;
    }

    std::cout<<__func__<<"() recv "<<bytes_tranferred<<" bytes"<<std::endl;

    std::string message(buffer->begin(), buffer->end());
    std::cout<<__func__<<"() received message : "<<message<<std::endl;


    socket->async_receive_from(
        boost::asio::buffer(*buffer),
        *ep_from,
        boost::bind(on_recv,
            socket,
            ep_from,
            buffer,
            boost::asio::placeholders::error,
            boost::asio::placeholders::bytes_transferred));
}
```

Of sender.

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <thread>
#include <cstdint>
#include <cassert>
#include <boost/make_shared.hpp>
#include <boost/asio.hpp>
#include <boost/bind.hpp>

const int port = 7003;

void on_sent(
    boost::shared_ptr<boost::asio::ip::udp::endpoint> ep_from,
    boost::shared_ptr<std::vector<uint8_t>> buffer,
    boost::system::error_code const& ec,
    std::size_t const& bytes_tranferred);

int main(void)
{
    std::cout<<"Enter"<<std::endl;

    boost::asio::io_service io_service;
    boost::asio::io_service::work worker(io_service);

    auto thread = std::shared_ptr<std::thread>(new std::thread([&io_service](){ io_service.run(); }));
    assert(thread != nullptr);


    auto socket = boost::make_shared<boost::asio::ip::udp::socket>(
        io_service,
        boost::asio::ip::udp::endpoint());
    assert(socket != nullptr);

    auto buffer = boost::make_shared<std::vector<uint8_t>>();
    assert(buffer != nullptr);

    std::string hello = "HELLO UDP WORLD";
    buffer->assign(hello.begin(), hello.end());

    auto ep_from = boost::make_shared<boost::asio::ip::udp::endpoint>(
        boost::asio::ip::udp::v4(),
        port);
    assert(ep_from != nullptr);

    socket->async_send_to(
       boost::asio::buffer(*buffer),
       *ep_from,
       boost::bind(on_sent,
           ep_from,
           buffer, // prevent to loose reference count
           boost::asio::placeholders::error,
           boost::asio::placeholders::bytes_transferred));


    thread->join();

    std::cout<<"Exit"<<std::endl;
    return EXIT_SUCCESS;
}


void on_sent(
    boost::shared_ptr<boost::asio::ip::udp::endpoint> ep_from, // ep to response
    boost::shared_ptr<std::vector<uint8_t>> buffer,
    boost::system::error_code const& ec,
    std::size_t const& bytes_tranferred)
{
    if (ec)
    {
        std::cerr<<__func__<<"() error code : "<<ec.message()<<std::endl;
        return ;
    }

    std::cout<<__func__<<"() sent "<<bytes_tranferred<<" bytes"<<std::endl;
}
```

And, I got following result.

```shell
$ ./asio_udp_receiver_sample
Enter
on_recv() recv 10 bytes
on_recv() received message : HELLO UDP 
```

As you can see, I got only half of buffer compare to original sent buffer.
Of course, I set receiving buffer size to very small. but I read buffer repeatedly.
And still, got only fist small buffer of string.


So, why is it?? 

In `UNIX Network Programming: The socket networking API` book has clue.

```
OS BSD-derived systems, when a UDP datagram arrives that is larger than the 
application's buffer, recvmsg sets the MSG_TRUNC flag in the msg_flags member
of the msghdr structure (Figure 14.7). All Berkeley-derived implementations 
that support the msghdr structure with the msg_flags member provide this 
notification.

Unfortunately, not all implementations handle a larger-than-expected UDP 
datagram in this fashion. There are three possible scenarios:
 1. Discard the excess bytes and return the MSG_TRUNC flag to the application. 
    This requires that the application call recvmsg to receive the flag.
 2. Discard the excess bytes, but do not tell the application.
 3. Keep the excess bytes and return them in subsequent read operations on the socket.

 Since there are such variations in how implementations handle datagrams that 
 are larger than the application's receive buffer, one way to detect the 
 problem is to always allocate an application buffer that is one byte greater 
 than the largest datagram the application should ever receive. 
 If a diagram is ever received whose length equals this buffer, consider it 
 an error.
```

So, there are 3 possible scenarios, and the third is not possible. cause I already add subsequent read operation already.
Then, let's find out `MSG_TRUNC` flag on the `msg_flags` member of `msghdr` structure.
