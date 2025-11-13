参考文档：https://gin-gonic.com/zh-cn/docs/

https://github.com/gin-gonic/gin/blob/master/docs/doc.md

# 热加载

热加载就是当我们对代码进行修改时，程序能够自动重新启动并执行

beego中我们可以使用官方的bee工具来热加载项目，但是gin中并没有官方提供的热加载工具，需要借助第三方工具

工具1:http://github.com/gravityblast/fresh

```
go get github.com/pilu/fresh
go install github.com/pilu/fresh@latest
```

终端使用fresh命令实现自动更新

# panic

```
panic("出现错误了！")
```

会立即让程序：

1. **停止执行当前函数；**
2. **依次向上返回（unwind）调用栈中的每一层函数；**
3. 在返回过程中执行所有的 `defer` 语句；
4. 如果最顶层也没有捕获这个 panic，程序就会**崩溃退出**，并打印错误信息和堆栈追踪。

## 调用栈

当函数之间有嵌套调用时：

```
main() → f1() → f2() → panic()
```

`panic` 会从 `f2` 开始，一层层“向上冒泡”，直到 `main()`：

- 如果中间没人拦截它（用 `recover()`），程序就会崩溃；

## 拦截panic

Go 提供了一个内置函数 `recover()`，可以**在 defer 中捕获 panic**，从而避免程序崩溃

# 路由

gin 框架中采用的路由库是基于httprouter(*多路复用器*或简称*mux*)做的

详情参考https://github.com/julienschmidt/httprouter

## 路由构建

```go
func main() {
  router := gin.Default()
  router.GET("/someGet", getting)
  router.POST("/somePost", posting)
  router.PUT("/somePut", putting)
  router.DELETE("/someDelete", deleting)
  router.PATCH("/somePatch", patching)
  router.HEAD("/someHead", head)
  router.OPTIONS("/someOptions", options)
  router.Run(":3000") //监听3000端口 默认监听 0.0.0.0:8080
}
```

**`gin.Default()`**：创建一个默认的 Gin 路由引擎。
 这个默认引擎已经自动使用了两个中间件：

* `Logger`：请求日志记录器

* `Recovery`：在发生 panic 时自动恢复，防止程序崩溃。

`router.GET("/someGet", getting)`注册一个 `GET` 请求的路由 `/someGet`当访问 `http://localhost:3000/someGet` ，就会执行这个函数

## 表单参数

表单传输为post请求

表单参数可以通过PostForm()方法获取，该方法默认解析的是x-www-form-urlencoded或from-data格式的参数

## 上传文件

### 单文件

```go
func main() {
  router := gin.Default()
  router.MaxMultipartMemory = 8 << 20  //判断文件大小，超过 8MB 的文件会被暂存在临时文件中，而不是直接读入内存。
  router.POST("/upload", func(c *gin.Context) { 
    file, _ := c.FormFile("file")
    log.Println(file.Filename)

    c.SaveUploadedFile(file, dst)

    c.String(http.StatusOK, fmt.Sprintf("'%s' uploaded!", file.Filename))
  })
  router.Run(":8080")
}
```

`file, _ := c.FormFile("file")`从表单中获取上传的文件。

FormFile("file") 表示你上传时表单字段的名字是 "file"（必须对应 HTML 中 <input type="file" name="file">）返回一个 *multipart.FileHeader 对象

`log.Println(file.Filename)`输出上传的文件名到控制台日志

` c.SaveUploadedFile(file, dst)`将上传的文件保存到本地 `dst` 是目标路径（例如 `"./uploads/" + file.Filename`）

### 多文件

```go
func main() {
  router := gin.Default()
  router.MaxMultipartMemory = 8 << 20 
  router.POST("/upload", func(c *gin.Context) {
    form, _ := c.MultipartForm()
    files := form.File["upload[]"]

    for _, file := range files {
      log.Println(file.Filename)

      c.SaveUploadedFile(file, dst)
    }
    c.String(http.StatusOK, fmt.Sprintf("%d files uploaded!", len(files))) //完成响应
  })
  router.Run(":8080")
}
```

` form, _ := c.MultipartForm()`解析整个 multipart 表单

`files := form.File["upload[]"]`获取表单字段名为 `"upload[]"` 的所有文件（注意前端表单 `<input type="file" name="upload[]" multiple>` 必须这样写）`files` 是一个 `[]*multipart.FileHeader` 切片，每个元素代表一个文件

## 分组

```go
func main() {
  router := gin.Default()

  {
    v1 := router.Group("/v1") //组1
    v1.POST("/login", loginEndpoint)
    v1.POST("/submit", submitEndpoint)
    v1.POST("/read", readEndpoint)
  } //创建一个路由分组，所有定义的路由都会自动加上 /v1 前缀

  {
    v2 := router.Group("/v2") //组2
    v2.POST("/login", loginEndpoint)
    v2.POST("/submit", submitEndpoint)
    v2.POST("/read", readEndpoint)
  }

  router.Run(":8080")
}
```

## 裸实例路由引擎

```
r := gin.New()
```

`gin.New()` 会返回一个全新的 `*gin.Engine` 对象，也就是 Gin 的“路由核心”。
 它**不会自动加载任何中间件**

* 不会打印日志；

* 不会自动恢复 panic；

* 完全由自己控制中间件和行为

用于大型项目、生产部署、需要自定义中间件链的情况

# 中间件

```go
func main() {
  r := gin.New()
  r.Use(gin.Logger())
  r.Use(gin.Recovery())
  //功能相当于DefaultWriter
  r.GET("/benchmark", MyBenchLogger(), benchEndpoint)
  //执行benchEndpoint函数会优先执行MyBenchLogger()，MyBenchLogger()已经定义
  authorized := r.Group("/")
  authorized.Use(AuthRequired())
  {
    authorized.POST("/login", loginEndpoint)
    authorized.POST("/submit", submitEndpoint)
    authorized.POST("/read", readEndpoint)
    //三个路由都会先执行中间件
    testing := authorized.Group("testing") //子路由
    testing.GET("/analytics", analyticsEndpoint)
  }

  r.Run(":8080")
}
```

`authorized.Use(AuthRequired())`给整个 `authorized` 分组注册中间件 `AuthRequired()`作用：只有在这个分组下的路由，才会触发该中间件

常用于：

- 登录验证；
- Token 检查；
- 权限判断。

## 自定义恢复中间件

```go
r.Use(gin.CustomRecovery(func(c *gin.Context, recovered any) {
    if err, ok := recovered.(string); ok {
        c.String(http.StatusInternalServerError, fmt.Sprintf("error: %s", err))
    }
    c.AbortWithStatus(http.StatusInternalServerError)
})) //当某个请求的处理过程中发生 panic，Gin 会将错误传给 recovered 判断是否是字符串类型的错误

r.GET("/panic", func(c *gin.Context) {
    panic("foo")
}) //故意触发一个 panic，测试上面的自定义恢复机制

```

`gin.CustomRecovery()` 是一个可以自定义 panic 恢复逻辑的中间件。它能捕获程序运行中的 panic，防止服务器直接崩溃

# 日志定向

```go
func main() {
  gin.DisableConsoleColor() //作用：关闭 Gin 默认的彩色日志输出
  f, _ := os.Create("gin.log") // 作用：创建一个日志文件
  //f, _ := os.OpenFile("gin.log", os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644) 追加日志

  gin.DefaultWriter = io.MultiWriter(f) //作用：修改 Gin 的默认日志输出目标
  //gin.DefaultWriter = os.Stdout 输出到控制台
  router := gin.Default()
  router.GET("/ping", func(c *gin.Context) {
      c.String(http.StatusOK, "pong")
  })
  router.Run(":8080")
}

```

## 日志格式

```go
func main() {
  router := gin.New()

  router.Use(gin.LoggerWithFormatter(func(param gin.LogFormatterParams) string { // Gin 提供的一个增强版日志中间件，允许你自定义日志格式
    return fmt.Sprintf("%s - [%s] \"%s %s %s %d %s \"%s\" %s\"\n",
        param.ClientIP,
        param.TimeStamp.Format(time.RFC1123),
        param.Method,
        param.Path,
        param.Request.Proto,
        param.StatusCode,
        param.Latency,
        param.Request.UserAgent(),
        param.ErrorMessage,
    ) //日志输出样式
  }))

  router.Use(gin.Recovery()) //异常恢复中间件，防止程序因为 panic 崩溃

  router.GET("/ping", func(c *gin.Context) {
    c.String(http.StatusOK, "pong")
  })

  router.Run(":8080")
}

```

# 模块绑定和验证

要将请求体绑定到某个类型，请使用模型绑定 目前Gin支持绑定 JSON、XML、YAML、TOML 和标准表单值（例如 foo=bar&boo=baz）

Gin 提供了两组绑定方法：

- 类型\- 必须绑定
  - **方法**- `Bind`，`BindJSON`，`BindXML`，`BindQuery`，`BindYAML`，`BindHeader`，`BindTOML`
  - **行为**- 这些方法`MustBindWith`在底层使用。如果出现绑定错误，请求将被中止`c.AbortWithError(400, err).SetType(ErrorTypeBind)`。这将响应状态码设置为 400，并将`Content-Type`标头设置为`text/plain; charset=utf-8`。请注意，如果在此之后尝试设置响应代码，将会导致警告`[GIN-debug] [WARNING] Headers were already written. Wanted to override status code 400 with 422`。如果您希望更好地控制此行为，请考虑使用`ShouldBind`等效方法。
- 类型\- 应绑定
  - **方法**- `ShouldBind`，`ShouldBindJSON`，`ShouldBindXML`，`ShouldBindQuery`，`ShouldBindYAML`，`ShouldBindHeader`，`ShouldBindTOML`，
  - **行为**- 这些方法`ShouldBindWith`在底层使用。如果出现绑定错误，则会返回该错误，开发人员有责任妥善处理请求和错误。

使用 ` `MustBindWith`or` `ShouldBindWith`可以确定要绑定的内容

## JSON数据绑定

```go
router.POST("/loginJSON", func(c *gin.Context) {
  var json Login
  if err := c.ShouldBindJSON(&json); err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    return
  }
  if json.User != "manu" || json.Password != "123" {
    c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
    return
  }
  c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
})

```

* `ShouldBindJSON(&json)` 自动解析请求体中的 JSON 数据；

* 将字段值映射到结构体 `Login`；

* 如果字段缺失或类型错误，返回 400；

* 检查用户名密码是否正确；

* 返回登录结果

## XML数据绑定

```go
router.POST("/loginXML", func(c *gin.Context) {
  var xml Login
  if err := c.ShouldBindXML(&xml); err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    return
  }
  if xml.User != "manu" || xml.Password != "123" {
    c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
    return
  }
  c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
})

```

- `ShouldBindXML` 会从 XML 请求体中提取数据；
- 自动映射到 `Login` 结构体；
- 验证字段是否存在；
- 检查用户名密码；
- 返回 JSON 格式的结果。

## 表单数据绑定

```go
router.POST("/loginForm", func(c *gin.Context) {
  var form Login
  if err := c.ShouldBind(&form); err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    return
  }
  if form.User != "manu" || form.Password != "123" {
    c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
    return
  }
  c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
})

```

`ShouldBind()` 会根据 `Content-Type` 自动选择合适的绑定方法：

- JSON → `ShouldBindJSON`
- XML → `ShouldBindXML`
- 表单 → `ShouldBindQuery` / `ShouldBindForm`

这是一个更通用的绑定方式

# 绑定Uri

```go
route.GET("/:name/:id", func(c *gin.Context) {
  var person Person
  if err := c.ShouldBindUri(&person); err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"msg": err.Error()})
    return
  }
  c.JSON(http.StatusOK, gin.H{"name": person.Name, "uuid": person.ID})
})

```

**`/:name/:id`**

- 这是路由参数模式。

- URL 中的 `:name` 和 `:id` 是占位符。

- 例如访问：

  ```
  http://localhost:8088/Alice/550e8400-e29b-41d4-a716-446655440000
  ```

**`c.ShouldBindUri(&person)`**

- Gin 会自动将 URL 路径参数（`name` 和 `id`）解析并填充到结构体 `Person` 对应的字段。
- 同时会根据 `binding` 标签自动验证参数是否符合要求。

**错误处理部分**

```
if err := c.ShouldBindUri(&person); err != nil {
  c.JSON(http.StatusBadRequest, gin.H{"msg": err.Error()})
  return
}
```

- 如果参数格式错误（例如 `id` 不是 UUID 格式），Gin 会返回 400 状态码和错误信息。

**正常响应**

```
c.JSON(http.StatusOK, gin.H{"name": person.Name, "uuid": person.ID})
```

- 返回解析出的参数

# XML、JSON、YAML、TOML 和 ProtoBuf 渲染

```go
// gin.H is a shortcut for map[string]any
r.GET("/someJSON", func(c *gin.Context) {
  c.JSON(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
})

```

`gin.H` 是 `map[string]interface{}` 的简写。

这段返回的响应内容是一个 JSON：

```
{
  "message": "hey",
  "status": 200
}
```

`c.JSON` 表示返回 JSON 格式响应

```go
r.GET("/moreJSON", func(c *gin.Context) {
  var msg struct { 
    Name    string `json:"user"`
    Message string
    Number  int
  } //匿名结构体
  msg.Name = "Lena"
  msg.Message = "hey"
  msg.Number = 123
  c.JSON(http.StatusOK, msg)
})

```

字段 `Name` 有 JSON 标签 `json:"user"`。

- 返回时会显示为 `"user"` 而不是 `"Name"`

```go
r.GET("/someXML", func(c *gin.Context) {
  c.XML(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
})

```

```go
r.GET("/someYAML", func(c *gin.Context) {
  c.YAML(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
})

```

```go
r.GET("/someTOML", func(c *gin.Context) {
  c.TOML(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
})

```



```go
r.GET("/someProtoBuf", func(c *gin.Context) {
  reps := []int64{int64(1), int64(2)}
  label := "test"
  data := &protoexample.Test{
    Label: &label,
    Reps:  reps,
  }
  c.ProtoBuf(http.StatusOK, data)
})

```

`protoexample.Test` 是根据 `.proto` 文件生成的结构体。

`c.ProtoBuf()` 会自动把结构体序列化成 **Protocol Buffers** 二进制格式。

常用于微服务或移动端通信（更高效、更小体积）。

响应类型为 `application/x-protobuf`

## SecureJSON

使用 SecureJSON 防止 JSON 劫持。默认情况下，`"while(1),"`如果给定的结构体是数组值，则会在响应正文中添加前缀

```go
r.GET("/someJSON", func(c *gin.Context) {
    names := []string{"lena", "austin", "foo"}

    c.SecureJSON(http.StatusOK, names)
  })
```

## JSONP

使用 JSONP 从不同域的服务器请求数据。如果查询参数回调存在，则将回调添加到响应体中

```go
 r.GET("/JSONP", func(c *gin.Context) {
    data := gin.H{
      "foo": "bar",
    }
    c.JSONP(http.StatusOK, data)
  })

```

## AsciiJSON

使用 AsciiJSON 生成仅包含 ASCII 字符的 JSON，并对非 ASCII 字符进行转义

```go
r.GET("/someJSON", func(c *gin.Context) {
    data := gin.H{
      "lang": "GO语言",
      "tag":  "<br>",
    }
    c.AsciiJSON(http.StatusOK, data)
  })
```

## PureJSON

通常情况下，JSON 会将特殊的 HTML 字符替换为对应的 Unicode 字符，例如 `<code>``<`会替换`\u003c`为 `<code>`。如果您希望按字面值编码这些字符，可以使用 PureJSON。此功能在 Go 1.6 及更低版本中不可用

```go
r.GET("/json", func(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
      "html": "<b>Hello, world!</b>",
    })
  })

  r.GET("/purejson", func(c *gin.Context) {
    c.PureJSON(http.StatusOK, gin.H{
      "html": "<b>Hello, world!</b>",
    })
  })
```

# 提供静态文件

```go
func main() {
  router := gin.Default()
  router.Static("/assets", "./assets") //将本地目录 ./assets 映射到 URL 路径 /assets
  router.StaticFS("/more_static", http.Dir("my_file_system")) //与上面类似，只不过你可以手动传入一个 http.FileSystem 对象
  router.StaticFile("/favicon.ico", "./resources/favicon.ico") //将单个文件 ./resources/favicon.ico 直接映射为 /favicon.ico 路径
  router.StaticFileFS("/more_favicon.ico", "more_favicon.ico", http.Dir("my_file_system")) //和 StaticFile() 类似，但允许你指定文件系统（http.Dir）从指定目录加载文件

  // Listen and serve on 0.0.0.0:8080
  router.Run(":8080")
}
```



# 从文件提取数据

```go
 router.GET("/local/file", func(c *gin.Context) {
    c.File("local/file.go")
  }) //当访问 http://localhost:8080/local/file 时，Gin 会读取你项目中的 local/file.go 文件，然后直接把这个文件的内容发送给客户端（浏览器或其他请求方）。

  var fs http.FileSystem = // ...
  router.GET("/fs/file", func(c *gin.Context) {
    c.FileFromFS("fs/file.go", fs)
  }) //FileFromFS 的作用是：从一个 指定的文件系统（http.FileSystem） 中读取文件并返回
//http.FileSystem 是 Go 标准库的一个接口，你可以用它来访问：本地文件夹嵌入式文件系统（embed.FS）虚拟文件系统（如内存中的文件）
```

# HTML渲染

使用 LoadHTMLGlob() 或 LoadHTMLFiles() 或 LoadHTMLFS()

```go
var templates embed.FS 

func main() {
  router := gin.Default()
  router.LoadHTMLGlob("templates/*")
  router.GET("/index", func(c *gin.Context) {
    c.HTML(http.StatusOK, "index.tmpl", gin.H{
      "title": "Main website",
    })
  })
  router.Run(":8080")
}
```

* `//go:embed templates/*`它可以在 **编译时** 把文件夹中的内容（这里是 `templates` 目录）**打包进可执行文件里**。程序运行时不再需要依赖外部文件。`templates` 是一个 `embed.FS` 类型（内嵌文件系统）

* `router.LoadHTMLGlob("templates/*")`这是 Gin 默认的加载模板方式：

  - 从本地磁盘读取模板文件（运行时加载）。
  - 适合开发阶段使用

* `router.LoadHTMLFS(http.FS(templates), "templates/template1.html", ...)`这里 `http.FS(templates)` 是把 `embed.FS` 转换为 `http.FileSystem`。

  这样 Gin 就能从内嵌的文件系统中加载模板了。

  不依赖文件系统路径，适合**打包发布**

模板/index.tmpl

```html
<html>
  <h1>
    {{ .title }}
  </h1>
</html>
```

## 在不同的目录中使用同名模板

```go
func main() {
  router := gin.Default()
  router.LoadHTMLGlob("templates/**/*") //用于加载模板文件，支持通配符
  router.GET("/posts/index", func(c *gin.Context) {
    c.HTML(http.StatusOK, "posts/index.tmpl", gin.H{
      "title": "Posts",
    })
  })
  router.GET("/users/index", func(c *gin.Context) {
    c.HTML(http.StatusOK, "users/index.tmpl", gin.H{
      "title": "Users",
    })
  })
  router.Run(":8080")
}
```

当访问 `/posts/index` 时：

- 渲染 `templates/posts/index.tmpl`
- 传入一个模板变量 `title: "Posts"`

当访问 `/users/index` 时：

- 渲染 `templates/users/index.tmpl`
- 传入 `title: "Users"`

templates/posts/index.tmpl

```html
{{ define "posts/index.tmpl" }}
<html><h1>
  {{ .title }}
</h1>
<p>Using posts/index.tmpl</p>
</html>
{{ end }}
```

templates/users/index.tmpl

```html
{{ define "users/index.tmpl" }}
<html><h1>
  {{ .title }}
</h1>
<p>Using users/index.tmpl</p>
</html>
{{ end }}
```

## 自定义模板渲染器

使用自己的 HTML 模板渲染

```go
import "html/template"

func main() {
  router := gin.Default()
  html := template.Must(template.ParseFiles("file1", "file2"))
  router.SetHTMLTemplate(html)
  router.Run(":8080")
}
```

## 分隔符

在 Go 的模板系统中，默认的模板变量语法是

```
{{ .title }}
```

但有时候这会与前端框架（例如 **Vue.js、AngularJS**）的语法冲突。比如 Vue 也使用 `{{ message }}`，会造成冲突或渲染异常。所以 Gin 允许你通过 `Delims()` 修改模板的“插值符号”

```go
  r := gin.Default()
  r.Delims("{[{", "}]}")
  r.LoadHTMLGlob("/path/to/templates")
```

# 自定义函数模板

```go
import (
  "fmt"
  "html/template"
  "net/http"
  "time"

  "github.com/gin-gonic/gin"
)

func formatAsDate(t time.Time) string {
  year, month, day := t.Date()
  return fmt.Sprintf("%d/%02d/%02d", year, month, day)
} // 定义一个模板函数 formatAsDate，接收一个 time.Time 类型的参数，并返回格式化的日期字符串，格式为 YYYY/MM/DD

func main() {
  router := gin.Default()
  router.Delims("{[{", "}]}")
  router.SetFuncMap(template.FuncMap{
      "formatAsDate": formatAsDate,
  })
  router.LoadHTMLFiles("./testdata/template/raw.tmpl")

  router.GET("/raw", func(c *gin.Context) {
      c.HTML(http.StatusOK, "raw.tmpl", gin.H{
          "now": time.Date(2017, 07, 01, 0, 0, 0, 0, time.UTC),
      })
  })

  router.Run(":8080")
}

```

# 重定向

重定向：让浏览器自动跳转到另一个 URL

发起HTTP重定向非常简单。内部和外部位置都受支持

```go
r.GET("/test", func(c *gin.Context) {
  c.Redirect(http.StatusMovedPermanently, "http://www.google.com/")
}) //返回一个 301 状态码（永久重定向），并告诉浏览器：“请访问 http://www.google.com/”
```



从 POST 请求发出 HTTP 重定向。请参阅问题：[#444](https://github.com/gin-gonic/gin/issues/444)

```go
r.POST("/test", func(c *gin.Context) {
  c.Redirect(http.StatusFound, "/foo")
})
```

当客户端向服务器发送一个 **POST 请求** 到 `/test` 时，Gin 会执行这个处理函数。处理函数执行 `c.Redirect(http.StatusFound, "/foo")`这意味着：

> “告诉客户端：请跳转到 `/foo` 路由。”



发出路由器重定向指令，使用`HandleContext`方法如下。

```go
r.GET("/test", func(c *gin.Context) {
    c.Request.URL.Path = "/test2" //这行代码直接修改了当前请求的路径，把请求从 /test 改成 /test2
    r.HandleContext(c) //让当前的请求上下文（c）重新交给 Gin 引擎处理一次
})
r.GET("/test2", func(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{"hello": "world"})
})
```

# 自定义中间件

```go
func Logger() gin.HandlerFunc {
  return func(c *gin.Context) {
    t := time.Now()

    c.Set("example", "12345")

    c.Next()

    latency := time.Since(t)
    log.Print(latency)

    status := c.Writer.Status()
    log.Println(status)
  }
}

func main() {
  r := gin.New()
  r.Use(Logger())

  r.GET("/test", func(c *gin.Context) {
    example := c.MustGet("example").(string)
    log.Println(example)
  })
  r.Run(":8080")
}
```

`gin.HandlerFunc` 是中间件的标准函数类型（`func(c *gin.Context)`）。

`c.Next()` 表示“执行下一个中间件或路由处理函数”。

`c.Set("key", value)` 向上下文中写入数据，可以在之后通过 `c.Get` 或 `c.MustGet` 取出。

`c.Writer.Status()` 获取 HTTP 状态码。

`time.Since(t)` 获取时间差，计算请求耗时。

`r.Use()` 将中间件加入全局请求链中，
 每个请求都会先经过 `Logger()` 中的逻辑。

`c.MustGet("example")`：

- 从 context 里取出 `"example"` 的值；
- 若不存在，会直接 panic；
- `(string)` 是类型断言
