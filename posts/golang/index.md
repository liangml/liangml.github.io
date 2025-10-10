# Golang

# build_packages

## go打包命令
```shell
# 常规打包方法
go build                      
# 使用 “-dflags” 缩小大小
go build -ldflags '-w -s'
# 使用upx打包为最小程序
upx  ...二进制文件
```
### 示例
```shell
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o target/NAME_`date +%Y_%m_%d` ${MAINSRCPATH}
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags '-w -s' -o pkg/PACKAGENAME
```
### multi_platfrom_build
```shell
● Mac 打包Linux windows
$ CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build test.go
$ CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build test.go

● Linux打包Mac windows
$ CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build test.go
$ CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build test.go

● windows编译Linux Mac
$ SET CGO_ENABLED=0SET GOOS=darwin3 SET GOARCH=amd64 go build test.go
$ SET CGO_ENABLED=0 SET GOOS=linux SET GOARCH=amd64 go build test.go
```
> 参数说明<br>
> GOOS：目标可执行程序运行操作系统，支持 darwin，freebsd，linux，windows<br>
> GOARCH：目标可执行程序操作系统构架，包括 386，amd64，arm

## go 代理配置
```shell
go env -w GOPROXY=https://goproxy.cn,direct
```
## fmt 使用
### General
```go
%v // 以默认的方式打印变量的值
%T // 打印变量的类型
```
### Integer
```go
%+d  // 带符号的整型，fmt.Printf("%+d", 255)输出+255
%q   // 打印单引号
%o   // 不带零的八进制
%#o  // 带零的八进制
%x   // 小写的十六进制
%X   // 大写的十六进制
%#x  // 带0x的十六进制
%U   // 打印Unicode字符
%#U  // 打印带字符的Unicode
%b   // 打印整型的二进制
```
### Integer width
```go
%5d  // 表示该整型最大长度是5，下面这段代码
%-5d // 则相反，打印结果会自动左对齐
%05d // 会在数字前面补零。
```
### Float
```go
%f(=%.6f)   // 6位小数点
%e(=%.6e)   // 6位小数点（科学计数法）
%g          // 用最少的数字来表示
%.3g        // 最多3位数字来表示
%.3f        // 最多3位小数来表示
```
### String
```go
%s   //正常输出字符串
%q   //字符串带双引号，字符串中的引号带转义符
%#q  //字符串带反引号，如果字符串内有反引号，就用双引号代替
%x   //将字符串转换为小写的16进制格式
%X   //将字符串转换为大写的16进制格式
%x   //带空格的16进制格式
```
### String Width (以5做例子）
```go
%5s     // 最小宽度为5
%-5s    // 最小宽度为5（左对齐）
%.5s    // 最大宽度为5
%5.7s   // 最小宽度为5，最大宽度为7
%-5.7s  // 最小宽度为5，最大宽度为7（左对齐）
%5.3s   // 如果宽度大于3，则截断
%05s    // 如果宽度小于5，就会在字符串前面补零
```
### Struct
```go
%v    // 正常打印。比如：{sam {12345 67890}}
%+v   // 带字段名称。比如：{name:sam phone:{mobile:12345 office:67890}
%#v   // 用Go的语法打印。
// 比如main.People{name:"sam", phone:main.Phone{mobile:"12345", office:"67890"}}
```
### Boolean
```go
%t // 打印true或false
```

### Pointer
```go
%p   // 带0x的指针
%#p  // 不带0x的指针
```

---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/golang/  

