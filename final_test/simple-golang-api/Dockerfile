# Stage 1: Build the Go binary
FROM golang:1.21 AS builder

# Set the Current Working Directory inside the container
WORKDIR /build

# Copy the Go Modules manifests and download dependencies
COPY go.mod go.sum ./
RUN go mod download

# Copy the source code into the container
COPY . .

# Build the Go binary with optimizations
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o main main.go

# Stage 2: Create a minimal image with the Go binary
FROM scratch

# Copy the Go binary from the builder stage
COPY --from=builder /build/main /main

# Expose port 8080 to the outside world
EXPOSE 8080

# Command to run the binary
CMD ["/main"]

