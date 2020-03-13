---
title: go 单元测试
date: 2019-11-27 21:08:38
tags:
---

用 `GoLand` 新建项目的时候，我们选择 `Go Modules(vgo)` ，填写我们的项目地址和项目名称，我们命名为 `GinHello` 。

<!--more-->



![新建项目](https://island-hexo.oss-cn-beijing.aliyuncs.com/gin_create_new_project.jpg)新建项目



点击 `Create` ,此时 `Goland` 为我们生成了项目目录，`Go` 项目的目录永远是那么的简单，比 `Java` 的 `Maven` 或者 `Gradle` 生成的项目目录简单多了。

```
GinHello||-go.mod
```

对，就是一个文件 ，一个 `Go module` 文件。`go mod` 是 `Go` 官方引入的一个依赖管理工具。

## 添加依赖

通过 `go mod` 文件进行依赖的。

```
require github.com/gin-gonic/gin v1.4.0
```

我们把上面的依赖进行添加到 `go module` 中， `goLand` 会自动帮我们进行依赖的下载和管理。

## Hello Gin

当完成依赖的添加，就可以开始写代码了。

新建一个 `main.go` 文件。

```
package mainimport (	"github.com/gin-gonic/gin")func main() {	router := gin.Default()	router.Run()}
```

`Gin` 只需要两行代码就可以把我们的服务跑起来。

我们只要点击运行，项目便会启动一个 `8080` 端口，打开浏览器 `localhost:8080` 我们便可以看到页面上提示出 `404 page not found` ，这是因为我们的根路由上并没有返回任何结果。同时我们可以在控制台上看到一些打印信息，其中就包括我们刚刚访问根路由的端口。

## 产生接口

项目已经启动了，那么如何返回一个接口呢？

通过 `router` 的 `Handle` 进行配置我们返回的参数。

```
	// 省略代码	// 添加 Get 请求路由	router.GET("/", func(context *gin.Context) {		context.String(http.StatusOK, "hello gin")	})	// 省略代码
```

此时我们重启项目，重新访问页面 `localhost:8080`，此刻的页面上已经显示了 `hello gin`。

同样，我们还可以进行 `POST`,`PUT`,`DELETE`等请求方式。

## 单元测试

单元测试是项目不能缺少的模块，也是保障项目可以正常运行的重要依赖。下面就对　Gin 进行单元测试。

为了方便单元测试，我们首先要对我们的项目进行一下抽取。

新建立一个文件夹叫做 `initRouter`

建立 `go` 文件 `initRouter.go`

```
package initRouterimport (	"github.com/gin-gonic/gin"	"net/http")func SetupRouter() *gin.Engine {	router := gin.Default()	// 添加 Get 请求路由	router.GET("/", func(context *gin.Context) {		context.String(http.StatusOK, "hello gin")	})	return router}
```

同时修改 `main.go`

```
package mainimport (	"GinHello/initRouter")func main() {	router := initRouter.SetupRouter()	_ = router.Run()}
```

完成了项目测试的初步建立。

建立 `test` 目录, `golang` 的单元测试都是以 `_test` 结尾，建立 `index_test.go` 文件。

```
package testimport (	"GinHello/initRouter"	"github.com/stretchr/testify/assert"	"net/http"	"net/http/httptest"	"testing")func TestIndexGetRouter(t *testing.T) {	router := initRouter.SetupRouter()	w := httptest.NewRecorder()	req, _ := http.NewRequest(http.MethodGet, "/", nil)	router.ServeHTTP(w, req)	assert.Equal(t, http.StatusOK, w.Code)	assert.Equal(t, "hello gin", w.Body.String())}
```

通过 `assert` 进行断言，来判断返回状态码和返回值是否与代码中的值一致。

此时的项目目录为：

```
GinHello||-initRouter|  |-initRouter.go||-test|  |-index_test.go||-main.go|-go.mod|-go.sum
```

运行单元测试，控制台打印出单元测试结果。

> — PASS: TestIndexGetRouter (0.02s)
> PASS