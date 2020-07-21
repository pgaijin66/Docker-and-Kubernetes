# First stage
FROM golang:1.12.4 AS compiler

WORKDIR /helloworld

COPY main.go .

RUN GOOS=linux go build -a -installsuffix cgo -o main .

# First stage is responsible for producing the compiled package that's it

# Second Stage (It is responsible for running the package.)
FROM alpine:3.9.3

WORKDIR /root

COPY --from=compiler /helloworld/main .

CMD ["./helloworld"]