---
title: "Python regex contents in curly braces"
date: 2019-06-20T23:18:49+09:00
draft: false
categories: [studylog]
tags: [tip,python,regex,howto]
---

When I want to extract contents between curly braces using python+regex.

<!--more-->

The content is following.

```
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

When I extract content between `if (ec) { ... }` I should use following code.

```python
>>> import re
>>> contents = """void on_sent(
... boost::shared_ptr<boost::asio::ip::udp::endpoint> ep_from, // ep to response
...     boost::shared_ptr<std::vector<uint8_t>> buffer,
...     boost::system::error_code const& ec,
...     std::size_t const& bytes_tranferred)
... {
...     if (ec)
...     {
...         std::cerr<<__func__<<"() error code : "<<ec.message()<<std::endl;
...         return ;
...     }
... 
...     std::cout<<__func__<<"() sent "<<bytes_tranferred<<" bytes"<<std::endl;
... }"""
>>> re.findall(r"if.*?\{(.*?)\}", contents, re.MULTILINE | re.DOTALL)
['\n        std::cerr<<__func__<<"() error code : "<<ec.message()<<std::endl;\n        return ;\n    ']
```

Note. I don't know this is right answer. (maybe not :P ) but, anyway It works. 

How about whole function?

```python
>>> re.findall(r"on_sent.*?\{(.*?)\}", a, re.MULTILINE | re.DOTALL)
['\n    if (ec)\n    {\n        std::cerr<<__func__<<"() error code : "<<ec.message()<<std::endl;\n        return ;\n    ']
```

This is not what I want. So sad. :(

How can I fix? Before fix code, I should understand what the regex meaning is.

ref : [greedy-vs-non-greedy](https://wikidocs.net/4309#greedy-vs-non-greedy)

* `.*` : greedy search (return maxmium statisfied length string)
* `.*?` : non-greedy search (return minimum satistfied length string)

Oh, Then the problem could resolve easily.

```python
>>> re.findall(r"on_sent.*?\{(.*)\}", a, re.MULTILINE | re.DOTALL)
['\n    if (ec)\n    {\n        std::cerr<<__func__<<"() error code : "<<ec.message()<<std::endl;\n        return ;\n    }\n\n    std::cout<<__func__<<"() sent "<<bytes_tranferred<<" bytes"<<std::endl;\n']
```

What an easy!
