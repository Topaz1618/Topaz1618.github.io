---
title: "Flask Development Tips"
layout: post
date: 2021-04-20 18:59
tag:
 - Flask
 - Web Framework
 - Development
 - Python
blog: true
author: Topaz
summary: "Introduce some random thing of Flask"
permalink: flask-development-tools
---
<h1 class="title"> Flask Development Tips </h1>



<h2> Table of Contents </h2>
- [1. High-level overview of Flask](#c1)
- [2. Using jsonify](#c2)
- [3. Flask's built-in logger](#c3)


<h2 id="c1"> 1. High-level overview of Flask </h2>

When you run a Flask application using the flask run command, Flask creates a single process to handle incoming requests, and each incoming request is handled by a thread within that process.

By default, Flask uses a single-threaded web server called Werkzeug to handle incoming requests, which means it can only handle one request at a time. If your blocking API endpoint takes 10 seconds to handle a request, the server will not be able to handle any additional requests during that time.

Because for Flask, when all the available threads in the Flask application's thread pool are busy processing other requests, incoming requests are placed in a request queue to wait for a thread to become available. Once a thread becomes available, the web server(Werkzeug) assigns it to process the next request in the queue and forwards the request to the Flask application for processing.

However, Werkzeug provides an option to use a multi-threaded approach by using the built-in WSGI server with a thread pool. This can be achieved by setting the threaded parameter to True when starting the server.

code
```
from flask import Flask

app = Flask(__name__)

if __name__ == '__main__':
    app.run(threaded=True, processes=4)
```



In summary, the responsibility of assigning a thread to process the next request in the request queue falls on the application's web server, which is responsible for managing the thread pool and allocating threads to handle incoming requests. The Flask application relies on the web server to manage the underlying network communication and thread pool, and is responsible for processing incoming requests and generating responses.


You can specify other web servers, such as Gunicorn or uWSGI as well. In general, both Gunicorn and uWSGI are capable of handling thousands of concurrent connections and processing hundreds of requests per second.


Concept
```
If a user sends two requests to a Flask application with a blocking API that takes 10 seconds to complete, when the second request is sent while the first request is still being processed, the second request is placed in a request queue.


- Request queue: Flask uses a request queue to hold incoming requests until they can be processed by the application. Request queue is a data structure that holds incoming requests until they can be processed by the application.  

```







When a request comes in, Werkzeug creates a new thread to handle the request, and passes the request to your Flask application to process. Once the request has been processed, the thread is terminated and the response is sent back to the client.

Flask also provides a built-in development server that you can use for testing and development. This server is based on Werkzeug and also uses separate threads to handle incoming requests.

<h2 id="c2"> 2. Using jsonify </h2>


Using jsonify
```
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/data')
def get_data():
    data = {'name': 'John', 'age': 30, 'city': 'New York'}
    return jsonify(data)
```

Using JSON
```
import json
from flask import Flask

app = Flask(__name__)

@app.route('/data')
def get_data():
    data = {'name': 'John', 'age': 30, 'city': 'New York'}
    json_data = json.dumps(data)
    response = app.response_class(response=json_data, status=200, mimetype='application/json')
    return response
```



<h2 id="c3"> 3. Flask's built-in logger </h2>

The logger is typically called by the Flask application's request handling code in the following ways:

- Decorators such as @app.route(): When a Flask application defines a route using the @app.route() decorator, the decorator creates a new function that handles incoming requests for that route. This function is called automatically by the Flask application when a request is received for the specified route, and the logger is called to generate a log message with information about the request.

- Request hooks: Flask provides a number of hooks that can be used to customize the behavior of the request handling code. These hooks include functions such as before_request() and after_request() that are called automatically by the Flask application before and after each request is processed. These hooks can be used to perform additional logging or other tasks related to request processing.

- Error handling code: When a Flask application encounters an error while processing a request, it automatically generates an error response and sends it back to the client. The error handling code that generates this response can also generate log messages to help diagnose the cause of the error.


In summary, the Flask's built-in logger is called automatically by the Flask application's request handling code, which includes the app.route() decorator and other functions or methods used to process incoming requests. The logger is typically called when a request is received by the application, and it generates a log message with information about the request.
