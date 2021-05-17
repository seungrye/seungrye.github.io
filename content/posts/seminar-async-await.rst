+++
title = "Async, Await"
date = "2021-05-16"
draft = "true"
categories = ["studylog"]
tags = ["async","seminar","async","howto"]
+++


.. contents:: Table of Contents


concurrency / parallelism
=========================

.. image:: ./resources/_gen/images/1_Q_UZeToStz8YY2oQGiUPqw.png
  :width: 640
  :alt: concurrency vs parallelism

* concurrency : 동시성
* parallelism : 병렬성

syntax
======

asyncronous in JavaScript
-------------------------

timeout
~~~~~~~
.. code-block:: javascript

        function async_fn(cb) {
            var finished = false;
            var a = [];
            var r = [];

            setTimeout(function repeat() { (finished)?cb(r.length):setTimeout(repeat, 0) }, 0);

            for (var i = 0; i < 1000000; ++i) {
                a.push(Math.random().toString(36));
            }

            a.forEach(s => {
                if (s.split('').every(c => c.match(/[0.a-z]/))) r.push(s); // 0, ., 영문소문자 로만 이루어진것을 찾아보자.
            });

            // console.log(a[0]);
            finished = true;
        }

        const t0 = performance.now();
        async_fn(n => console.log("calculated count : "+n));
        const t1 = performance.now();
        console.log("time 1");
        console.log(`Call to doSomething took ${t1 - t0} milliseconds.`);  


promise
~~~~~~~

async, await
~~~~~~~~~~~~

asyncronous in Python
-------------

yield
~~~~~

async
~~~~~

asyncronous in C++
------------------

thread
~~~~~~

future, promise
~~~~~~~~~~~~~~~

async, await
~~~~~~~~~~~~

demo
----

c++ implementation
==================
