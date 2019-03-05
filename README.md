## Source
This project is custom of https://github.com/favadi/protoc-go-inject-tag

## Why?

- Golang [protobuf](https://github.com/golang/protobuf) doesn't support
[custom tags to generated structs](https://github.com/golang/protobuf/issues/52).
- In auto gen swagger doc use tag for description, example, valid ...

## Install
*  `go get github.com/Beeketing/bee-protoc-inject-tag`

## Usage

Add a comment with syntax 
- `// @description: "this is description"`
- `// @required`
- `// @valid: "1,2,3,4"`
- `// @example: "example_value"`


before fields to add custom tag to in .proto files.

Example:

```
// file: test.proto
syntax = "proto3";

package pb;

message Sample {
    int64 id = 1;
    // @required
    // @description: "description"
    string message = 2;
}
```

Generate with protoc command as normal.

```
protoc --go_out=. test.proto
```

Run `bee-protoc-inject-tag` with generated file `test.pb.go`.

```
bee-protoc-inject-tag -input=./test.pb.go
```

The custom tags will be injected to `test.pb.go`.

```
type SampleResponse struct {
	Id int64 `protobuf:"varint,1,opt,name=id" json:"id,omitempty"`
	// @required: "true"
	// @description: "2212312"
	Message              string         `protobuf:"bytes,2,opt,name=message" json:"message,omitempty" required:"true" description:"description"`
	Sample               *StreamRequest `protobuf:"bytes,3,opt,name=sample" json:"sample,omitempty"`
	XXX_NoUnkeyedLiteral struct{}       `json:"-"`
	XXX_unrecognized     []byte         `json:"-"`
	XXX_sizecache        int32          `json:"-"`
}
```

To skip the tag for the generated XXX_* fields, use `-XXX_skip=yaml,xml` flag.
