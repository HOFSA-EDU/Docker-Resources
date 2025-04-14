```python
# this is our base image.
# we start from here.
FROM alpine:latest

# run updates
# run is a single command
# execution time: during the building process
RUN apk update

# Copy files in our image
# source: hosting device
# destination: /python in the root folder of our alpine
COPY . /python

# install python and pip
RUN apk add --no-cache python3 py3-pip

#Set the working directory at /python
WORKDIR /python

# Run our python script
# execution time: start of the container
CMD ["python3", "matrix.py"]
```