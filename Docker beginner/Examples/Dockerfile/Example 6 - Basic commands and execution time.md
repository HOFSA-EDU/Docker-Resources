# Scenario description
In this example, we have a Python program that we want to containerise. The source code can be found in the following section of the example. First, we want to create a Docker image that we can use to deploy one or more containers. As a basis we use a Linux Alpine and install Python 3 including pip. When the container is started, the code should be executed immediately.

# Learning Goals
1. basic commands of a dockerfile (copy, base image, run commands)
	- Copy data into the image with the COPY command
	- Define the base image with FROM
	- Run instructions with CMD and RUN
	
2. execution time of commands
	- RUN for dependencies 
	- CMD for instructions after the deployment
	
3. creation of a docker image with docker build
	- Using docker build

# Step-by-step instructions

Starting with the python script. Create a file called `app.py` containing the following lines:
```python
#! /usr/bin/env python3

MAX_CASCADES = 600
MAX_COLS = 20
FRAME_DELAY = 0.03

MAX_SPEED = 5

import shutil, sys, time
from random import choice, randrange, paretovariate

CSI = "\x1b["
pr = lambda command: print("\x1b[", command, sep="", end="")
getchars = lambda start, end: [chr(i) for i in range(start, end)]

black, green, white = "30", "32", "37"

latin = getchars(0x30, 0x80)
greek = getchars(0x390, 0x3d0)
hebrew = getchars(0x5d0, 0x5eb)
cyrillic = getchars(0x400, 0x50)

chars= latin + greek + hebrew + cyrillic

def pareto(limit):
    scale = lines // 2
    number = (paretovariate(1.16) - 1) * scale
    return max(0, limit - number)

def init():
    global cols, lines
    cols, lines = shutil.get_terminal_size()
    pr("?25l")  # Hides cursor
    pr("s")  # Saves cursor position

def end():
    pr("m")   # reset attributes
    pr("2J")  # clear screen
    pr("u")  # Restores cursor position
    pr("?25h")  # Show cursor

def print_at(char, x, y, color="", bright="0"):
    pr("%d;%df" % (y, x))
    pr(bright + ";" + color + "m")
    print(char, end="", flush=True)

def update_line(speed, counter, line):
    counter += 1
    if counter >= speed:
        line += 1
        counter = 0
    return counter, line

def cascade(col):
    speed = randrange(1, MAX_SPEED)
    espeed = randrange(1, MAX_SPEED)
    line = counter = ecounter = 0
    oldline = eline = -1
    erasing = False
    bright = "1"
    limit = pareto(lines)
    while True:
        counter, line = update_line(speed , counter, line)
        if randrange(10 * speed) < 1:
            bright = "0"
        if line > 1 and line <= limit and oldline != line:
            print_at(choice(chars),col, line-1, green, bright)
        if line < limit:
            print_at(choice(chars),col, line, white, "1")
        if erasing:
            ecounter, eline = update_line(espeed, ecounter, eline)
            print_at(" ",col, eline, black)
        else:
            erasing = randrange(line + 1) > (lines / 2)
            eline = 0
        yield None
        oldline = line
        if eline >= limit:
            print_at(" ", col, oldline, black)
            break

def main():
    cascading = set()
    added_new = True
    while True:
        while add_new(cascading): pass
        stopped = iterate(cascading)
        sys.stdout.flush()
        cascading.difference_update(stopped)
        time.sleep(FRAME_DELAY)

def add_new(cascading):
    if randrange(MAX_CASCADES + 1) > len(cascading):
        col = randrange(cols)
        for i in range(randrange(MAX_COLS)):
            cascading.add(cascade((col + i) % cols))
        return True
    return False

def iterate(cascading):
    stopped = set()
    for c in cascading:
        try:
            next(c)
        except StopIteration:
            stopped.add(c)
    return stopped

def doit():
    try:
        init()
        main()
    except KeyboardInterrupt:
        pass
    finally:
        end()

if __name__=="__main__":
    doit()
```

> If you have python3 installed on your hosting device, you can test the python code with the command `python3 app.py`

Now, it is time to write the definition of your dockerfile to create the docker image! To start with, create a file called `dockerfile`.

```yaml
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
CMD ["python3", "app.py"]
```

Before we create the Docker image, make sure that your current working directory contains the dockerfile as well as the python code `app.py`. Now execute the following command:

```bash
docker build -t matrix .
```

> - The command `docker build` creates a Docker image based on the description in the dockerfile.
> - The tag -t stands for tag and assigns a name to the docker image created
> - The last argument of the command refers to our dockerfile. The `.` refers to the current working directory.

Expected output:
```bash
[+] Building 11.1s (10/10) FINISHED                                                                      docker:default
 => [internal] load build definition from dockerfile                                                               0.0s
 => => transferring dockerfile: 542B                                                                               0.0s
 => [internal] load metadata for docker.io/library/alpine:latest                                                   0.0s
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => [1/5] FROM docker.io/library/alpine:latest                                                                     0.0s
 => [internal] load build context                                                                                  0.1s
 => => transferring context: 3.82kB                                                                                0.0s
 => [2/5] RUN apk update                                                                                           1.9s
 => [3/5] COPY . /python                                                                                           0.1s
 => [4/5] RUN apk add --no-cache python3 py3-pip                                                                   8.4s
 => [5/5] WORKDIR /python                                                                                          0.1s
 => exporting to image                                                                                             0.5s
 => => exporting layers                                                                                            0.5s
 => => writing image sha256:eb203eec7f38d35c03fbe16a36e7be73e0424b156b6cda68efef641d4253abec                       0.0s
 => => naming to docker.io/library/matrix                                                                          0.0s
```

> The a closer look at the output. You can see the different layers that docker builds. To read more about this behaviour, take a look at [[Docker-Resources/Resources/Dockerfile|Dockerfile]]

Now we can use our freshly created image to deploy a container with it.
```bash
docker run matrix
```

Exit the container with ``CTRL + C``.

# Summary
It is important to know the correct execution time of the individual Docker commands in order to create a Docker image that works as desired. `FROM, CMD, RUN` and `COPY` are among the elementary commands and can be found in almost every Dockerfile.