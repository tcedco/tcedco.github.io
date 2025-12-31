---
title: "Codects: Simple Web Server"
layout: post
outputs: [html, json]
categories: [web development]
date: 2024-08-31
---

Welcome to another installment of Codects, my weekly coding adventure where I take on new challenges to learn something cool. Last week we dove into Linux and built my own simple UNIX-based shell. This week, I decided to build something practical and foundational in terms of web development—a simple web server in Python. While there are plenty of robust servers out there, like **[Apache](https://httpd.apache.org/)** and **[Nginx](https://nginx.org/en/)**, building your own server from scratch is a great way to understand how the web actually works, so that's what I did.

## The Starting Point

I started off with the goal of creating a server that could handle both **`GET`** and **`POST`** requests. The **`GET`** request would serve up a basic HTML page, and the **`POST`** request would allow users to submit a form, which would then display a personalized message.

## Setting Up the Server

To begin, I used Python's **`http.server`** and **`socketserver`** modules. These are built-in and make setting up a basic server incredibly straightforward. The **`http.server.SimpleHTTPRequestHandler`** class, in particular, provides a simple way to handle HTTP requests, which is perfect for this project.

I defined a custom request handler class called **`MyRequestHandler`** that extends **`SimpleHTTPRequestHandler`**. This class is where the magic happens—it handles incoming requests and sends back the appropriate responses.

```python
import http.server
import socketserver
import urllib.parse

PORT = 8000

class MyRequestHandler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self) -> None:
        print(f"Received GET request for {self.path}")

        if self.path == '/':
            self.path = "index.html"

        return super().do_GET()

    def do_POST(self) -> None:
        print(f"Received POST request")

        content_length = int(self.headers["Content-Length"])
        post_data = self.rfile.read(content_length)
        post_data = urllib.parse.parse_qs(post_data.decode("utf-8"))
        name = post_data.get("name", [''])[0]

        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()

        response = f"""
        <!DOCTYPE html>
        <html>
            <body>
                <h1>Hello, {name}!</h1>
                <a href="/">Go back</a>
            </body>
        </html>
        """

        self.wfile.write(response.encode("utf-8"))

with socketserver.TCPServer(("", PORT), MyRequestHandler) as httpd:
    print(f"Server started on port {str(PORT)}")

    try:
        httpd.serve_forever()

    except KeyboardInterrupt:
        print("\nShutting down the server...")
        httpd.server_close()
```

## Handling `GET` Requests

The first thing I needed to do was handle `GET` requests. These are the requests that the browser sends when you type a URL into the address bar or click on a link. In this case, I wanted my server to respond with an HTML page when someone accessed the root directory `/`.

At first, I encountered an issue where my server wasn’t serving the correct file. The problem was a simple mistake: I had accidentally used `==` instead of `=` when assigning `self.path`. Once I fixed that, the server correctly served up `index.html` when accessed at `/`.

## Crafting the HTML Page

For the HTML, I kept it simple but functional. The page included a form where users could input their name. When the form is submitted, it sends a `GET` request with the user’s name as a query parameter.

```html
<!DOCTYPE html>

<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Simple Web Server</title>
    </head>
    <body>
        <h1>Welcome to my simple web server</h1>
        <p>This is a custom HTML page served by my Python web server</p>

        <form>
            <label for="name">Your Name:</label>
            <input type="text" id="name" name="name">
            <input type="submit" value="Submit">
        </form>
    </body>
</html>
```

## Handling `POST` Requests

Next, I needed to handle `POST` requests. These requests are typically used when you submit a form on a website. I wanted my server to capture the user’s name from the form and then display a personalized greeting.

This part was a bit trickier. I had to figure out how to read the data from the request and then parse it. Python’s `urllib.parse` module came in handy here. It allowed me to easily decode the data and extract the user’s input.

After parsing the data, I created a simple HTML response that included the user’s name. This response was then sent back to the browser, where it would display the personalized message.

## What's the Result?

After a few rounds of testing and debugging, the server was up and running! When I accessed the server and submitted my name through the form, I was greeted with a message that said, “Hello, Sami!”

This project was a great way to understand the basics of how a web server works. Even though it’s a simple implementation, it touches on some core concepts like handling HTTP requests, serving static files, and processing form data. Plus, it’s always fun to see your code in action on a web page!

The code is on [GitHub](https://github.com/Tcedco/simple-web-server), so feel free to check it out and maybe even try building on it. There’s always more to explore, like adding support for different types of content or handling more complex routing. That's pretty much for this week edition of *Codects*, until next time!