Open Docker Desktop App

Open Command Prompt

Setup the PostgreSQL with Docker

a. Pull the PostgreSQL image
docker pull postgres
b. Create and start a PostgreSQL instance
docker run -d -p 5432:5432 --name postgres1 -e POSTGRES_PASSWORD=pass12345 postgres
c. Open a terminal to the container
docker exec -it postgres1 bash
d. Interact with PostgreSQL using psql
psql -d postgres -U postgres

Run the commands 
Container is created in the Docker

Open "VS Code"
Create the files
-->requirements.txt
flask
redis

-->app.py
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return f"Hello World! I have been seen {count} times.\n"

if __name__ == "__main__":   
    app.run(host="0.0.0.0", port=5000)

-->Dockerfile
FROM python:3.7-alpine
WORKDIR /code

ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0

RUN apk add --no-cache gcc musl-dev linux-headers

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

EXPOSE 5000
COPY . .

CMD ["flask", "run"]

-->compose.yaml

services:
  web:
    build: .
    ports:
      - "8000:5000"
    depends_on:
      - redis

  redis:
    image: "redis:alpine"


      
Open "Terminal" in VS Code
  Run the Command ("docker composeup")
  
Open the Application in a browser at http://localhost:8000
