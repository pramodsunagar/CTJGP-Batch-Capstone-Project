# Docker Image Creation for Python API
### Tasks:

In this task, a **Python Flask REST API** is containerized using Docker. The process involves creating the application files, defining dependencies, writing a Dockerfile, building the Docker image, and pushing the image to **Docker Hub**.


## Steps:
Create Docker working directory:
```
mkdir task3 && cd task3
```
Create dependency file:
```
vi requirements.txt
```
Add the given content, by pressing `INSERT`
```
Flask==1.0.1
requests==2.8.1
```
Create Dockerfile:
```
vi Dockerfile
```
Add the given content, by pressing `INSERT`
```Dockerfile
FROM ubuntu:18.04
LABEL maintainer="Admin CloudThat"
RUN apt-get update -y && \
    apt-get install -y python-pip python-dev
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD [ "python", "app.py" ]
```
Create application directory:
```
vi app.py
```
Add the given content, by pressing `INSERT`
```
#!/usr/bin/env python3.8
# -*- coding: utf-8 -*-
import os

from flask import Flask, jsonify, request

app = Flask(__name__)

notes = [
    {
        "author": "hightower",
        "title": "Kubernetes up and Running"
    },
    {
        "author": "navathe",
        "title": "Database Fundamentals"
    },
    {
        "author": "ritchie",
        "title": "Let us C"
    }
]

@app.route("/")
def hello():
    return "Welcome to my bookstore!"

@app.route("/v1/books/")
def list_all_books():
    list = []
    for item in notes:
       list.append({'book':item['title']})
    return jsonify(list)

@app.route("/v1/books/<string:author>")
def get_by_author(author):
    for item in notes:
	    if item['author'] == author:
	       data = item
    return jsonify(data)
    if not item:
        return jsonify({'error': 'Author does not exist'}), 404

@app.route("/v1/books/", methods=["POST"])
def add_book():
    author = request.json.get('author')
    book = request.json.get('title')
    if not author or not book:
        return jsonify({'error': 'Please provide Author and Title'}), 400
    else:
        data = request.get_json()
        notes.append(data)
        return jsonify({'message': 'Added book successfully','author':author,'book': book}),200

if __name__ == '__main__':
    app.run(threaded=True, host='0.0.0.0', port=5000)
```

Build Docker image:
```
docker build -t <username>/test-flask-app:v1 .
```
Login to Docker Hub:
```
docker login -u <username>
```
Push image:
```
docker push <username>/test-flask-app:v1 
```
