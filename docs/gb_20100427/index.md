# Gb_20100427

# JSON-RPC：接口的故事

安德鲁·杰朗  
2010 年 4 月 27 日

在这里，我们展示了一个示例，其中 Go 的[接口](https://go.dev/doc/effective_go.html#interfaces_and_types) 可以轻松重构一些现有代码，使其更加灵活和可扩展。最初，标准库的[RPC 包](https://go.dev/pkg/net/rpc/) 使用称为[gob](https://go.dev/pkg/encoding/gob/)的自定义线路格式。对于特定的应用程序，我们希望使用[JSON](https://go.dev/pkg/encoding/json/) 作为替代的有线格式。

我们首先定义了一对接口来描述现有有线格式的功能，一个用于客户端，一个用于服务器（如下所示）。

```
type ServerCodec interface {
 ReadRequestHeader(*Request) error
 ReadRequestBody(interface{}) error
 WriteResponse(*Response, interface{}) error
 Close() error
}
```

在服务器端，我们随后更改了两个内部函数签名以接受`ServerCodec`接口而不是我们现有的`gob.Encoder`. 这是其中之一：

```
func sendResponse(sending *sync.Mutex, req *Request,
 reply interface{}, enc *gob.Encoder, errmsg string)
```

变成了

```
func sendResponse(sending *sync.Mutex, req *Request,
  reply interface{}, enc ServerCodec, errmsg string)
```

然后我们编写了一个简单的`gobServerCodec`包装器来重现原始功能。从那里很容易构建一个`jsonServerCodec`.

在对客户端进行了一些类似的更改之后，这就是我们需要对 RPC 包进行的全部工作。整个练习大约需要 20 分钟！在整理和测试新代码之后，提交了[最终的变更集。](https://github.com/golang/go/commit/dcff89057bc0e0d7cb14cf414f2df6f5fb1a41ec)

在 Java 或 C++ 等面向继承的语言中，显而易见的路径是泛化 RPC 类，并创建 JsonRPC 和 GobRPC 子类。但是，如果您想进一步推广与该层次结构正交，则此方法会变得很棘手。（例如，如果您要实现替代的 RPC 标准）。在我们的 Go 包中，我们采用了一条概念上更简单且需要编写或更改的代码更少的路线。

任何代码库的一个重要品质是可维护性。随着需求的变化，必须轻松而干净地调整您的代码，以免它变得难以处理。我们相信 Go 的轻量级、面向组合的类型系统提供了一种可扩展的结构化代码的方法。

