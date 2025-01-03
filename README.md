# goss

✨ `goss` 是一个简洁的云存储 golang 库，兼容 amazon s3 协议，支持但不限于**阿里云**、**腾讯云**、**七牛云**、**华为云**、**aws s3**、**minio**。

[![Go Reference](https://pkg.go.dev/badge/github.com/huagelong/goss.svg)](https://pkg.go.dev/github.com/huagelong/goss)
[![Go Report Card](https://goreportcard.com/badge/github.com/huagelong/goss)](https://goreportcard.com/report/github.com/huagelong/goss)
[![Go](https://github.com/huagelong/goss/actions/workflows/go.yml/badge.svg)](https://github.com/huagelong/goss/actions/workflows/go.yml)
[![codecov](https://codecov.io/gh/huagelong/goss/branch/main/graph/badge.svg?token=UU4lLD2n4k)](https://codecov.io/gh/huagelong/goss)
[![GitHub license](https://img.shields.io/github/license/huagelong/goss)](https://github.com/huagelong/goss/blob/main/LICENSE)
![GitHub go.mod Go version](https://img.shields.io/github/go-mod/go-version/huagelong/goss)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/huagelong/goss)


## 🚀 安装

```shell
go get -u github.com/huagelong/goss
```


## ⚙️ 配置

所有支持的配置项：

```go
type Config struct {
    Endpoint          string `yaml:"endpoint"`
    AccessKey         string `yaml:"access_key"`
    SecretKey         string `yaml:"secret_key"`
    Region            string `yaml:"region"`
    Bucket            string `yaml:"bucket"`

    // 如果是使用 minio，并且没有使用 https，需要设置为 true
    UseSsl            *bool  `yaml:"use_ssl"`
    // 如果是使用 minio，需要设置为 true
    HostnameImmutable *bool  `yaml:"hostname_immutable"`
}
```

配置的方式，在创建实例的时候通过 `WithConfig` 来传递：

```go
goss, err := goss.New(goss.WithConfig(&goss.Config{
    Endpoint: "",
    AccessKey: "",
    SecretKey: "",
    Region: "",
    Bucket: "",
}))
```


## 💡 基本用法

1. 你可以通过下面的代码来导入 `goss`:

```go
import "github.com/huagelong/goss"
```

2. 使用之前需要创建实例：

```go
goss, err := goss.New(goss.WithConfig(&Config{
    Endpoint: "",
    AccessKey: "",
    SecretKey: "",
    Region: "",
    Bucket: "",
}))
```

3. 使用

```go
// goss.GetString 会获取路径指定的文件，返回字符串
fmt.Println(goss.GetString(context.TODO(), "test/foo.txt"))
```


## 📚 接口

`goss` 支持以下操作：

- [Put](#Put)
- [PutFromFile](#PutFromFile)
- [Get](#Get)
- [GetString](#GetString)
- [GetBytes](#GetBytes)
- [GetToFile](#GetToFile)
- [Delete](#Delete)
- [Exists](#Exists)
- [Files](#Files)
- [Size](#Size)

### Put

上传文件到云存储。第一个参数是 `key`，第二个参数是 `io.Reader`。

```go
data := []byte("this is some data stored as a byte slice in Go Lang!")
r := bytes.NewReader(data)
err := goss.Put(context.TODO(), "test/test.txt", r)
```

### PutFromFile

上传文件到云存储。第一个参数是 `key`，第二个参数是本地文件路径。

```go
err := goss.PutFromFile(context.TODO(), "test/test.txt", "/path/to/test.txt")
```

### Get

从云存储获取文件。参数是 `key`。返回值是 `io.ReadCloser` 和 `error`。

```go
// rc 是 `io.ReadCloser`
rc, err := goss.Get(context.TODO(), "test/test.txt")
defer rc.Close()

bs, err := io.ReadAll(rc)
fmt.Println(string(bs))
```

### GetString

从云存储获取文件。参数是 `key`。返回值是 `string` 和 `error`

```go
content, err := goss.GetString(context.TODO(), "test/test.txt")
fmt.Println(content)
```

### GetBytes

从云存储获取文件。参数是 `key`。返回值是 `[]byte` 和 `error`

```go
bs, err := goss.GetBytes(context.TODO(), "test/test.txt")
fmt.Println(string(bs))
```

### GetToFile

下载云存储文件到本地。第一个参数是 `key`，第二个参数是本地路径。

```go
// 第一个参数是云端路径，第二个参数是本地路径
err := goss.GetToFile(context.TODO(), "test/test.txt", "/path/to/local")
```

### Delete

删除云存储文件。

```go
err := goss.Delete(context.TODO(), "test/test.txt")
```

### Exists

判断云存储文件是否存在。

```go
exists, err := goss.Exists(context.TODO(), "test/test.txt")
```

### Files

根据前缀获取文件列表。

```go
exists, err := goss.Files(context.TODO(), "test/")
```

### Size

获取云存储文件大小。

```go
size, err := goss.Size(context.TODO(), "test/test.txt")
```

## 参考文档

1. [阿里云对象存储](https://help.aliyun.com/product/31815.html)
2. [腾讯云对象存储](https://cloud.tencent.com/document/product/436)
3. [七牛云对象存储](https://developer.qiniu.com/kodo)
4. [华为云对象存储](https://support.huaweicloud.com/obs/index.html)
5. [aws s3](https://docs.aws.amazon.com/sdk-for-go/api/service/s3/)
6. [minio](https://github.com/minio/minio)

## 各云厂商对 s3 的支持

1. [阿里云: OSS与Amazon S3的兼容性](https://help.aliyun.com/document_detail/389025.html)
2. [腾讯云: 使用 AWS S3 SDK 访问 COS](https://cloud.tencent.com/document/product/436/37421)
3. [七牛云: AWS S3 兼容](https://developer.qiniu.com/kodo/4086/aws-s3-compatible)
4. 华为云：支持 s3，但是官网文档找不到相关关于 s3 兼容的相关描述
5. [minio: AWS S3 Compatibility](https://min.io/product/s3-compatibility)

## 注意事项

1. 七牛云的 `endpoint` 和 `region` 配置请参考这个文档：[AWS S3 兼容 - 服务域名](https://developer.qiniu.com/kodo/4088/s3-access-domainname)

## Changelog

### [4.0.0] - 2023-09-28

#### Changed

- 所有方法添加 `context.Context` 作为第一个参数。
