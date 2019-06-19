---
title: "Why My Udp Socket Lost Buffer"
date: 2019-06-16T00:29:10+09:00
draft: false
categories: [studylog]
tags: [c++,boost,socket,udp,network,cpp,asio,boost-asio]
---

When I connect via udp socket, I got buffer lost issue. and I don't know why.

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

As you can see, I got only half of buffer compare to original sent buffer. ( Where is the `WORLD` string?? :$ )
Of course, I set receiving buffer size to very small. but I read buffer repeatedly.
And still, got only first small buffer of string.


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

So, there are 3 possible scenarios, and the third is not possible. 
cause I already add subsequent read operation, but still has issue.

Then, let's find out `MSG_TRUNC` flag on the `msg_flags` that member of `msghdr` structure.

First of all, what `MSG_TRUNC` value is it??

```bash
$ cat /usr/include/bits/socket.h | grep MSG_TRUNC
    MSG_TRUNC           = 0x20,
#define MSG_TRUNC       MSG_TRUNC
```

I found the `msg_flags` variable on `boost::asio::detail::socket_ops::recvfrom(...)`.

```cpp
signed_size_type recvfrom(socket_type s, buf* bufs, size_t count,
    int flags, socket_addr_type* addr, std::size_t* addrlen,
    boost::system::error_code& ec)
{
  clear_last_error();
  msghdr msg = msghdr();
  init_msghdr_msg_name(msg.msg_name, addr);
  msg.msg_namelen = static_cast<int>(*addrlen);
  msg.msg_iov = bufs;
  msg.msg_iovlen = static_cast<int>(count);
  signed_size_type result = error_wrapper(::recvmsg(s, &msg, flags), ec);
> *addrlen = msg.msg_namelen;
  if (result >= 0)
    ec = boost::system::error_code();
  return result;
```

And I check `msg.msg_flags` after returned of `::recvmsg(s, &msg, flags)`.

It is 0x20. 

```lldb
(lldb) bt
* thread #2, name = 'asio_udp_receiv', stop reason = step over
  * frame #0: 0x0000555555564780 asio_udp_receiver_sample1`boost::asio::detail::socket_ops::recvfrom(s=6, bufs=0x00007ffff7a6db10, count=1, flags=0, addr=0x000055555559983c, addrlen=0x00007ffff7a6db00, ec=0x0000555555599880) at socket_ops.ipp:941:18
    frame #1: 0x0000555555564839 asio_udp_receiver_sample1`boost::asio::detail::socket_ops::non_blocking_recvfrom(s=6, bufs=0x00007ffff7a6db10, count=1, flags=0, addr=0x000055555559983c, addrlen=0x00007ffff7a6db00, ec=0x0000555555599880, bytes_transferred=0x0000555555599890) at socket_ops.ipp:1015:50
    frame #2: 0x000055555556fda0 asio_udp_receiver_sample1`boost::asio::detail::reactive_socket_recvfrom_op_base<boost::asio::mutable_buffers_1, boost::asio::ip::basic_endpoint<boost::asio::ip::udp> >::do_perform(base=0x0000555555599860) at reactive_socket_recvfrom_op.hpp:57:54
    frame #3: 0x0000555555560de9 asio_udp_receiver_sample1`boost::asio::detail::reactor_op::perform(this=0x0000555555599860) at reactor_op.hpp:44:25
    frame #4: 0x0000555555562da3 asio_udp_receiver_sample1`boost::asio::detail::epoll_reactor::descriptor_state::perform_io(this=0x0000555555599700, events=1) at epoll_reactor.ipp:743:52
    frame #5: 0x0000555555562ed3 asio_udp_receiver_sample1`boost::asio::detail::epoll_reactor::descriptor_state::do_complete(owner=0x00005555555992d0, base=0x0000555555599700, ec=0x00007ffff7a6ddd0, bytes_transferred=1) at epoll_reactor.ipp:774:52
    frame #6: 0x000055555555f4c8 asio_udp_receiver_sample1`boost::asio::detail::scheduler_operation::complete(this=0x0000555555599700, owner=0x00005555555992d0, ec=0x00007ffff7a6ddd0, bytes_transferred=1) at scheduler_operation.hpp:40:10
    frame #7: 0x0000555555563b70 asio_udp_receiver_sample1`boost::asio::detail::scheduler::do_run_one(this=0x00005555555992d0, lock=0x00007ffff7a6dd30, this_thread=0x00007ffff7a6dd60, ec=0x00007ffff7a6ddd0) at scheduler.ipp:401:20
    frame #8: 0x00005555555635b5 asio_udp_receiver_sample1`boost::asio::detail::scheduler::run(this=0x00005555555992d0, ec=0x00007ffff7a6ddd0) at scheduler.ipp:154:20
    frame #9: 0x000055555556406a asio_udp_receiver_sample1`boost::asio::io_context::run(this=0x00007fffffffebe0) at io_context.ipp:62:27
    frame #10: 0x000055555555b7a5 asio_udp_receiver_sample1`operator(__closure=0x0000555555599418) at main.cpp:27:92
    frame #11: 0x000055555555c7d9 asio_udp_receiver_sample1`std::__invoke_impl<void, main()::<lambda()> >((null)=__invoke_other @ 0x00007ffff7a6de40, __f=0x0000555555599418)> &&) at invoke.h:60:36
    frame #12: 0x000055555555c77a asio_udp_receiver_sample1`std::__invoke<main()::<lambda()> >(__fn=0x0000555555599418)> &&) at invoke.h:95:40
    frame #13: 0x000055555555c718 asio_udp_receiver_sample1`std::thread::_Invoker<std::tuple<main()::<lambda()> > >::_M_invoke<0>(this=0x0000555555599418, (null)=_Index_tuple<0> @ 0x00007ffff7a6de90) const at thread:244:26
    frame #14: 0x000055555555c6d9 asio_udp_receiver_sample1`std::thread::_Invoker<std::tuple<main()::<lambda()> > >::operator(this=0x0000555555599418)() const at thread:251:31
    frame #15: 0x000055555555c6ae asio_udp_receiver_sample1`std::thread::_State_impl<std::thread::_Invoker<std::tuple<main()::<lambda()> > > >::_M_run(this=0x0000555555599410) const at thread:195:13
    frame #16: 0x00007ffff7e65cd4 libstdc++.so.6`execute_native_thread_routine at thread.cc:80:18
    frame #17: 0x00007ffff7f8857f libpthread.so.0`start_thread + 239
    frame #18: 0x00007ffff7b6ff13 libc.so.6`__GI___clone + 67
(lldb) p/x msg.msg_flags
(int) $8 = 0x00000020
```

So, In my case. I am in `1. Discard the excess bytes and return the MSG_TRUNC flag to the application.` scenario.

Too bad that boost asio library did not notice this kind of situation. or my bad :P

