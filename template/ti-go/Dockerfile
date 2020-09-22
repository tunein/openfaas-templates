FROM openfaas/classic-watchdog:0.18.1 as watchdog
FROM golang:alpine AS builder

COPY --from=watchdog /fwatchdog /usr/bin/fwatchdog

RUN chmod +x /usr/bin/fwatchdog

# Set necessary environmet variables needed for our image
ENV GO111MODULE=on \
    CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=amd64

# Move to working directory /build
WORKDIR /build

# Copy the code into the container
COPY . .

# Build the application
RUN go build -o main /build/main.go

# Move to /dist directory as the place for resulting binary folder
WORKDIR /dist

# Copy binary from build to main folder
RUN cp /build/main .

# Build a small image
FROM alpine:3.12

COPY --from=builder /usr/bin/fwatchdog .
COPY --from=builder /dist/main /

ENV fprocess="./main"
EXPOSE 8080

# Command to run
ENTRYPOINT ["/fwatchdog"]
