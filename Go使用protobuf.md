本文更新于2019-01-03。

1. 从[https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases)下载protoc（如：Windows则下载protoc-3.6.1-win32.zip）。protoc命令位于bin目录下。
1. go get [github.com/golang/protobuf](https://github.com/golang/protobuf)。
1. 编译github.com/golang/protobuf/protoc-gen-go。因protoc需调用protoc-gen-go，故需将protoc-gen-go放在环境变量PATH指定的目录中，或protoc所在的目录。
1. 定义proto文件。如：

	```protobuf
	syntax = "proto2";
	package example;
	
	enum FOO { X = 17; };
	
	message Test {
	  required string label = 1;
	  optional int32 type = 2 [default=77];
	  repeated int64 reps = 3;
	}
	```
1. 使用protoc生成go代码，生成的文件名为*.pb.go。

	```shell
	protoc --proto_path=IMPORT_PATH --go_out=DST_DIR *.proto
	```
	* --proto_path：同-I，指定proto文件的目录，缺省则为当前进程目录。
	* --go_out：指定go文件生成目录。
1. 调用（示例中假设生成的go代码位于path/to/example）。

	```go
	package main

	import (
		"log"
		
		"github.com/golang/protobuf/proto"
		
		"path/to/example"
	)

	func main() {
		test := &example.Test{
			Label: proto.String("hello"),
			Type:  proto.Int32(17),
			Reps:  []int64{1, 2, 3},
		}
		data, err := proto.Marshal(test)
		if err != nil {
			log.Fatal("marshaling error: ", err)
		}
		newTest := &example.Test{}
		err = proto.Unmarshal(data, newTest)
		if err != nil {
			log.Fatal("unmarshaling error: ", err)
		}
		// Now test and newTest contain the same data.
		if test.GetLabel() != newTest.GetLabel() {
			log.Fatalf("data mismatch %q != %q", test.GetLabel(), newTest.GetLabel())
		}
		// etc.
	}
	```
