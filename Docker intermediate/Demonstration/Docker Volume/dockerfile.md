**From** python:3.11-slim

**WORKDIR** /app

RUN mkdir logs

**COPY** . /app

**ENTRYPOINT** ["python", "main.py"]