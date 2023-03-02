# Mini WGSI Server

This is a simple WGSI server, according to tutorials [Let’s Build A Web Server](https://ruslanspivak.com/lsbaws-part1/)

> What is WSGI?
>
> Python Web Server Gateway Interface (WSGI),
> which allowed developers to separate choice of a Web framework from choice of a Web server. 

This mini WGSI server can run our basic Web application written with Web framework such as Flask, Django, or some other Python WSGI framework

There are some simple examples of application in `/test_app`, which can be used to test WGSI server.

## Extra Feature

1. Implement concurrent server using os.fork()

When a process forks a new process it becomes a parent process to that newly forked child process. So in server, every parent process does now is accept a new connection from a client, fork a child to handle the client request, and loop over to accept a new client connection

2. Solve the zombie problem which happend when forking a child process and it exits and the parent process doesn’t wait for it and doesn’t collect its termination status, it becomes a zombie.

The method to deal with zombie problem is using the SIGCHLD event handler to asynchronously wait for a terminated child to get its termination status

3. Handle with a flood of SIGCHLD signals that caused by connecting so many simultaneous connection. 

The solution to the problem is to set up a SIGCHLD event handler but instead of wait use a waitpid system call with a WNOHANG option in a loop to make sure that all terminated child processes are taken care of.

## How to use

Take Flask Application as the first example:

Ask the server to load the ‘app’ callable from the python module `test_app/flask_app`. (the parameter should be `path/to/module/module_name:app`)

```
$ python3 WGSI_server.py test_app/flask_app:app

WSGIServer: Serving HTTP on port 8888 ...
```

Now the server is ready to take requests and forward them to the Flask application.

Then type ‘curl’ and see that the server returns a message generated by the Flask application

```
$ curl -v http://localhost:8888/hello

*   Trying 127.0.0.1:8888...
* Connected to localhost (127.0.0.1) port 8888 (#0)
> GET /hello HTTP/1.1
> Host: localhost:8888
> User-Agent: curl/7.71.1
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Content-Type: text/plain; charset=utf-8
< Content-Length: 24
< Date: Mon, 15 Jul 2019 5:54:48 GMT
< Server: WSGIServer 0.2
< 
Hello world from Flask!
* Connection #0 to host localhost left intact
```

The steps to run a Django application are basically the same as above, but the Django application returns more complex content (HTML), which is recommended to be viewed in a browser. 

Also, `test_app/wsgi_app` is a minimalistic WSGI Web application/Web framework without using Pyramid, Flask, or Django. You can also run it with the server.

## Acknowledgements

Thanks to [Ruslan's Blog](https://ruslanspivak.com/)
