# Building docker image effeciently

#### docker image
Make docker image as small as possible
Can be started, stopped and restarted

## Multi-stage Builds
Multi stage builds have more thean one FROM directive in Dockerfile with each FROM directive starting a new stage

Each stage begins completely new set of file system layers, allowing you to selectively copy only the files you need from previous layers

You can use --from flag with COPY to copy files from previous stage..COPY --from=0

YOu can also name your stage with FROM ... AS and reference the name with COPY --from=...

## Let's create a effeient GO app

Create a dcocker file with following
```
FROM golang:1.12.4 AS compiler

WORKDIR /helloworld

COPY main.go .

RUN GOOS=linux go build -a -installsuffix cgo -o main .

FROM alpine:3.9.3

WORKDIR /root

COPY --from=compiler /helloworld/helloworld .

CMD ["./helloworld"]
```

Create a image and check the image size. Its aroudn 700MB

Now create another Dockerfile and put following in it
```
# First stage
FROM golang:1.12.4 AS compiler

WORKDIR /helloworld

COPY main.go .

RUN GOOS=linux go build -a -installsuffix cgo -o main .

# First stage is responsible for producing the compiled package that's it

# Second Stage (It is responsible for running the package.)
FROM alpine:3.9.3

WORKDIR /root

COPY --from=compiler /helloworld/helloworld .

CMD ["./helloworld"]
```

Now build image from the Dockerfile and see what is the size. We divided the docker build into two stages. First stage create the executable file and second stage uses the executable files 

```
docker image ls

REPOSITORY                     TAG                 IMAGE ID            CREATED              SIZE
go2                            latest              4f449c132fe5        15 seconds ago       7.53MB
```