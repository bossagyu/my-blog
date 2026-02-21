+++
title = 'Research on gRPC'
description = 'Introduction to gRPC with Python implementation. Covers Protocol Buffers schema definition, .proto file syntax, and a hands-on Quick Start tutorial.'
date = 2024-09-01T17:53:57+09:00
draft = false
categories = ['Engineering']
tags = ['gRPC']
+++

## What is gRPC?

* gRPC is a protocol developed by Google to implement RPC.
* It uses Protocol Buffers to serialize data, enabling high-speed communication.
* API specifications are pre-defined in a `.proto` file using IDL, from which source code for both the server and client sides is generated.

Differences between REST and gRPC

* REST is resource-oriented, whereas RPC focuses on method invocation, with data being a byproduct.

## Advantages and Disadvantages

### Advantages

- High performance with HTTP/2
- Data transfer via Protocol Buffers
    - Development follows a schema-first approach due to the use of IDL
- Flexible streaming modes

### Disadvantages

- Non-compliance with HTTP/2
- Limited browser support
- Variability in feature implementation depending on the programming language
- Serialized binary data is not human-readable
- REST is also sufficiently fast

## .proto Files

gRPC uses Protocol Buffers as its serialization format.  
Schema definitions are made in files with the `.proto` extension, and code for various languages is generated using the `protoc` command.  
In Protocol Buffers, all values have types, which can be divided into scalar types and message types.

### Scalar Types

* Numeric, string, boolean, byte array

### Message Types

- Message types with multiple fields
- Multiple message types can be defined in a single `.proto` file

```shell
message Person {
    int32 id = 1;
    string name = 2;
    string email = 3;
}
```

## Conducting the gRPC Quick Start

This time, we will perform the gRPC Quick Start using a Python environment.  
https://grpc.io/docs/languages/python/quickstart/

Set up the necessary Python environment.

```shell
python -m pip install grpcio
python -m pip install grpcio-tools
```

Download the sample code.

```shell
git clone -b v1.64.0 --depth 1 --shallow-submodules https://github.com/grpc/grpc
cd grpc/examples/python/helloworld
```

Start the server.

```shell
python greeter_server.py

# Output
Server started, listening on 50051
```

Open another terminal and start the client.

```shell
python greeter_client.py

## Response
Will try to greet world ...
Greeter client received: Hello, you!
```

Communication between the gRPC client and server was successfully established.

## Modifying the .proto File

This time, we will modify the `helloworld.proto` file to add a new method.

Move to the directory containing the `helloworld.proto` file.

```shell
cd grpc/examples/protos
```

Modify the file as follows:

```python
syntax = "proto3";

option java_multiple_files = true;
option java_package = "io.grpc.examples.helloworld";
option java_outer_classname = "HelloWorldProto";
option objc_class_prefix = "HLW";

package helloworld;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}

  // Add the following line
  rpc SayHelloAgain (HelloRequest) returns (HelloReply) {}

  rpc SayHelloStreamReply (HelloRequest) returns (stream HelloReply) {}

  rpc SayHelloBidiStream (stream HelloRequest) returns (stream HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

Generate the gRPC code.

```shell
cd examples/python/helloworld

python -m grpc_tools.protoc -I../../protos --python_out=. --pyi_out=. --grpc_python_out=. ../../protos/helloworld.proto
```

The following files are regenerated.

```shell
ls -l

-rw-r--r--@ 1 xx  xx  1823  9  1 18:12 helloworld_pb2.py
-rw-r--r--@ 1 xx  xx  578  9  1 18:12 helloworld_pb2.pyi
-rw-r--r--@ 1 xx  xx  7018  9  1 18:12 helloworld_pb2_grpc.py
```

The `_pd` file that is updated contains the auto-generated Protocol Buffers definition classes and is generally not manually modified.

Update the `greeter_server.py` file.

```python
from concurrent import futures
import logging

import grpc
import helloworld_pb2
import helloworld_pb2_grpc


class Greeter(helloworld_pb2_grpc.GreeterServicer):
    def SayHello(self, request, context):
        return helloworld_pb2.HelloReply(message="Hello, %s!" % request.name)
    # Add the following function
    def SayHelloAgain(self, request, context):
        return helloworld_pb2.HelloReply(message="Hello Again, %s!" % request.name)

def serve():
    port = "50051"
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    helloworld_pb2_grpc.add_GreeterServicer_to_server(Greeter(), server)
    server.add_insecure_port("[::]:" + port)
    server.start()
    print("Server started, listening on " + port)
    server.wait_for_termination()


if __name__ == "__main__":
    logging.basicConfig()
    serve()
```

Update the `greeter_client.py` file.

```python
def run():
    # NOTE(gRPC Python Team): .close() is possible on a channel and should be
    # used in circumstances in which the with statement does not fit the needs
    # of the code.
    print("Will try to greet world ...")
    with grpc.insecure_channel("localhost:50051") as channel:
        stub = helloworld_pb2_grpc.GreeterStub(channel)
        response = stub.SayHello(helloworld_pb2.HelloRequest(name="you"))
        print("Greeter client received: " + response.message)
        response = stub.SayHelloAgain(helloworld_pb2.HelloRequest(name="you"))
        print("Greeter client received: " + response.message)
```

Restart the server and run the client.

```shell
python greeter_server.py
python greeter_client.py

# Output
Greeter client received: Hello, you!
Greeter client received: Hello Again, you!
```

The newly added method is confirmed to be functioning correctly.

## Summary

This time, we researched gRPC and followed the official documentation tutorial.  
gRPC allows for schema-first development and enables high-performance communication via HTTP/2. Recently, it has been gaining attention as an alternative to REST APIs, making it a technology worth familiarizing yourself with.
