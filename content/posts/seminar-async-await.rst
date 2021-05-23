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

.. code-block::html
    <body>
        <script>
            function async_fn(cb) {
                var finished = false;
                var url = 'https://i.imgur.com/82tHC9G.jpeg';
                var request = new XMLHttpRequest();
                var buffer;
    
                setTimeout(function repeat() { (finished)?cb(buffer):setTimeout(repeat, 0) }, 0);
    
                request.open('GET', url, false); // 'false' makes the request synchronous
                request.overrideMimeType('text/plain; charset=x-user-defined');
                request.send(null);
                console.log("cur time : "+Date.now() + ", request response with result code : "+request.status);

                var binary = ""
                for(var i=0;i<request.responseText.length;i++){
                    binary += String.fromCharCode(request.responseText.charCodeAt(i) & 0xff);
                }
                
                buffer = 'data:image/jpeg;base64,' + btoa(binary);

                finished = true;
            }
    
            $(document).ready(() => {
                console.log("start time : "+Date.now());
                async_fn(b => {
                    console.log("async end : "+Date.now());
                    var img = document.createElement('img');
                    img.src = b;
                    img.style.height="480px";
                    document.body.appendChild(img);
                });
                console.log("cur time : "+Date.now() + ", time 1");
                console.log("end time : "+Date.now());
            });
        </script>

        <h3>messages</h3>
        <ul class="messages"></ul>
    </body>

callback hell
~~~~~~~~~~~~~

.. code-block::JavaScript
    function do_something1(v, cb) { setTimeout(() => { cb(v+'1'); }, 100); }
    function do_something2(v, cb) { setTimeout(() => { cb(v+'2'); }, 100); }
    function do_something3(v, cb) { setTimeout(() => { cb(v+'3'); }, 100); }
    do_something1('0', (r) => {
        console.log("cb of do_something1 "+r);
        do_something2(r, (r) => {
            console.log("cb of do_something2 "+r);
            do_something3(r, (r) => {
                console.log("cb of do_something3 "+r);
            });
        });
    });

promise
~~~~~~~

.. code-block::html
    <body>
        <script>
            'use strict';
            function async_fn() {
                var url = 'https://i.imgur.com/82tHC9G.jpeg';
                var request = new XMLHttpRequest();

                request.open('GET', url, false); // 'false' makes the request synchronous
                request.overrideMimeType('text/plain; charset=x-user-defined');
                request.send(null);
                console.log("cur time : "+Date.now() + ", request response with result code : "+request.status);

                var binary = ""
                for(var i=0;i<request.responseText.length;i++){
                    binary += String.fromCharCode(request.responseText.charCodeAt(i) & 0xff);
                }
                
                return 'data:image/jpeg;base64,' + btoa(binary);
            }
    
            $(document).ready(() => {
                console.log("start time : "+Date.now());
                var asynced = new Promise((resolve, reject) => resolve(async_fn()));
                asynced.then(b => {
                    console.log("async end : "+Date.now());
                    var img = document.createElement('img');
                    img.src = b;
                    img.style.height="480px";
                    document.body.appendChild(img);
                });
                console.log("cur time : "+Date.now() + ", time 1");
                console.log("end time : "+Date.now());
            });
        </script>

        <h3>messages</h3>
        <ul class="messages"></ul>
    </body>

chaining instead of callback
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block::JavaScript
    function do_something1(v) { return new Promise(resolve => { setTimeout(() => { resolve(v+'1'); }, 100); }); }
    function do_something2(v) { return new Promise(resolve => { setTimeout(() => { resolve(v+'2'); }, 100); }); }
    function do_something3(v) { return new Promise(resolve => { setTimeout(() => { resolve(v+'3'); }, 100); }); }
    var promised = do_something1('0');
    promised.then(/*resolve fn*/(r) => { console.log("after do_something1 "+r); return do_something2(r); })
        .then(/*resolve fn*/(r) => { console.log("after do_something2 "+r); return do_something3(r); })
        .then(/*resolve fn*/(r) => { console.log("after do_something3 "+r); });

async, await
~~~~~~~~~~~~

.. code-block:html
    <body>
        <script>
            'use strict';
            function hxr_img_buff() {
                return new Promise(resolve => {
                    var url = 'https://i.imgur.com/82tHC9G.jpeg';
                    var request = new XMLHttpRequest();

                    request.open('GET', url, false); // 'false' makes the request synchronous
                    request.overrideMimeType('text/plain; charset=x-user-defined');
                    request.send(null);
                    console.log("cur time : "+Date.now() + ", request response with result code : "+request.status);

                    var binary = ""
                    for(var i=0;i<request.responseText.length;i++){
                        binary += String.fromCharCode(request.responseText.charCodeAt(i) & 0xff);
                    }
                    
                    resolve('data:image/jpeg;base64,' + btoa(binary));
                });
            }
            
            async function async_fn() {  // async function can wait promise, it return 'value'
                var b = await hxr_img_buff();
                console.log("async end : "+Date.now());
                var img = document.createElement('img');
                img.src = b;
                img.style.height="480px";
                document.body.appendChild(img);
            }

            $(document).ready(() => {
                console.log("start time : "+Date.now());
                async_fn();
                console.log("cur time : "+Date.now() + ", time 1");
                console.log("end time : "+Date.now());
            });
        </script>

        <h3>messages</h3>
        <ul class="messages"></ul>
    </body>

synchronous-like
~~~~~~~~~~~~~~~~

.. code-block::JavaScript
    // async function return 'new Promise()' object.
    async function sleep(m) { return new Promise(resolve => setTimeout(() => resolve(), m)); }
    async function do_something1(v) { await sleep(100); return v+'1'; }
    async function do_something2(v) { await sleep(100); return v+'2'; }
    async function do_something3(v) { await sleep(100); return v+'3'; }
    (async function() {
        var r = await do_something1('0');
        console.log("after do_something1 "+r);
        r = await do_something2(r);
        console.log("after do_something2 "+r);
        r = await do_something3(r);
        console.log("after do_something3 "+r);
    })();
    console.log("what?");
        
asyncronous in Python
---------------------

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
