本文更新于2023-09-15。

翻译自Command go官方文档（[https://golang.org/cmd/go/](https://golang.org/cmd/go/)，国内可使用[https://golang.google.cn/cmd/go/](https://golang.google.cn/cmd/go/)；同理，文中golang.org的链接也可使用golang.google.cn替换）。章节段落结构稍作改变，对应的go版本为1.21。

[TOC]

# go

```shell
go <command> [arguments]
```

go是管理Go源代码的工具。

command可参阅 **go help——查看帮助信息** 小节。

# go bug——启动bug报告

```shell
go bug
```

打开默认浏览器并启动新的bug报告，报告包括有用的系统信息。

# go build——编译包及其依赖包

```shell
go build [-o output] [build flags] [packages]
```

编译指定导入路径的包，以及其依赖包，但不安装结果。

如果构建的参数为单个目录下的.go文件列表时，会将它们当作指定单个包的源文件列表。

当编译包时，忽略以“_test.go”结尾的文件。

当编译单个main包时，生成的可执行文件会被写入到以第一个源文件（如：“go build ed.go rx.go”写入“ed”或“ed.exe”）或源代码目录名（如：“go build unix/sam”写入“sam”或“sam.exe”）命名的输出文件中。当写入Windows下的可执行文件时会添加“.exe”后缀。

当编译多个包或单个非main包时，build编译包但会丢弃目标对象文件，行为只用于类似检查包是否可以构建。

标志（flag）：

* -i：同时安装目标的依赖包。-i标志被反对使用。编译的包会自动被缓存。
* -o output：强制将结果可执行文件或对象文件写入指定名字的输出文件或目录，而不是在本节最上两个段落所描述的默认行为。如果指定的名字为一个已存在的目录或者以斜杠或反斜杠结尾，则所有结果可执行文件将写入到该目录中。

构建标志（build flags）会被build、clean、get、install、list、run、test命令共享：

* -a：强制重新构建已经是最新的包。
* -asan：启用与Address Sanitizer的互操作。只支持linux/arm64或linux/amd64，且只支持GCC 7及更高版本或Clang/LLVM 9及更高版本。
* -asmflags '[pattern=]arg list'：传递给每次go tool asm调用的参数。
* -buildmode mode：使用的构建模式，更多信息参阅“go help buildmode”。
* -buildvcs：是否用版本控制信息标记二进制文件（“true”、“false”，或“auto”）。默认情况下（“auto”），如果main包，包含它的主模块，以及当前目录全都在同一个仓库中，版本控制信息会被标记入二进制文件。使用-buildvcs=false来总是忽略版本控制信息，或-buildvcs=true则如果版本控制信息是可用的但因为缺少工具或不明确的目录结构而不能被包含，就会出错。
* -C dir：在运行命令前切换至dir目录。在命令行上指定名字的任何文件都在切换目录之后被解析。如果被使用，此标志必需为命令行中的第一个。
* -compiler name：使用的编译器名，即runtime.Complier（gccgo或gc）。
* -cover：启用代码覆盖率检测。
* -covermode set,count,atomic：设置覆盖率分析的模式。默认为“set”除非-race启用，这种情况下它为“atomic”。会设置-cover。值可为：
	* atomic：int类型，类似count，但在多线程测试中正确；明显地更加昂贵。
	* count：int类型，此语句运行多少次？
	* set：bool类型，此语句是否运行？
* -coverpkg pattern1,pattern2,pattern3：对于以“main”包为目标的构建（例如构建一个Go可执行文件），应用覆盖率分析至每个匹配模式的包。默认为应用覆盖率分析至在主Go模块中的包。关于包模式的描述参阅“go help packages”。会设置-cover。
* -gccgoflags '[pattern=]arg list'：传递给每次gccgo编译器/链接器调用的参数。
* -gcflags '[pattern=]arg list'：传递给每次go tool complie调用的参数。
* -installsuffix suffix：在包安装目录名字中使用的后缀，以便使输出文件与默认构建分开。如使用-race标志，安装后缀会自动设置为race，或如果显式设置则在其后追加_race。对-msan和-asan也同样。使用需要使用非默认编译标志的-buildmode选项也有类似效果。
* -ldflags '[pattern=]arg list'：传递给每次go tool link调用的参数。
* -linkshared：链接到的之前使用-buildmode=shared生成的共享库。
* -mod mode：使用的模块下载模式：readonly、vendor或mod。默认情况下，如果vendor目录存在且go.mod中的go版本为1.14或更高，go命令表现如同-mod=vendor被设置一样。否则，go命令表现如同-mod=readonly被设置一样。详情参阅[https://golang.org/ref/mod#build-commands](https://golang.org/ref/mod#build-commands)。
* -modcacherw：令保留在模块缓存中的新创建的目录可读写，而不是令它们只读。
* -modfile file：在模块感知模式下，读取（并且可能写入）备用的go.mod文件而不是在模块根目录下的go.mod文件。名字为“go.mod”的文件仍然必须存在用来确定模块根目录，但其不会被访问。当-modfile被指定，备用的go.sum文件也会被使用：其路径从-modfile标志产生，通过去除“.mod”扩展名并追加“.sum”。
* -overlay file：读取为构建操作提供覆盖的JSON配置文件。文件是只有一个字段的JSON结构，名为“Replace”，映射每个磁盘文件路径（一个字符串）至其后备文件路径，以使构建运行起来如同磁盘文件路径存在有后备文件路径提供的内容，或如果其后备文件路径为空则如同磁盘文件路径不存在。对-overlay标志的支持有一些限制：重要的是，从包含路径之外包含的cgo文件必需与Go包被包含于相同的目录，且当二进制和测试分别通过go run和go test运行时覆盖不会出现。
* -msan：启用与Memory Sanitizer的互操作，只支持linux/amd64、linux/arm64、freebsd/amd64，并且只能使用clang/llvm作为宿主C编译器。PIE构建模式将在除了linux/amd64的所有平台上使用。
* -n：将实际需执行的命令打印出来但不运行。
* -p n：可以并行运行的程序——如构建命令或测试二进制文件——的数量。默认为GOMAXPROCS，通常是可用的CPU的数量。
* -pgo file：为profile-guided optimization（PGO）指定profile的文件路径。当特殊的名字“auto”被指定时，对构建中的每个main包，go命令选择在包的目录中的名字为“default.pgo”的文件——如果该文件存在，并应用其至main包的（传递）依赖（其它包不被影响）。特殊的名字“off”关闭PGO。默认为“auto”。
* -pkgdir dir：从dir而不是通常的位置安装和加载所有包（pkg）。例如，当使用非标准设置构建时，使用-pkgdir来令在特定的目录生成包。
* -race：启用数据竞态检测，只支持linux/amd64、freebsd/amd64、darwin/amd64、darwin/arm64、windows/amd64、linux/ppc64le、linux/arm64（只对48位VMA）。
* -tags tag,list：构建期间需满足的额外构建标记的逗号分隔的列表。关于构建标记的更多信息，参阅“go help buildconstraint”。（早期Go版本使用空白分隔的列表，已经被反对使用，但仍然能识别。）
* -toolexec 'cmd args'：用来调用如vet和asm等工具链程序的程序。例如，go命令将运行“cmd args /path/to/asm <arguments for asm>”，而不是运行asm。TOOLEXEC_IMPORTPATH环境变量将被设置，其匹配正在构建的包的“go list -f {{.ImportPath}}”。
* -trimpath：从目标可执行文件中移除所有文件系统路径。作为文件系统绝对路径的替代，记录下的文件名将以“模块路径@版本”（当使用模块），或“直接的import路径”（当使用标准库，或GOPATH）开头。
* -v：打印编译的包名。
* -work：打印临时工作目录并在退出时不删除之。
* -x：同时打印实际执行的命令。

-asmflags、-gccgoflags、-gcflags、-ldflags接受一个以空白分隔的参数列表，用以在构建时传递给底层工具。要在列表元素中嵌入空白，需使用单引号或双引号括起。参数列表前面可以是一个包模式和一个等号，它将该参数列表的使用限制为与该模式匹配的包（有关包模式的说明，请参阅“go help packages”）。如果没有模式，参数列表只应用于命令行中命名的包。这些标志可以使用不同的模式重复指定，以便为不同的包指定不同的参数。如果一个包与多个标志中给定的模式匹配，则使用最后一个指定的。例如：“go build -gcflags=-S fmt”只打印fmt包的反汇编，“go build -gcflags=all=-S fmt”则打印fmt包及其所有依赖包的反汇编。

有关指定包的详细信息，参阅“go help packages”。有关包和二进制文件的安装位置的详细信息，参阅“go help gopath”。有关Go和C/C++之间调用的详细信息，参阅“go help c”。

注意：构建遵守某些约定，例如在“go help gopath”中描述的。然而，并不是所有项目都能遵守这些约定。使用自己的约定来安装，或使用一个单独的软件构建系统，可以选择使用低级别的调用，如“go tool compile”和“go tool link”，来避免构建工具的开销和设计决策。

参阅：go install，go get，go clean。

# go clean——删除对象文件和缓存文件

```shell
go clean [clean flags] [build flags] [packages]
```

从包源代码目录中删除对象文件。go命令在临时目录中构建大多数对象文件，因此go clean主要关心通过其他工具或通过手动调用go build遗留下来的对象文件。

如果给出packages参数或者-i或-r标志，clean将从与导入路径对应的每个源文件目录中删除以下文件：

* _obj/：旧的对象文件目录，来自Makefile。
* _test/：旧的测试文件目录，来自Makefile。
* _testmain.go：旧的gotest文件，来自Makefile。
* test.out：旧的测试日志，来自Makefile。
* build.out：旧的测试日志，来自Makefile。
* *.[568ao]：对象文件，来自Makefile。
* DIR(.exe)：来自go build。
* DIR.test(.exe)：来自go test -c。
* MAINFILE(.exe)：来自go build MAINFILE.go。
* *.so：来自SWIG。

在上述列表中，DIR表示目录路径的最后一个元素，MAINFILE表示目录中任何在构建包时未包含的go源文件的基本名。

标志：

* -cache：删除整个go build缓存。
* -fuzzcache：删除为模糊测试存储在Go构建缓存中的文件。模糊测试引擎缓存扩展代码覆盖范围的文件，因此删除它们可能令到模糊测试降低效果，直到找到提供同样覆盖范围的新的输入。这些文件不同于那些存储在testdata目录中的文件；clean不删除那些文件。
* -i：删除相关的已安装的归档文件或二进制文件（由“go install”创建）。
* -modcache：删除整个模块下载缓存，包括指定版本依赖的已解压的源代码。
* -n：将实际需执行的命令打印出来但不运行。
* -r：递归地应用于所有由导入路径指定的依赖包。
* -testcache：令go build缓存中所有测试结果过期。
* -x：同时打印实际执行的命令。

关于构建标志的更多信息，参阅“go help build”。

关于指定包的更多信息，参阅“go help packages”。

# go doc——查看包或符号的文档

```shell
go doc [doc flags] [package|[package.]symbol[.methodOrField]]
```

打印由参数（包、常量、函数、类型、变量、方法、结构体字段）指定的项相关的文档注释，该项后跟该项下的每个第一级项（该项为包即为包级声明，该项为类型即为其方法，依次类推）的一行概要。

doc命令接受0个、1个或2个参数。

如使用0个参数，即运行：

```shell
go doc
```

会打印当前目录下包的包文档。如果包是命令的包（main包），包的导出符号会从展示输出中省略，除非指定了-cmd标志。

当使用1个参数运行时，参数会被视为要展示文档的项的Go语法表示。参数指定的项取决于GOROOT和GOPATH中安装的内容，以及参数的格式。参数的格式如下所示：

```shell
go doc <pkg>
go doc <sym>[.<methodOrField>]
go doc [<pkg>.]<sym>[.<methodOrField>]
go doc [<pkg>.][<sym>.]<methodOrField>
```

上述列表中参数匹配的第一项是待打印文档的项。（参见下面的示例。）但是，如果参数是以大写字母开头的，则假设其表示当前目录中的符号或方法。

对于包，扫描顺序是从词汇上按广度优先确定的。也就是说，呈现的包是匹配搜索的其中一个，并且为最接近搜索根，在其层次结构中搜索到的从词汇上来说的第一个。GOROOT树总是在GOPATH之前被完整扫描。

如果没有包被指定或被匹配，则选择当前目录下的包，因此“go doc Foo”显示当前目录中符号为Foo的文档。

包路径必须是限定路径或者是路径的正确后缀。go工具通常的包机制不适用于：类似“.”和“...”的包路径元素，是不被go doc实现的。

当使用2个参数运行时，第一个参数是一个包路径（完整的路径或后缀），第二个参数是一个符号、或带有方法或结构体字段的符号：

```shell
go doc <pkg> <sym>[.<methodOrField>]
```

在所有的形式中，当匹配符号时，参数的小写字母不区分大小写，但大写字母只匹配大写。这意味着如果不同的符号有不同的大小写，则包中小写字母参数可能有多个匹配。如果这种情况发生，将打印所有匹配项的文档。

示例：

* go doc：显示当前包的文档。
* go doc Foo：显示当前包中Foo的文档。（Foo以大写字母开头，因此其不能匹配为一个包路径。）
* go doc encoding/json：显示encoding/json包的文档。
* go doc json：encoding/json的简写。
* go doc json.Number（或go doc json.number）：显示json.Number的文档和方法概要。
* go doc json.Number.Int64（或go doc json.number.int64）：显示json.Number的Int64方法的文档。
* go doc cmd/doc：显示doc命令的包文档。
* go doc -cmd cmd/doc：显示doc命令的包文档和导出符号。
* go doc template.new：显示html/template的New函数的文档。（html/template从词汇上来说位于text/template之前。）
* go doc text/template.new（一个参数）：显示text/template的New函数的文档。
* go doc text/template new（两个参数）：显示text/template的New函数的文档。

至少在当前的包树结构中，以下调用均会打印json.Decoder的Decode方法的文档：

* go doc json.Decoder.Decode
* go doc json.decoder.decode
* go doc json.decode
* cd go/src/encoding/json; go doc decode

标志：

* -all：显示包的所有文档。
* -c：匹配符号时区分大小写。
* -cmd：将命令（main包）当作普通包看待。否则当显示包的顶层文档时，main包的导出符号将被隐藏。
* -short：每个符号一行表示。
* -src：显示符号的完整源代码。这将显示其声明和定义的完整Go代码，如函数定义（包括函数体），类型声明或闭包引用的常量块。输出可能因此包含未导出的细节。
* -u：像导出符号、方法、字段一样显示未导出的符号、方法、字段的文档。

# go env——打印环境变量

```shell
go env [-json] [-u] [-w] [var ...]
```

打印Go环境变量信息。

env默认像shell脚本（在Windows下，像批处理文件）一样打印信息。如果一个或多个变量名作为参数给定，env将为每个指定名字的变量新起一行打印其值。

标志：

* -json：以JSON格式打印环境变量，而不是像shell脚本一样。
* -u：需要一个或多个参数，如果指定名字的环境变量已经使用“go env -w”设置，则取消其默认设置。
* -w：需要一个或多个格式为NAME=VALUE的参数，使用指定的值修改指定名字的环境变量的默认设置。

关于环境变量的更多信息，参阅“go help environment”。

# go fix——使用新的API更新包代码

```shell
go fix [-fix list] [packages]
```

在通过导入路径指定的包上运行Go fix命令。

标志：

* -fix：设置需要运行的修复的逗号分隔的列表。默认为所有已知的修复。（其值被传递至“go tool fix -r”。）

关于fix的更多信息，参阅“go doc cmd/fix”。关于指定包的更多信息，参阅“go help packages”。

如需使用其它选项，运行“go tool fix”。

参阅：go fmt，go vet。

# go fmt——gofmt（重新格式化）包代码

```shell
go fmt [-n] [-x] [packages]
```

在导入路径指定的包上运行“gofmt -l -w”命令。将打印被修改的文件名。

关于gofmt的更多信息，参阅“go doc cmd/gofmt”。关于指定包的更多信息，参阅“go help packages”。

标志：

* -mod：标志的值设置要使用那种模块下载模式：readonly还是vendor。更多信息参阅“go help modules”。
* -n：将实际需执行的命令打印出来但不运行。
* -x：同时打印实际执行的命令。

如需使用特定的选项，直接运行gofmt。

参阅：go fix，go vet。

# go generate——通过执行源代码生成Go文件

```shell
go generate [-run regexp] [-n] [-v] [-x] [build flags] [file.go... | packages]
```

通过在现存文件中描述的指令执行命令。命令可以运行任何进程，但目的是创建或更新Go源文件。

go generate永远不会通过go build、go test等自动运行。必须明确地运行。

go generate扫描文件搜索指令，指令是如下格式的行：

```
//go:generate command argument...
```

(注意：开头没有空白，“//go”中间也没有空白)其中command是要运行的生成器，对应于可在本地运行的可执行文件。其必须在shell PATH中（如gofmt），或为一个完全限定路径（如/usr/you/bin/mytool），或为一个命令别名，如下所述。

注意go generate不解析文件，因此在注释或多行字符串中看起来像指令的行将会被视为指令。

指令的参数是空白分隔的记号，记号可为双引号引起的字符串，在生成器运行时每个记号作为独立的参数传递给生成器。

带引号的字符串使用Go语法，并在执行前进行计算；一个带引号的字符串作为生成器的一个单独的参数。

为了向人类和机器工具传达，代码是自动生成的，生成的源代码应有一行匹配如下的正则表达式（以Go语法）：

```
^// Code generated .* DO NOT EDIT\.$
```

该行必需出现在文件中第一行非注释、非空行文本之前。

go generate在运行生成器时会设置几个环境变量：

* $GOARCH：执行时的体系结构（arm、amd64等）。
* $GOOS：执行时的操作系统（linux、windows等）。
* $GOFILE：文件的基本名（base name）。
* $GOLINE：指令在源文件中的行号。
* $GOPACKAGE：包含指令的文件的包名。
* $GOROOT：给调用生成器的“go”命令的GOROOT目录，包含Go工具链和标准库。
* $DOLLAR：一个美元符号。
* $PATH：父进程的$PATH，带有$GOROOT/bin放置于开头。这令到执行“go”命令的生成器与父“go generate”命令使用相同的“go”。

除了变量替换和带引号的字符串求值，在命令行上不会执行诸如“globbing”（文件名通配符匹配）之类的特殊处理。

作为执行命令前的最后一步，对任何以字母数字命名的环境变量（如$GOFILE或$HOME）的任何调用，均会在命令行中展开。在所有的操作系统上环境变量名的展开语法都类似$NAME这样。由于求值的顺序，环境变量甚至在带引号的字符串中也会展开。如果名字为NAME的环境变量未设置，$NAME会展开为空字符串。

如下形式的指令：

```
//go:generate -command xxx args...
```

表示，只对于此源文件的接续部分，字符串xxx代表由参数指定的命令。这可以用来创建别名或处理多个单词的生成器。例如：

```
//go:generate -command foo go tool foo
```

表示，命令“foo”代表生成器“go tool foo”。

generate按照命令行中给定的顺序处理包，一次一个。如果命令行列出一个单独的目录中的.go文件，它们会作为一个单独的包对待。在包中，generate按照包中文件名的顺序处理源文件，一次一个。在一个源文件中，generate按照在文件中出现的顺序运行生成器，一次一个。go generate工具还会设置构建标记“generate”，以便文件可以被go generate审查但会在构建时忽略。

对于带有无效代码的包，generate只处理带有有效包子句的源文件。（译注：未看懂）

如果任何一个生成器返回了错误退出状态，“go generate”会跳过该包所有接续的处理。

生成器运行在包的源代码目录中。

标志：

* -run=""：如果非空，指定一个正则表达式来选择指令，该指令的完整原始源文本（忽略结尾处的空白和最后的换行）匹配正则表达式。
* -skip=""：如果非空，指定一个正则表达式来禁止指令，该指令的完整原始源文本（忽略结尾处的空白和最后的换行）匹配正则表达式。如果指令既匹配-run也匹配-skip参数，其会被忽略。

也接受标准的构建标志，包括-v、-n和-x。

* -n：将实际需执行的命令打印出来但不运行。
* -v：打印正在处理的包名和文件名。
* -x：同时打印实际执行的命令。

关于构建标志的更多信息，参阅“go help build”。

关于指定包的更多信息，参阅“go help packages”。

# go get——添加依赖包至当前模块并安装之

```shell
go get [-t] [-u] [-v] [build flags] [packages]
```

get解析其命令行参数为特定模块版本的包，更新go.mod来依赖于那些版本，并下载源代码至模块缓存。

要添加对包的依赖或更新其至最后的版本：

```shell
go get example.com/pkg
```

要更新或下载包至特定的版本：

```shell
go get example.com/pkg@v1.2.3
```

要移除对模块的依赖并降级依赖于它的模块：

```shell
go get example.com/mod@none
```

要升级最低要求的Go版本至最新发布的Go版本：

```shell
go get go@latest
```

要升级Go工具链至当前Go工具链的最新补丁发布版本：

```shell
go get toolchain@patch
```

详情参阅[https://golang.org/ref/mod#go-get](https://golang.org/ref/mod#go-get)。

在Go的早期版本，“go get”被用于构建和安装包。现在，“go get”专用于调整go.mod中的依赖。代替之，“go install”可用于构建和安装命令。当版本被指定时，“go install”运行在模块感知模式并忽略当前目录中的go.mod文件。例如：

```
go install example.com/pkg@v1.2.3
go install example.com/pkg@latest
```

详情参阅“go help install”或[https://golang.org/ref/mod#go-install](https://golang.org/ref/mod#go-install)。

“go get”接受以下的标志：

* -t：同时添加命令行中指定的包构建测试所需的模块。
* -u：同时更新命令行中指定的包的依赖模块，令依赖模块使用较新的次版本的发布版本或补丁版本的发布版本，前提是这些版本可用。
* -u=patch：（不是-u patch）同样更新依赖模块，但不同之处在于默认选择补丁版本的发布版本。
* -x：同时打印实际执行的命令。当模块直接从仓库下载时，这对调试版本控制命令有用。

当-t和-u一起使用时，将也会更新构建测试所需的模块的依赖模块。

关于模块的更多信息，参阅[https://golang.org/ref/mod](https://golang.org/ref/mod)。

关于使用“go get”来升级最低Go版本和建议的Go工具链的更多信息，参阅[https://go.dev/doc/toolchain](https://go.dev/doc/toolchain)。

关于指定包的更多信息，参阅“go help packages”。

上文介绍了get使用模块来管理源代码和依赖的行为。如果相反，go命令运行在GOPATH模式下，get的标志和行为的细节也会改变，如同“go help get”的内容也会不同。参阅“go help gopath-get”。

参阅：go build、go install、go clean、go mod。

# go help——查看帮助信息

```shell
go
go help
go help <command>
go help <topic>
```

命令（command）为：

* bug：启动bug报告。
* build：编译包及其依赖包。
* clean：删除对象文件和缓存文件。
* doc：查看包或符号的文档。
* env：打印环境变量。
* fix：使用新的API更新包代码。
* fmt：gofmt（重新格式化）包代码。
* generate：通过执行源代码生成Go文件。
* get：添加依赖包至当前模块并安装之。
* help：查看帮助信息。
* install：编译并安装模块及其依赖。
* list：列出包和模块。
* mod：模块维护。
* run：编译并运行Go程序。
* test：测试包。
* tool：运行指定的go tool。
* version：打印Go版本。
* vet：报告包中有可能的错误。
* work：工作区维护。

关于命令的更多信息，参阅“go help \<command>”。

额外的帮助主题（topic）为：

* buildconstraint：构建约束。
* buildmode：构建模式。
* c：Go和C之间的调用。
* cache：构建和测试缓存。
* environment：环境变量。
* filetype：文件类型。
* go.mod：go.mod文件。
* gopath：GOPATH环境变量。
* gopath-get：遗留的GOPATH的go get。
* goproxy：模块代理协议。
* importpath：import路径语法。
* module-auth：使用go.sum的模块校验。
* module-get：模块感知的go get。
* modules：模块，模块版本，及更多。
* packages：包列表和模式。
* private：下载非公共代码的配置。
* testflag：测试标志。
* testfunc：测试函数。
* vcs：用GOVCS控制版本控制。

关于主题的更多信息，参阅“go help \<topic>”。

# go install——编译并安装模块及其依赖

```shell
go install [build flags] [packages]
```

编译并安装导入路径指定的包。

可执行文件安装在GOBIN环境变量指定的目录中，默认为$GOPATH/bin或如果GOPATH环境变量没有设置时为$HOME/go/bin。$GOROOT中的可执行文件安装在$GOROOT/bin或$GOTOOLDIR，而不是$GOBIN。

如果参数有版本后缀（如@latest或@v1.0.0），“go install”在模块感知模式下构建包，忽略当前目录或任何父目录中的go.mod文件，如果有的话。这对安装可执行文件而不影响主模块的依赖很有用。为了消除关于构建中要使用哪个模块版本的歧义，参数必需满足以下约束：

* 参数必需为包路径或包模式（带有“...”通配符）。不能是标准包（如fmt），元模式（std、cmd、all），相对或绝对文件路径。
* 所有的参数带有相同的版本后缀。不同的查询是不允许的，即使它们指向相同的版本。
* 所有的参数必需指向相同模块相同版本中的包。
* 包路径参数必需指向main包。模式参数只会匹配main包。
* 不能有模块被认作“main”模块。如果包含命令行中指定名字的包的模块有go.mod文件，其不能包含可能导致其被不一样地解析为主模块的指令（replace和exclude）。模块不能依赖于自身的一个更高的版本。
* vendor目录不会在任何模块中被使用。（vendor目录不会被包含在被“go install”下载的模块zip文件中。）

如果参数没有版本后缀，“go install”可能运行在模块感知模式或GOPATH模式，取决于GO111MODULE环境变量和go.mod文件的存在。详情参阅“go help modules”。如果模块感知模式启用，“go install”运行在主模块的上下文中。

当模块感知模式禁用，非main包被安装在目录$GOPATH/pkg/$GOOS_$GOARCH中。当模块感知模式启用，非main包被构建和缓存但不会被安装。

在Go 1.20之前，标准库被安装至$GOROOT/pkg/$GOOS_$GOARCH。从Go 1.20起，标准库被构建和缓存但不会被安装。设置GODEBUG=installgoroot=all恢复$GOROOT/pkg/$GOOS_$GOARCH的使用。

关于构建标志的更多信息，参阅“go help build”。

关于指定包的更多信息，参阅“go help packages”。

参阅：go build、go get、go clean。

# go list——列出包和模块

```shell
go list [-f format] [-json] [-m] [list flags] [build flags] [packages]
```

列出指定名字的包，一行一个。最常用的标志是-f和-json，用以控制每个包的输出打印格式。其他标志，文档于下文，控制更多特定的细节。

默认输出显示包的导入路径：

```
bytes
encoding/json
github.com/gorilla/mux
golang.org/x/net/html
```

标志（-f或-m外的其他标志的说明需参阅-f或-m）：

* -complied：将CompiledGoFiles设置为提交给编译器的Go源文件。通常，这意味着其会重复GoFiles列出的文件，然后也会添加通过处理CgoFiles和SwigFiles生成的Go代码。Imports列表包含来自GoFiles和CompiledGoFiles所有导入的并集。
* -deps：不只是遍历指定名字的包，也会遍历它们的依赖。list以深度优先后序遍历的方式遍历它们，因此一个包会在它的所有依赖之后列出。未在命令行中显式列出的包将有一个设置为true的DepOnly字段。
* -e：改变对错误包的处理方式，那些错误包或者不存在或者内容残缺。默认情况下，list命令为每个错误包打印一条错误信息至标准错误输出中，然后在普通的打印中忽略这些错误包。使用-e标志，list命令将不会打印错误信息至标准错误输出中，而是以普通打印的方式处理错误包。错误包将有非空的ImportPath和非nil的Error字段；其他信息可能会也可能不会缺失（置为零值）。
* -export：将Export字段设置为包含指定包最新导出信息的文件名，并且BuildID字段为被编译的包的构建ID。
* -f：指定list使用的替换格式，使用template包的语法。默认的输出等同于-f '{{.ImportPath}}'。传递给模板的结构体为：

	```go
	type Package struct {
		Dir             string   // 包含包源代码的目录
		ImportPath      string   // 目录中包的导入路径
		ImportComment   string   // package语句中导入注释的路径
		Name            string   // 包名
		Doc             string   // 包文档字符串
		Target          string   // 安装路径
		Shlib           string   // 包含本包的共享库（只在设置-linkshared时使用）
		Goroot          bool     // 本包是否在GOROOT中
		Standard        bool     // 本包是否是Go标准库之一
		Stale           bool     // “go install”会否为本包进行一些处理
		StaleReason     string   // 当Stale==true时的解析原因
		Root            string   // 包含本包的GOROOT或GOPATH目录
		ConflictDir     string   // 本目录在$GOPATH中的同名目录
		BinaryOnly      bool     // 是否仅为二进制包（不再支持）
		ForTest         string   // 包只在该指定名字包的测试下使用
		Export          string   // 包含导出数据的文件（当使用-export时）
		BuildID         string   // 编译的包的构建ID（当使用-export时）
		Module          *Module  // 包包含的模块的信息，如果有的话（可以为nil）
		Match           []string // 匹配本包的命令行模式
		DepOnly         bool     // 如为true，则包只是一个依赖，未在命令行中显式列出
		DefaultGODEBUG string    // 默认GODEBUG设置，用于主模块
		
		// 源文件
		GoFiles           []string // .go源文件（不包括CgoFiles、TestGoFiles、XTestGoFiles中的文件）
		CgoFiles          []string // import "C"的.go源文件
		CompiledGoFiles   []string // 提交给编译器的.go文件（当使用-compiled时）
		IgnoredGoFiles    []string // 由于构建限制被忽略的.go源文件
		IgnoredOtherFiles []string // 由于构建约束被忽略的非.go源文件
		CFiles            []string // .c源文件
		CXXFiles          []string // .cc/.cxx/.cpp源文件
		MFiles            []string // .m源文件
		HFiles            []string // .h/.hh/.hpp/.hxx源文件
		FFiles            []string // .f/.F/.for/.f90的Fortran源文件
		SFiles            []string // .s源文件
		SwigFiles         []string // .swig文件
		SwigCXXFiles      []string // .swigcxx文件
		SysoFiles         []string // 添加到归档的.syso对象文件
		TestGoFiles       []string // 包中的_test.go文件
		XTestGoFiles      []string // 包外的_test.go文件
		
		// 嵌入文件
		EmbedPatterns      []string // //go:embed模式
		EmbedFiles         []string // 被EmbedPatterns匹配的文件
		TestEmbedPatterns  []string // TestGoFiles中的//go:embed模式
		TestEmbedFiles     []string // 被TestEmbedPatterns匹配的文件
		XTestEmbedPatterns []string // XTestGoFiles中的//go:embed模式
		XTestEmbedFiles    []string // 被XTestEmbedPatterns匹配的文件
		
		// Cgo指令
		CgoCFLAGS    []string // cgo：给C编译器的标志
		CgoCPPFLAGS  []string // cgo：给C预编译器的标志
		CgoCXXFLAGS  []string // cgo：给C++编译器的标志
		CgoFFLAGS    []string // cgo：给Fortran编译器的标志
		CgoLDFLAGS   []string // cgo：给链接器的标志
		CgoPkgConfig []string // cgo：pkg-config名字
		
		// 依赖信息
		Imports      []string          // 本包使用的导入路径
		ImportMap    map[string]string // 原始导入路径到ImportPath的映射（标识入口信息被省略）
		Deps         []string          // 所有（递归的）导入依赖
		TestImports  []string          // TestGoFiles使用的导入依赖
		XTestImports []string          // XTestGoFiles使用的导入依赖
		
		// 错误信息
		Incomplete bool            // 本包或依赖包是否有错误
		Error      *PackageError   // 加载本包的错误
		DepsErrors []*PackageError // 加载依赖包的错误
	}
	```
	vendor目录中的包报告的ImportPath包括vendor目录自身的路径（例如，“d/vendor/p”而不是“p”），以便ImportPath唯一地标识一个给定包的副本。Imports、Deps、TestImports、和XTestImports列表同样包含这种扩展的导入路径。更多关于使用vendor的信息参阅[golang.org/s/go15vendor](https://golang.org/s/go15vendor)。
	
	错误信息，如果有的话，为：
	
	```go
	type PackageError struct {
		ImportStack   []string // 从命令行指定的包名到此的最短路径
		Pos           string   // 错误的位置（如果有的话为，文件名:行号:列号）
		Err           string   // 错误自身
	}
	```
	
	模块信息为一个Module结构体，其定义在下文的-m讨论中。
	
	模板函数“join”调用strings.Join。
	
	模板函数“context”返回构建的上下文，定义如下：
	
	```go
	type Context struct {
		GOARCH        string   // 目标体系结构
		GOOS          string   // 目标操作系统
		GOROOT        string   // GOROOT
		GOPATH        string   // GOPATH
		CgoEnabled    bool     // cgo是否可用
		UseAllFiles   bool     // 是否使用所有文件而不管//go:build行、文件名
		Compiler      string   // 在计算目标路径时假定的编译器
		BuildTags     []string // 要在//go:build行中匹配的构建约束
		ToolTags      []string // 工具链特定的构建约束
		ReleaseTags   []string // 与当前发布版本兼容的发布版本
		InstallSuffix string   // 安装目录名字中使用的后缀
	}
	```
	
	关于这些字段含义的更多信息，参阅go/build包的Context类型的文档。
	
	Dir、Target、Shlib、Root、ConflictDir和Export中的文件路径均为绝对路径。
	
	默认情况下，GoFiles、CgoFiles等列表中的名字为Dir目录中的文件（亦即，相对于Dir的路径，而不是绝对路径）。当使用-compiled和-test标志时添加的生成文件为指向生成的Go源代码的缓存副本的绝对路径。即使它们为Go源文件，路径可能不是以“.go”结尾。
* -find：找出指定名字的包但不解析它们的依赖：Imports和Deps列表将会为空。带有-find标志时，-deps、-test和-export命令不能被使用。
* -json：包数据以JSON格式打印，而不是使用template包的格式。JSON标志能可选地被提供一组逗号分隔的输出时的必填字段名。如果这样，那些必填字段将总是出现在JSON输出中，但其它可能被忽略来节省计算JSON结构的工作。
* -m：列出模块而不是包。

	当列出模块时，-f标志仍然指定一个应用于Go结构体的格式模板，但现在为一个Module结构体：
	
	```go
	type Module struct {
		Path       string       // 模块路径
		Query      string       // 对应此版本的版本查询
		Version    string       // 模块版本
		Versions   []string     // 可获得的模块版本
		Replace    *Module      // 被此模块替代
		Time       *time.Time   // 版本创建的时间
		Update     *Module      // 可获得的更新（当使用-u时）
		Main       bool         // 是否为主模块？
		Indirect   bool         // 模块只是被主模块间接依赖
		Dir        string       // 保存文件本地副本的目录，如果有的话
		GoMod      string       // 描述模块的go.mod文件的路径，如果有的话
		GoVersion  string       // 模块中使用的go版本
		Retracted  []string     // 撤回的信息，如果有的话（当使用-retracted或-u时）
		Deprecated string       // 弃用的信息，如果有的话（当使用-u时）
		Error      *ModuleError // 加载模块时的错误
		Origin     any          // 模块来源
		Reuse      bool         // 重用旧模块信息是安全的
	}
	
	type ModuleError struct {
		Err string // 错误自身
	}
	```
	
	如果模块在模块缓存中或如果-modfile标志被使用，GoMod指向的文件可能在模块目录之外。
	
	默认的输出是打印模块路径，然后是关于版本和替代的信息，如果有的话。例如，“go list -m all”可能打印：
	
	```
	my/main/module
	golang.org/x/text v0.3.0 => /tmp/text
	rsc.io/pdf v0.1.1
	```
	
	Module结构体有一个String方法，用以格式化输出的这一行，因此默认格式等同于-f '{{.String}}'。
	
	注意，当模块被替代，其Replace字段描述替代的模块，其Dir字段设置为替代模块的源代码的目录，如果有的话。（也就是说，如果Replace不为nil，那么Dir设置为Replace.Dir，无法访问被替代的源代码。）
	
	list -m的参数会被解析为模块的列表，而不是包的列表。主模块是包含当前目录的模块。活跃的（active）模块为主模块及其依赖。不带参数，list -m显示主模块。带参数，list -m显示参数指定的模块。任何活跃的模块都可以通过其模块路径指定。特殊的模式“all”指定所有活跃的模块，首先是主模块，然后是使用模块路径排序的依赖。包含“...”的模式指定其模块路径匹配该模式的活跃模块。path@version形式的查询指定该查询的结果，不限于活跃的模块。关于模块查询的更多信息，参阅“go help modules”。
	
	模板函数“module”使用单个字符串参数，必须为模块路径或查询，并以Module结构体的形式返回指定的模块。如果发生错误，结果将是带有非nil的Error字段的Module结构体。
* -retracted：报告关于被撤回的模块版本的信息。当-retracted与-f或-json一起使用，Retracted字段将被设置为一个字符串用以解释为什么版本被撤回。该字符串从模块的go.mod文件中的retract指令的注释中提取。当-retracted和-versions一起使用时，被撤回的版本和未被撤回的版本一起列出。-retracted标志可与或者不与-m一起使用。
* -reuse：当使用-m时，-reuse=old.json标志接受包含前一次以相同的修饰标志（例如-r、-retracted和-versions）集合进行的“go list -m -json”调用的JSON输出的文件名。go命令可使用此文件来决定自前一次调用以来模块没有变化，并避免重新下载关于它的信息。未被重新下载的模块将通过设置Reuse字段为true来在新输出中标记。通常模块缓存自动地提供这种重用；-reuse标志在不保存模块缓存的系统上是有用的。
* -test：不只是报告指定名字的包，也报告它们的测试二进制文件（对带有测试的包），从而将测试二进制文件是怎样构建的准确地传递给源代码分析工具。测试二进制文件的报告的导入路径为包的导入路径添加“.test”后缀，如“math/rand.test”。当构建测试时，有时候需要重新构建测试特定的某些依赖（最常见的是被测试的包本身）。为特定的测试二进制文件重新编译的包的报告的导入路径，会添加一个空格和以括号括起的测试二进制文件的名字，如“math/rand [math/rand.test]”或“regexp [sort.test]”。ForTest也会设置为正在被测试的包名（在此前的例子中为“math/rand”或“sort”）。
* -u：添加关于可用升级的信息。当给定模块的最后版本比当前模块较新时，-u设置Module的Update字段为关于较新模块的信息。如果当前版本被撤回，-u也会设置Module的Retracted字段。通过在当前版本后面格式化以括号括起的较新版本，Module的String方法表示一个可获得的升级。如果版本被撤回，其后跟字符串“(retracted)”。例如，“go list -m -u all”可能打印：
	
	```
	my/main/module
	golang.org/x/text v0.3.0 [v0.4.0] => /tmp/text
	rsc.io/pdf v0.1.1 (retracted) [v0.1.2]
	```
	
	（对工具来说，“go list -m -u -json all”可能更便于解析。）
* -versions：设置Module的Versions字段为该模块的所有已知版本的列表，根据语义版本进行排序，从最早至最后。该标志也会改变默认的输出格式来显示模块路径，其后接着以空格分隔的版本列表。

关于构建标志的更多信息，参阅“go help build”。

关于指定包的更多信息，参阅“go help packages”。

关于模块的更多信息，参阅[https://golang.org/ref/mod](https://golang.org/ref/mod)。

# go mod——模块维护

```shell
go mod <command> [arguments]
```

command可为以下命令：

* download：下载模块至本地缓存中。
* edit：通过工具或脚本编辑go.mod。
* graph：打印模块依赖图。
* init：在当前目录初始化新模块。
* tidy：添加缺少的模块并删除未使用的模块。
* vendor：生成依赖的vendor副本。
* verify：验证依赖有预期的内容。
* why：解释为什么需要该包或模块。

go mod提供对模块操作的访问。

注意对模块的支持内置于所有go命令，不只是“go mod”。例如，依赖的日常添加、删除、升级、降级应该使用“go get”来完成。关于模块功能的概述，参阅“go help modules”。

关于command的更多信息，参阅“go help mod <command>”。

## go mod download——下载模块至本地缓存中

```shell
go mod download [-x] [-json] [-reuse=old.json] [modules]
```

download下载指定名字的模块，可为选择主模块依赖的模块匹配模式，或path@version形式的模块查询。

不带参数，download应用于在主模块中构建和测试包所需的模块：这些模块被主模块明确地依赖，如果其为“go 1.17”或更高；或所有传递性依赖的模块，如果其为“go 1.16”或更低。

go命令将在常规执行期间根据需要自动下载模块。“go mod download”命令主要用于预填充本地缓存或计算Go模块代理的结果。

默认情况下，download不向标准输出写入内容。其可能打印进度信息和错误至标准错误输出。

标志：

* -json：打印一系列JSON对象至标准输出，描述每个下载的模块（或失败），相当于该Go结构体：
	
	```go
	type Module struct {
		Path     string // 模块路径
		Query    string // 对应此版本的版本查询
		Version  string // 模块版本
		Error    string // 加载模块时的错误
		Info     string // 缓存的.info文件绝对路径
		GoMod    string // 缓存的.mod文件绝对路径
		Zip      string // 缓存的.zip文件绝对路径
		Dir      string // 缓存的源文件根目录绝对路径
		Sum      string // 路径、版本的校验和（如go.sum中所示）
		GoModSum string // go.mod的校验和（如go.sum中所示）
		Origin   any    // 模块来源
		Reuse    bool   // 重用旧模块信息是安全的
	}
	```
* -reuse：接受包含前一次“go mod download -json”调用的JSON输出的文件名。go命令可使用此文件来决定自前一次调用以来模块没有变化，并避免重新下载它。未被重新下载的模块将通过设置Reuse字段为true来在新输出中标记。通常模块缓存自动地提供这种重用；-reuse标志在不保存模块缓存的系统上是有用的。
* -x：同时打印实际执行的命令。

关于“go mod download”的更多信息，参阅[https://golang.org/ref/mod#go-mod-download](https://golang.org/ref/mod#go-mod-download)。
	
关于版本查询的更多信息，参阅[https://golang.org/ref/mod#version-queries](https://golang.org/ref/mod#version-queries)。

## go mod edit——通过工具或脚本编辑go.mod

```shell
go mod edit [editing flags] [-fmt|-print|-json] [go.mod]
```

edit提供一个编辑go.mod的命令行接口，主要给工具或脚本使用。它只读取go.mod；不查找涉及模块的信息。默认情况下，edit读写主模块的go.mod文件，但也可以在标志后指定不同的目标文件。

标志：

* -dropexclude=path@version：删除给定模块路径和版本的排除。
* -dropreplace=old[@v]：删除给定模块路径和版本对的替代。如果@v省略，删除该模块不带版本的替代。
* -droprequire=path：删除给定的模块路径依赖的模块。该标志主要提供给工具用以理解模块图。用户应该使用“go get path@none”，可令其它go.mod根据需要调整来满足其它模块施加的限制。
* -dropretract=version：删除对给定版本的撤回。version可能是类似“v1.2.3”的单个版本或类似“[v1.1.0,v1.1.9]”的闭区间。
* -exclude=path@version：添加给定模块路径和版本的排除。注意如果排除已经存在-exclude=path@version是无操作的。
* -fmt：重新格式化go.mod文件，不作其他改变。使用或重写go.mod文件的任何其他修改也意味着这种重新格式化。需要该标志的唯一情形是没有指定其它标志，如“go mod edit -fmt”。
* -go=version：设置期望的Go语言版本。
* -json：以JSON格式打印最终的go.mod，而不是将其写回go.mod。JSON输出对应于这些Go类型：

	```go
	type Module struct {
		Path    string
		Version string
	}
	
	type GoMod struct {
		Module    ModPath
		Go        string
		Toolchain string
		Require   []Require
		Exclude   []Module
		Replace   []Replace
		Retract   []Retract
	}
	
	type ModPath struct {
		Path       string
		Deprecated string
	}
	
	type Require struct {
		Path string
		Version string
		Indirect bool
	}
	
	type Replace struct {
		Old Module
		New Module
	}
	
	type Retract struct {
		Low       string
		High      string
		Rationale string
	}
	```
	表示单个版本（不是一个区间）的Retract条目将有设置为相同值的“Low”和“High”字段。
* -module：修改模块路径（go.mod文件的模块行）。
* -print：以其文本格式打印最终的go.mod，而不是将其写回go.mod。
* -replace=old[@v]=new[@v]：添加给定模块路径和版本对的替代。如果old@v中的@v省略，则左侧不带版本的替代将被添加，应用于old模块路径的所有版本。如果new@v中的@v省略，新路径应为本地模块根目录，而不是模块路径。注意-replace覆盖old[@v]任何冗余的替代，因此省略@v将删除对特定版本的现有替代。
* -require=path@version：添加给定的模块路径和版本依赖的模块。注意-require覆盖该路径任何已存在的依赖的模块。该标志主要提供给工具用以理解模块图。用户应该使用“go get path@version”，其可令其它go.mod根据需要调整来满足其它模块施加的限制。
* -retract=version：添加对给定版本的撤回。version可能是类似“v1.2.3”的单个版本或类似“[v1.1.0,v1.1.9]”的闭区间。注意如果撤回已经存在-retract=version是无操作的。
* -toolchain=name：设置要使用的Go工具链。

-require、-droprequire、-exclude、-dropexclude、-replace、-dropreplace、-retract、-dropretract编辑标志可以重复，根据给定的顺序应用修改。

也提供-C、-n、-x构建标志。

注意这只描述go.mod文件自身，不描述其他间接引用的模块。对于构建可使用的的模块的完整集合，使用“go list -m -json all”。

关于“go mod edit”的更多信息参阅[https://golang.org/ref/mod#go-mod-edit](https://golang.org/ref/mod#go-mod-edit)。

## go mod graph——打印模块依赖图

```shell
go mod graph [-go=version] [-x]
```

以文本形式打印模块依赖图（已应用替代）。输出的每行有两个空格分隔的字段：模块和其依赖中的一个。每个模块都被标记为path@version形式的字符串，除了主模块，因其没有@version后缀。

标志：

* -go：令其报告如同被指定的Go版本加载的模块图，而不是被在go.mod文件中的“go”指令标示的版本。
* -x：令其打印执行的命令。

关于“go mod graph”的更多信息参阅[https://golang.org/ref/mod#go-mod-graph](https://golang.org/ref/mod#go-mod-graph)。

## go mod init——在当前目录初始化新模块

```shell
go mod init [module-path]
```

初始化并写入一个新的go.mod文件至当前目录中，实际上是创建一个以当前目录为根的新模块。文件go.mod必须不存在。

接受一个可选参数，即新模块的模块路径。如果模块路径参数省略，将使用.go文件中的import注释、vendoring工具配置文件（类似Gopkg.lock）和当前目录（如果在GOPATH中）尝试推断模块路径。

如果vendoring工具配置文件存在，将尝试从其导入模块依赖。

关于“go mod init”的更多信息参阅[https://golang.org/ref/mod#go-mod-init](https://golang.org/ref/mod#go-mod-init)。

## go mod tidy——添加缺少的模块并删除未使用的模块

```shell
go mod tidy [-e] [-v] [-x] [-go=version] [-compat=version]
```

确保go.mod与模块中的源代码一致。它添加构建当前模块的包和依赖所必须的任何缺少的模块，删除不提供任何有价值的包的未使用的模块。它也会添加任何缺少的条目至go.mod并删除任何不需要的条目。

标志：

* -compat：保留被指明的主Go发布版本的“go”命令所需的任何额外的校验和，用以成功加载模块图，并且如果“go”命令的该版本会从不同的模块版本加载任何导入的包，则令tidy出错。默认情况下，tidy如同-compat标志被设置为被go.mod文件中的“go”指令指示的版本之前的版本一样运作。
* -e：即使有加载包时遇到的错误仍尝试继续。
* -go：更新go.mod文件中的“go”指令为给定的版本，其可能改变go.mod文件中哪些模块依赖被保留为显式的要求。（Go版本1.17及更高版本保留更多的要求以为了支持延迟模块加载。）
* -v：打印被删除的模块的信息至标准错误输出。
* -x：令其打印执行的命令。

关于“go mod tidy”的更多信息参阅[https://golang.org/ref/mod#go-mod-tidy](https://golang.org/ref/mod#go-mod-tidy)。

## go mod vendor——生成依赖的vendor副本

```shell
go mod vendor [-e] [-v] [-o outdir]
```

重置主模块的vendor目录，使其包含构建和测试所有主模块的包所需要的所有包。不包括vendor中的包的测试代码。

标志：

* -e：即使有加载包时遇到的错误仍尝试继续。
* -o：在给定的路径下而不是“vendor”创建vendor目录。go命令只能使用在模块根目录下名为“vendor”的一个vendor目录，所以此标志主要对其它工具有用。
* -v：打印vendor的模块和包的名字至标准错误输出。

关于“go mod vendor”的更多信息参阅[https://golang.org/ref/mod#go-mod-vendor](https://golang.org/ref/mod#go-mod-vendor)。

## go mod verify——验证依赖有预期的内容

```shell
go mod verify
```

检查存储在本地下载源代码缓存中的当前模块的依赖，是否自从下载之后未被修改。如果所有模块都未被修改，打印“all modules verified”。否则报告哪个模块已经被修改并令“go mod”以非0状态退出。

关于“go mod verify”的更多信息参阅[https://golang.org/ref/mod#go-mod-verify](https://golang.org/ref/mod#go-mod-verify)。

## go mod why——解释为什么需要该包或模块

```shell
go mod why [-m] [-vendor] packages...
```

标志：

* -m：默认情况下，why在导入图中展示从主模块到每个列出的包的最短路径。如果给出-m标志，why将参数视为一个模块的列表并找出每个模块中的所有包的路径。
* -vendor：默认情况下，why查询与“go list all”匹配的包图，包括对可达包的测试相关的包。-vendor标志令why将依赖包的测试相关的包排除在外。

输出是一系列的小节，和命令行中每个包或模块的名字一一对应，以空行分隔。每个小节以注释行“# package”或“# module”开头，给出目标包或模块。随后的行通过导入图给出路径，一个包一行。如果包或模块没有被主模块引用，该小节将显示单独一个带圆括号的提示信息来表明该事实。

例如：

```
$ go mod why golang.org/x/text/language golang.org/x/text/encoding
# golang.org/x/text/language
rsc.io/quote
rsc.io/sampler
golang.org/x/text/language

# golang.org/x/text/encoding
(main module does not need package golang.org/x/text/encoding)
$
```

关于“go mod why”的更多信息参阅[https://golang.org/ref/mod#go-mod-why](https://golang.org/ref/mod#go-mod-why)。

# go run——编译并运行Go程序

```shell
go run [build flags] [-exec xprog] package [arguments...]
```

编译并运行指定名字的main Go包。通常包是作为一个单独的目录中的.go源文件列表指定的，但也可以是导入路径、文件系统路径、或匹配单独的已知包的模式，例如“go run .”或“go run my/cmd”。

如果package参数有版本后缀（如@latest或@v1.0.0），“go run”以模块感知模式构建程序，忽略在当前目录或任何父目录的go.mod文件，如果有的话。这在运行程序而不影响主模块的依赖时有用。

如果package参数没有版本后缀，“go run”可能以模块感知模式或GOPATH模式运行，取决于GO111MODULE环境变量和go.mod文件的存在。详细信息参阅“go help modules”。如果模块感知模式启用，“go run”运行在主模块的上下文中。

标志：

* -exec：默认情况下，“go run”直接运行编译的二进制文件：“a.out arguments...”。如果给定-exec标志，“go run”使用xprog调用二进制文件：“xprog a.out arguments...”。

	如果-exec标志未给定，GOOS或GOARCH与系统默认值不同，且一个名字为go_$GOOS_$GOARCH_exec的程序可在当前查找目录下找到，“go run”使用该程序调用二进制文件，如“go_js_wasm_exec a.out arguments...”。当模拟器或其他执行方法可用时，这允许交叉编译的程序的执行。

默认情况下，“go run”编译二进制但不生成被调试器使用的信息，来减少构建时间。要在二进制中包含调试器信息，使用“go build”。
	
run的退出状态不是编译的二进制文件的退出状态。

关于构建标志的更多信息，参阅“go help build”。关于指定包的更多信息，参阅“go help packages”。

参阅：go build。

# go test——测试包

```shell
go test [build/test flags] [packages] [build/test flags & test binary flags]
```

“go test”自动测试以导入路径命名的包。它以如下格式打印测试结果概要：

```
ok   archive/tar   0.011s
FAIL archive/zip   0.022s
ok   compress/gzip 0.033s
...
```

每个失败的包后跟随详细的输出。

“go test”将与名字匹配文件模式“*_test.go”的任何文件一起，重新编译每个包。这些附加的文件可以包含test函数、benchmark函数、fuzz测试、example函数。更多信息参阅“go help testfunc”。每个列出的包都会令到执行单独的测试二进制文件。以“_”（包括“_test.go”）或“.”开头的文件会被忽略。

带后缀“_test”声明的包的测试文件将被编译为单独的包，然后与main包的测试二进制文件链接并运行。

go tool将忽略名字为“testdata”的目录，令其可以持有被测试所需的辅助数据。

作为构建测试二进制文件的一部分，go test在包及其测试源文件上运行go vet来发现显而易见的问题。如果go vet发现任何问题，go test报告它们且不运行测试二进制文件。只使用默认的go vet检查的高可信的子集。该子集是：atomic、bool、buildtags、directive、errorsas、ifaceassert、nilfunc、printf和stringintconv。你可以通过“go doc cmd/vet”查看这些及其它vet测试的文档。要禁止运行go vet，使用-vet=off标志。要运行所有检查，使用-vet=all标志。

所有的测试输出和概要行都打印至go命令的标准输出，即使test打印它们至它自己的标准错误输出。（go命令的标准错误输出被保留为打印构建测试的错误。）

go命令放置$GOROOT/bin于测试的环境中$PATH的开头，以便执行“go”命令的测试与父“go test”命令使用相同的“go”。

go test以两种不同的模式运行：

第一种，称为本地目录模式，发生在当go test不使用包参数调用时（例如，“go test”或“go test -v”）。在这种模式下，go test编译在当前目录下找到的包源文件和测试文件，然后运行生成的测试二进制文件。在这种模式下，缓存（在下面讨论）被禁用。包测试完成后，go test打印一行概要行展示测试状态（“ok”或“FAIL”）、包名和消耗的时间。

第二种，称为包列表模式，发生在当go test使用显式的包参数调用时（例如“go test math”、“go test ./...”甚至“go test .”）。在这种模式下，go test编译并测试每个在命令行列出的包。如果一个包测试通过，go test只打印最终的“ok”概要行。如果一个包测试失败，go test打印完整的测试输出。如果使用-bench或-v标志调用（参阅 **testflag主题——测试标志** 小节），即使通过了包测试，go test也打印完整的输出，以便展示要求的基准测试结果或详细日志。对所有列出的包的包测试完成后，且它们的输出被打印，如果任一个包测试失败，go test打印最终的“FAIL”状态。

只在包列表模式，go test缓存成功的包测试结果来避免不必要的测试重复运行。当测试的结果可以从缓存恢复时，go test将重新展示之前的输出而不是再次运行测试二进制文件。当这种情况发生时，go test在概要行中打印“(cached)”取代消耗的时间。

缓存匹配的规则为，运行只涉及相同的测试二进制文件，且命令行中的标志完全来自一个受限的“可缓存”测试标志集合，定义为-benchtime、-cpu、-list、-parallel、-run、-short、-timeout、-failfast和-v。如果go test的运行有任何不在这个集合中的测试或非测试标志，结果是不被缓存的。要禁用测试缓存，使用可缓存标志以外的测试标志或参数。显式禁用测试缓存的惯用方式为使用-count=1。在包源代码根目录（通常为$GOPATH）内打开文件或查询环境变量的测试只匹配将来的文件和环境变量未改变的测试运行。缓存的测试结果被视为立即执行的，因此一个成功的包测试结果将被缓存且重用时忽略-timeout设置。

除了构建标志，“go test”自身处理的标志还有：

* -args：传递命令行中余下的参数（所有在-args后面的参数）至测试二进制文件，不解析且不修改。因为该标志使用命令行中余下的参数，包列表（如果有）必须出现在该标志之前。
* -c：编译测试二进制文件至当前目录中的pkg.test但不运行之（pkg是包的导入路径的最后的元素）。文件名或目标目录可以被-o标志修改。
* -exec xprog：使用xprog运行测试二进制文件。其行为与“go run”相同。详细信息参阅“go help run”。
* -json：转换测试输出为适合自动化执行的JSON格式。关于编码的详细信息参阅“go doc test2json”。
* -o file：编译测试二进制文件至指定名字的文件。测试仍会运行（除非指定-c或-i）。如果file以斜杠结尾或命名为已存在的目录，测试被写入至该目录的pkg.test。

测试二进制文件也接受控制测试执行的标志；这些参数也可被“go test”使用。详细信息参阅“go help testflag”。

关于构建标志的更多信息，参阅“go help build”。关于指定包的更多信息，参阅“go help packages”。

参阅：go build、go vet。

# go tool——运行指定的go tool

```shell
go tool [-n] command [args...]
```

运行参数指定的go tool命令。不带参数则打印已知的工具列表。

标志：

* -n：将实际需执行的命令打印出来但不运行。

关于每个tool命令的更多信息，参阅“go doc cmd/<command>”。

# go version——打印Go版本

```shell
go version [-m] [-v] [file ...]
```

为Go二进制文件打印的构建信息。

go version报告用来构建每个指定名字的文件的Go版本。

如果命令行中没有指定文件，go version打印其自身的版本信息。

标志：

* -m：当可以获得的时候，令go version打印每个文件的内嵌的模块版本信息。在输出中，模块信息包含跟在版本行之后的多行，每行由前导制表符缩进。
* -v：如果指定一个目录，go version递归地遍历该目录，查找可以识别的Go二进制文件并报告它们的版本。默认情况下，go version不报告在目录扫描期间发现的无法识别的文件。-v标志令其报告无法识别的文件。

参阅：go doc runtime/debug.BuildInfo。

# go vet——报告包中有可能的错误

```shell
go vet [build flags] [-vettool prog] [vet flags] [packages]
```

在通过导入路径指定名字的包上运行go vet命令。

关于vet和其标志的更多信息，参阅“go doc cmd/vet”。关于指定包的更多信息，参阅“go help packages”。关于检查程序及其标志的列表，参阅“go tool vet help”。关于诸如“printf”的特定检查程序的详细信息，参阅“go tool vet help printf”。

标志：

* -vettool=prog：选择带有备选或附加检查的不同的分析工具。例如，可以使用这些命令来构建和运行“shadow”分析器：

	```
	go install golang.org/x/tools/go/analysis/passes/shadow/cmd/shadow@latest
	go vet -vettool=$(which shadow)
	```

被go vet支持的构建标志是那些控制包解析和执行的，如-C、-n、-x、-v、-tags和-toolexec。关于这些标志的更多信息，参阅“go help build”。

参阅：go fmg、go fix。

# go work——工作区维护

```shell
go work <command> [arguments]
```

command可为以下命令：

* edit：通过工具或脚本编辑go.work。
* init：初始化工作区文件。
* sync：同步工作区构建列表至模块。
* use：添加模块至工作区文件。

提供对工作区上操作的访问。

注意对工作区的支持内置于许多其它命令，不只是“go work”。

关于Go的模块系统——工作区是其一部分——的信息参阅“go help modules”。

关于工作区深入的参考参阅[https://go.dev/ref/mod#workspaces](https://go.dev/ref/mod#workspaces)。

关于工作区入门的教程参阅[https://go.dev/doc/tutorial/workspaces](https://go.dev/doc/tutorial/workspaces)。

工作区被一个以“use”指令指定模块目录集合的go.work文件指定。这些模块被go命令为构建和相关操作用作根模块。一个没有指定要使用的模块的工作区不能被用来从本地模块执行构建。

go.work文件是面向行的。每行持有单条指令，由一个后跟参数的关键字组成。例如：

```go
go 1.18

use ../foo/bar
use ./baz

replace example.com/foo v1.2.3 => example.com/bar v1.4.5
```

开头的关键字可以从相邻的行分界出去来创建一个块，就像在Go import中一样。

```go
use (
  ../foo/bar
  ./baz
)
```

use指令指定在主模块的工作区集合中需要包含的模块。use指令的参数是包含模块的go.mod文件的目录。

go指令指定文件撰写于的Go版本。可能对工作区的语义将来会有变化，其可以被这个版本控制，但现在此指定的版本没有影响。

replace指令与go.mod文件中的replace指令有相同的语法，且优先于go.mod文件中的replace。它主要用来覆盖在不同工作区模块中冲突的replace。

要确定go命令是否运行在工作区模式下，使用“go env GOWORK”命令。这会指明正在被使用的工作区文件。

关于command的更多信息，参阅“go help work <command>”。

## go work edit——通过工具或脚本编辑go.work

```shell
go work edit [editing flags] [go.work]
```

edit提供一个编辑go.work的命令行接口，主要给工具或脚本使用。它只读取go.work；不查找涉及模块的信息。如果没有指定文件，edit在当前目录及其父目录查找go.work文件。

标志指定一系列编辑操作：

* -dropreplace=old[@v]：删除给定模块路径和版本对的替代。如果@v省略，删除该模块不带版本的替代。
* -dropuse=path：从go.work文件的模块目录集合中删除use指令。
* -fmt：重新格式化go.work文件，不作其他改变。使用或重写go.work文件的任何其他修改也意味着这种重新格式化。需要该标志的唯一情形是没有指定其它标志，如“go work edit -fmt”。
* -go=version：设置期望的Go语言版本。
* -json：以JSON格式打印最终的go.work，而不是将其写回go.work。JSON输出对应于这些Go类型：

	```go
	type GoWork struct {
		Go        string
		Toolchain string
		Use       []Use
		Replace   []Replace
	}
	
	type Use struct {
		DiskPath   string
		ModulePath string
	}
	
	type Replace struct {
		Old Module
		New Module
	}
	
	type Module struct {
		Path    string
		Version string
	}
	```
* -print：以其文本格式打印最终的go.work，而不是将其写回go.work。
* -replace=old[@v]=new[@v]：添加给定模块路径和版本对的替代。如果old@v中的@v省略，则左侧不带版本的替代将被添加，应用于old模块路径的所有版本。如果new@v中的@v省略，新路径应为本地模块根目录，而不是模块路径。注意-replace覆盖old[@v]任何冗余的替代，因此省略@v将删除对特定版本的现有替代。
* -use=path：在go.work文件的模块目录集合中添加use指令。
* -toolchain=name：设置要使用的Go工具链。

-use、-dropuse、-replace、-dropreplace编辑标志可以重复，根据给定的顺序应用修改。

更多信息参阅于[https://go.dev/ref/mod#workspaces](https://go.dev/ref/mod#workspaces)的工作区参考。

## go work init——初始化工作区文件

```shell
go work init [moddirs]
```

在当前目录初始化并写入一个新的go.work文件，实际上是在当前目录创建一个新的工作区。

可选地接受工作区模块的路径作为参数，如果参数被省略，一个没有模块的空工作区将被创建。

每个参数路径被添加至go.work文件中的一个use指令。当前go版本也将在go.work文件中列出。

更多信息参阅于[https://go.dev/ref/mod#workspaces](https://go.dev/ref/mod#workspaces)的工作区参考。

## go work sync——同步工作区构建列表至模块

```shell
go work sync
```

同步工作区的构建列表回到工作区的模块。

工作区的构建列表是在工作区中用来执行构建的所有（传递性的）依赖模块的版本集合。go work sync使用最小版本选择（Minimal Version Selection）算法生产该构建列表，并然后同步那些版本回到每个在工作区中（用use指令）指定的模块。

如果依赖模块的版本不是已经和构建列表的版本相同，同步是通过按顺序地升级在工作区模块中指定的每个依赖模块至在构建列表中的版本。注意最小版本选择算法保证每个模块的构建列表的版本总是等于或高于在每个工作区模块中的值。

更多信息参阅于[https://go.dev/ref/mod#workspaces](https://go.dev/ref/mod#workspaces)的工作区参考。

## go work use——添加模块至工作区文件

```shell
go work use [-r] [moddirs]
```

提供一个命令行接口来添加目录至go.work文件中——可选地递归。

对在命令行go.work文件中列出的每个参数目录，一个use指令将被添加至go.work文件中——如果它存在，或从go.work文件中删除——如果它不存在。如果任何剩下的use指令指向不存在的模块则失败。

会更新go.work中的go行来指定至少和被使用的模块——先前存在的和新近添加的——的所有go行一样新的版本号。不带有参数，此更新是go work use所做的唯一事情。

标志：

* -r：递归地搜索在参数目录中的模块，且use命令如同每个目录都被指定为参数一样工作：亦即，对存在的目录use指令将被添加，对不存在的目录将被删除。

更多信息参阅于[https://go.dev/ref/mod#workspaces](https://go.dev/ref/mod#workspaces)的工作区参考。

# buildconstraint主题——构建约束

构建约束（constraint），也被认为是构建标记（tag），是文件应该被包含在包当中的条件。构建约束由以此开头的行注释给出：

```go
//go:build
```

约束可以出现在任何类型的源文件中（不只是Go），但它们必须出现在文件的顶部附近，在其前面只有空行或其它行注释。这些规则意味着在Go文件中构建约束必须出现在package语句之前。

为了区分构建约束和包文档，构建约束应该后接一个空行。

构建约束注释视作包含被||、&&、!操作符和圆括号组合的构建标记的表达式。操作符和Go中有相同的意义。

例如，以下构建约束约束文件当“linux”和“386”约束被满足，或当“darwin”被满足且“cgo”不被满足时进行构建。

```go
//go:build (linux && 386) || (darwin && !cgo)
```

对一个文件有多于一个//go:build行是错误的。

在特定的构建期间，以下的构建标记是被满足的：

* 目标操作系统，即为runtime.GOOS显示的值，使用GOOS环境变量设置。
* 目标体系结构，即为runtime.GOARCH显示的值，使用GOARCH环境变量设置。
* 任何体系结构特性，格式为GOARCH.feature（例如，“amd64.v2”），如下所详述。
* “unix”，如果GOOS是Unix或类Unix系统。
* 使用的编译器，“gc”或“gccgo”之一。
* “cgo”，如果支持cgo命令（参阅“go help environment”中的CGO_ENABLED）。
* 对每个Go主发布版本的措辞，从当前版本起：“go1.1”从Go版本1.1开始，“go1.12”从Go版本1.12开始，依次类推。
* 被-tags标志指定的任何额外的标记（参阅“go help build”）。

beta或次版本（译注：不是修订版本？）的发布版本没有单独的构建标记。

如果文件名，在去掉扩展名和可能的_test后缀后，匹配任意下述的模式：

* *_GOOS
* *_GOARCH
* *_GOOS_GOARCH

（例如：source_windows_amd64.go）此处的GOOS和GOARCH分别表示任意已知的操作系统和体系结构值，那么该文件被认为有要求这些项的隐式的构建约束（加上文件中任何显式的约束）。

使用GOOS=android匹配关于GOOS=linux的构建标记和文件，并加上android标记和文件。

使用GOOS=illumos匹配关于GOOS=solaris的构建标记和文件，并加上illumos标记和文件。

使用GOOS=ios匹配关于GOOS=darwin的构建标记和文件，并加上ios标记和文件。

已定义的体系结构特性构建标记为：

* 对GOARCH=386，GO386=387和GO386=sse2分别设置386.387和386.sse2构建标记。
* 对GOARCH=amd64，GOAMD64=v1、v2和v3对应amd64.v1、amd64.v2和amd64.v3特性构建标记。
* 对GOARCH=arm，GOARM=5、6和7对应 arm.5、arm.6和arm.7特性构建标记。
* 对GOARCH=mips或mipsle，GOMIPS=hardfloat和softfloat对应mips.hardfloat和mips.softfloat（或mipsle.hardfloat和mipsle.softfloat）特性构建标记。
* 对GOARCH=mips64或mips64le，GOMIPS64=hardfloat和softfloat对应mips64.hardfloat和mips64.softfloat（或mips64le.hardfloat或mips64le.softfloat）特性构建标记。
* 对GOARCH=ppc64或ppc64le，GOPPC64=power8、power9和power10对应ppc64.power8、ppc64.power9和ppc64.power10（或ppc64le.power8、ppc64le.power9和ppc64le.power10）特性构建标记。
* 对GOARCH=wasm，GOWASM=satconv和signext对应wasm.satconv和wasm.signext特性构建标记。

对GOARCH=amd64、arm、ppc64和ppc64le，一个特定的特性等级也为所有之前的等级设置特性构建标记。例如，GOAMD64=v2设置amd64.v1和amd64.v2特性标记。这保证使用v2特性的代码在引入GOAMD64=v4时能继续编译。处理特定特性等级缺失的代码应使用否定：

```go
//go:build !amd64.v2
```

要使文件在任何构建时都不被考虑：

```go
//go:build ignore
```

（任何其它不满足规则的词也会如此工作，但“ignore”是按照惯例的。）

要只在使用cgo时，以及只在Linux和OS X上构建文件：

```go
//go:build cgo && (linux || darwin)
```

这样的文件通常与为其它系统实现默认功能的另一个文件搭配，在这个示例下将带有约束：

```go
//go:build !(cgo && (linux || darwin))
```

命名文件为dns_windows.go将令其只在为Windows构建包时被包含；类似地，math_386.s将只在为32位x86构建包时被包含。

Go版本1.16和更早的版本为构建约束使用不同的语法，带有“// +build”前缀。当遇到旧的语法时，gofmt命令将添加一个等价的//go:build约束。

# buildmode主题——构建模式

“go build”和“go install”命令使用-buildmode参数，用来表示要构建哪一种对象文件。当前支持的值为：

（译注：下述构建模式如无特别说明，均可列出多个包同时构建。）

* -buildmode=archive：构建列出的非main包生成.a文件。main包被忽略。
* -buildmode=c-archive：构建列出的main包，加上其导入的所有包，生成C归档文件。可调用的符号只为那些使用cgo的// export注释导出的函数。要求只能列出一个main包。
* -buildmode=c-shared：构建列出的main包，加上其导入的所有包，生成C共享库。可调用的符号只为那些使用cgo的// export注释导出的函数。要求只能列出一个main包。
* -buildmode=default：构建列出的main包生成可执行文件，构建列出的非main包生成.a文件（默认行为）。
* -buildmode=exe：构建列出的main包及其导入的所有包生成可执行文件。非main包被忽略。
* -buildmode=pie：构建列出的main包及其导入的所有包生成地址无关可执行文件（position independent executables，即PIE）。非main包被忽略。
* -buildmode=plugin：构建列出的main包，加上其导入的所有包，生成Go插件（plugin）。非main包被忽略。
* -buildmode=shared：合并所有列出的非main包生成单独的共享库，共享库可在当使用-linkshared选项构建时使用。main包被忽略。

在AIX上，当链接使用以-buildmode=c-archive构建的Go归档文件的C程序时，必须传递-Wl,-bnoobjreorder至C编译器。

# c主题——Go和C之间的调用

有两种不同的方式在Go和C/C++代码之间调用。

第一种方式是cgo工具，它是Go发行版的一部分。关于如何使用它的信息参阅cgo文档（go doc cmd/cgo）。

第二种是SWIG程序，它是语言之间通过接口连接的通用工具。关于SWIG的信息参阅[http://swig.org/](http://swig.org/)。当运行go build时，任何带有.swig扩展名的文件将传递给SWIG。任何带.swigcxx扩展名的文件将使用-c++选项传递给SWIG。

当cgo或SWIG任一种被使用时，go build将传递所有.c、.m、.s或.S文件至C编译器，以及所有.cc、.cpp、.cxx文件至C++编译器。可以设置CC或CXX环境变量来依次决定要使用的C或C++编译器。

# cache主题——构建和测试缓存

go命令缓存构建输出以在将来的构建中重用。缓存数据的默认位置为当前操作系统标准用户缓存目录下名字为go-build的子目录。设置GOCACHE环境变量覆盖该默认值，运行“go env GOCACHE”打印当前缓存目录。

go命令定期地删除最近未使用的缓存数据。运行“go clean -cache”删除所有缓存数据。

构建缓存会正确地考虑Go源文件、编译器、编译器选项等等的改变：在平常的使用中显式地清理缓存不是必须的。但是，构建缓存不检测由cgo导入的C库的改变。如果你在你的系统上对C库进行了修改，你将需要显式地清理缓存或使用-a构建标志（参阅“go help build”）来强制重新构建那些依赖已经更新了的C库的包。

go命令也缓存成功的包测试结果。更多细节参阅“go help test”。运行“go clean -testcache”删除所有缓存的测试结果（不包括缓存的构建结果）。

go命令也缓存以“go test -fuzz”在模糊测试中使用的值，特别地，当传递至模糊测试函数时扩展代码覆盖范围的值。这些值不被用作普通的构建和测试，但它们被存储在构建缓存的一个子目录中。运行“go clean -fuzzcache”删除所有缓存的模糊测试值。暂时地，这可能令到模糊测试降低效果。

GODEBUG环境变量可以启用关于缓存状态调试信息的打印：

* GODEBUG=gocacheverify=1：令go命令绕过任何缓存条目的使用，取而代之重新构建所有内容并检查结果是否与现有的缓存条目匹配。
* GODEBUG=gocachehash=1：令go命令打印为其使用来构建缓存查找键的所有内容哈希值的输入。输出是冗长的但能对调试缓存有用。
* GODEBUG=gocachetest=1：令go命令打印其关于是否重用缓存测试结果的决策的详细信息。

# environment主题——环境变量

go命令及其调用的工具查询环境变量以进行配置。如果环境变量未设置或为空，go命令使用一个合理的默认设置。要查看变量<NAME>的有效设置，运行“go env <NAME>”。要修改默认设置，运行“go env -w <NAME>=<VALUE>”。使用“go env -w”修改的默认值记录在存储在每个用户一个的配置目录中的Go环境变量配置文件中，即os.UserConfigDir所报告的值。配置文件的位置可以通过设置环境变量GOENV修改，且“go env GOENV”打印有效的位置，但“go env -w”不能修改默认位置。详细信息参阅“go help env”。

通用的环境变量：

* GCCGO：为“go build -compiler=gccgo”运行的gccgo命令。
* GO111MODULE：控制go命令运行在模块感知模式还是GOPATH模式。可为“off”、“on”或“auto”。参阅[https://golang.org/ref/mod#mod-commands](https://golang.org/ref/mod#mod-commands)。
* GOARCH：要为之编译代码的体系结构或处理器。例如amd64、386、arm、ppc64。
* GOBIN：“go install”安装命令使用的目录。
* GOCACHE：go命令存储缓存信息用以在将来的构建中重用的目录。
* GODEBUG：启用多种调试功能。参阅[https://go.dev/doc/godebug](https://go.dev/doc/godebug)。
* GOENV：Go环境变量配置文件位置。不能使用“go env -w”设置。在环境变量中设置GOENV=off禁止默认配置文件的使用。
* GOFLAGS：一个空白分隔的-flag=value设置列表，当给定的标志被当前命令已知时，会被默认应用于go命令。每个条目必须是单独的标志。因为条目是空白分隔的，标志值必须不包含空白。在命令行列出的标志在这个列表之后应用并因此覆盖之。
* GOINSECURE：模块路径前缀的glob模式（以Go的path.Match的语法）的逗号分隔的列表，其应总是以不安全的方式拉取。只应用于被直接拉取的依赖。GOINSECURE不禁用校验和数据库验证。GOPRIVATE或GONOSUMDB可以用来达到该目的。
* GOMODCACHE：go命令存储下载的模块的目录。
* GONOPROXY：等同于GOPRIVATE。
* GONOSUMDB：等同于GOPRIVATE。
* GOOS：要为之编译代码的操作系统。例如linux、darwin、windows、netbsd。
* GOPATH：控制各种文件存储的地方。参阅：“go help gopath”。
* GOPRIVATE：模块路径前缀的glob模式（以Go的path.Match的语法）的逗号分隔的列表，其应该总是直接拉取或不应该与校验和数据库比较。参阅[https://golang.org/ref/mod#private-modules](https://golang.org/ref/mod#private-modules)。
* GOPROXY：Go模块代理URL。详细信息参阅[https://golang.org/ref/mod#environment-variables](https://golang.org/ref/mod#environment-variables)和[https://golang.org/ref/mod#module-proxy](https://golang.org/ref/mod#module-proxy)。
* GOROOT：go树的根。
* GOSUMDB：使用的校验和数据库的名字，及可选的其公钥和URL。参阅[https://golang.org/ref/mod#authenticating](https://golang.org/ref/mod#authenticating)。
* GOTMPDIR：go命令写入临时源文件、包和二进制文件的目录。
* GOTOOLCHAIN：控制使用哪个Go工具链。参阅[https://go.dev/doc/toolchain](https://go.dev/doc/toolchain)。
* GOVCS：列出可与匹配的服务器一起使用的版本控制命令。参阅“go help vcs”。
* GOWORK：在模块感知模式，使用给定的go.work文件作为工作区文件。默认情况下或当GOWORK为“auto”时，go命令在当前目录并然后在包含其的目录中查找名字为go.work的文件，直到找到一个。如果一个有效的go.work文件被找到，指定的模块将共同地被用作主模块。如果GOWORK为“off”，或在“auto”模式下找不到go.work文件，工作区模式被禁用。

与cgo一起使用的环境变量：

* AR：当使用gccgo编译器构建时用来操作库归档文件的命令。默认为“ar”。
* CC：用来编译C代码的命令。
* CGO_CFLAGS：当编译C代码时cgo将传递给编译器的标志。
* CGO_CFLAGS_ALLOW：一个正则表达式，用来指定允许出现在#cgo CFLAGS源代码指令中的附加标志。不会应用于CGO_CFLAGS环境变量。
* CGO_CFLAGS_DISALLOW：一个正则表达式，指定必须被禁止出现在#cgo CFLAGS源代码指令中的标志。不会应用于CGO_CFLAGS环境变量。
* CGO_CPPFLAGS：类似CGO_CFLAGS，但提供给C预处理器。
* CGO_CPPFLAGS_ALLOW：类似CGO_CFLAGS_ALLOW，但提供给C预处理器。
* CGO_CPPFLAGS_DISALLOW：类似CGO_CFLAGS_DISALLOW，但提供给C预处理器。
* CGO_CXXFLAGS：类似CGO_CFLAGS，但提供给C++编译器。
* CGO_CXXFLAGS_ALLOW：类似CGO_CFLAGS_ALLOW，但提供给C++编译器。
* CGO_CXXFLAGS_DISALLOW：类似CGO_CFLAGS_DISALLOW，但提供给C++编译器。
* CGO_ENABLED：cgo命令是否支持。0或1。
* CGO_FFLAGS：类似CGO_CFLAGS，但提供给Fortran编译器。
* CGO_FFLAGS_ALLOW：类似CGO_CFLAGS_ALLOW，但提供给Fortran编译器。
* CGO_FFLAGS_DISALLOW：类似CGO_CFLAGS_DISALLOW，但提供给Fortran编译器。
* CGO_LDFLAGS：类似CGO_CFLAGS，但提供给链接器。
* CGO_LDFLAGS_ALLOW：类似CGO_CFLAGS_ALLOW，但提供给链接器。
* CGO_LDFLAGS_DISALLOW：类似CGO_CFLAGS_DISALLOW，但提供给链接器。
* CXX：用来编译C++代码的命令。
* FC：用来编译Fortran代码的命令。
* PKG_CONFIG：pkg-config工具的路径。

体系结构特定的环境变量：

* GO386：对GOARCH=386，如何实现浮点指令。有效值为sse2（默认）、softfloat。
* GOAMD64：对GOARCH=amd64，要为之编译的微体系结构级别。有效值为v1（默认）、v2、v3、v4。参阅[https://golang.org/wiki/MinimumRequirements#amd64](https://golang.org/wiki/MinimumRequirements#amd64)。
* GOARM：对GOARCH=arm，要为之编译的ARM体系结构。有效值为5、6、7。
* GOMIPS：对GOARCH=mips{,le}，是否使用浮点指令。有效值为hardfloat（默认）、softfloat。
* GOMIPS64：对GOARCH=mips64{,le}，是否使用浮点指令。有效值为hardfloat（默认）、softfloat。
* GOPPC64：对GOARCH=ppc64{,le}，目标ISA（指令集架构，Instruction Set Architecture）。有效值为power8（默认）、power9、power10。
* GOWASM：对GOARCH=wasm，要使用的实验性WebAssembly特性的逗号分隔的列表。有效值为satconv、signext。

与代码覆盖率一起使用的环境变量：

* GOCOVERDIR：写入由运行“go build -cover”二进制程序而生成的代码覆盖率数据文件的目录。需要启用GOEXPERIMENT=coverageredesign。

特殊用途的环境变量：

* GCCGOTOOLDIR：如果设置，表示在哪可以找到gccgo工具，例如cgo。默认值基于gccgo是怎么配置的。
* GIT_ALLOW_PROTOCOL：由Git定义。允许与git fetch/clone一起使用的冒号分隔的方案（scheme）列表。如果设置，任何未明确提及的方案会被“go get”认为是不安全的。因为该变量是被Git定义，默认值不可以使用“go env -w”设置。
* GOEXPERIMENT：要启用或禁用的工具链试验特性的逗号分隔列表。可用的试验特性列表可能随着时间随意地改变。当前有效值参阅src/internal/goexperiment/flags.go。警告：此变量提供给Go工具链自身的开发和测试。在该用途之外使用是不支持的。
* GOROOT_FINAL：已安装的Go树的根，用在当其安装在其构建位置以外的地方时。堆栈跟踪中的文件名从GOROOT重写为GOROOT_FINAL。
* GO_EXTLINK_ENABLED：当与使用cgo的代码一起使用-linkmode=auto时，链接器是否应该使用外部链接模式。设为0禁用外部链接模式，1为启用之。

从“go env”可获得的附加信息但不是从环境变量读取的：

* GOEXE：可执行文件名后缀（在Windows上为“.exe”，在其它系统上为“”）。
* GOGCCFLAGS：提供给CC命令的空白分隔的参数列表。
* GOHOSTARCH：Go工具链二进制文件的体系结构（GOARCH）。
* GOHOSTOS：Go工具链二进制文件的操作系统（GOOS）。
* GOMOD：主模块的go.mod的绝对路径。如果模块感知模式启用，但没有go.mod，GOMOD将为os.DevNull（在类Unix系统上为“/dev/null”，在Windows上为“NUL”）。如果模块感知模式禁用，GOMOD将为空字符串。
* GOTOOLDIR：go工具（compile、cover、doc等等）安装的目录。
* GOVERSION：已安装的Go树的版本，如同runtime.Version所报告的一样。

# filetype主题——文件类型

go命令检查每个目录中限定的一组文件的内容。其根据文件名的扩展名识别哪些文件要检查。这些扩展名为：

* .c/.h：C源文件。如果包使用cgo或SWIG，这些文件将使用操作系统本地编译器（通常为gcc）编译；否则它们将触发一个错误。
* .cc/.cpp/.cxx/.hh/.hpp/.hxx：C++源文件。只在使用cgo或SWIG时有用，并总是使用操作系统本地编译器编译。
* .go：Go源文件。
* .m：Objective-C源文件。只在使用cgo时有用，并总是使用操作系统本地编译器编译。
* .s/.S/.sx：汇编源文件。如果包使用cgo或SWIG，它们将使用操作系统本地汇编器（通常为gcc（sic））汇编；否则它们将使用Go汇编器汇编。
* .swig/.swigcxx：SWIG定义文件。
* .syso：系统对象文件。

这些类型除.syso之外的每个类型的文件都可能包含构建约束，但go命令在文件中不是空行或//-style行注释的第一项处停止扫描构建约束。更多详细信息参阅go/build包文档。

# go.mod主题——go.mod文件

模块版本是由源文件树定义的，在其根目录中有一个go.mod文件。当go命令运行时，它查找当前目录并然后查找相继的父目录来找出go.mod，go.mod标记主（当前）模块的根。

go.mod文件格式在[https://golang.org/ref/mod#go-mod-file](https://golang.org/ref/mod#go-mod-file)中详细地描述。

要创建一个新的go.mod文件，使用“go mod init”。详细信息参阅“go help mod init”或[https://golang.org/ref/mod#go-mod-init](https://golang.org/ref/mod#go-mod-init)。

要添加缺失的模块依赖或删除不需要的依赖，使用“go mod tidy”。详细信息，参阅“go help mod tidy”或[https://golang.org/ref/mod#go-mod-tidy](https://golang.org/ref/mod#go-mod-tidy)。

要添加、升级、降级、删除特定的模块依赖，使用“go get”。详细信息，参阅“go help module-get”或[https://golang.org/ref/mod#go-get](https://golang.org/ref/mod#go-get)。

要进行其它更改或要以JSON解析go.mod以供其它工具使用，使用“go mod edit”。参阅“go help mod edit”或[https://golang.org/ref/mod#go-mod-edit](https://golang.org/ref/mod#go-mod-edit)。

# gopath主题——GOPATH环境变量

（译注：当使用GOPATH模式时，GOPATH用于解析import导入；当使用模块感知模式时，GOPATH不用于解析import导入。）

GOPATH用于解析import语句，其被go/build包实现并在go/build包中记录文档。

GOPATH环境变量列出查找Go代码的位置。在Unix上，值是冒号分隔的字符串。在Windows上，值是分号分隔的字符串。在Plan 9上，值是一个列表。

如果环境变量未设置，GOPATH默认为在用户的主目录中名字为“go”的子目录（在Unix上为$HOME/go，在Windows上为%USERPROFILE%\go），除非该目录包含Go发行版。运行“go env GOPATH”来查看当前的GOPATH。

参阅[https://golang.org/wiki/SettingGOPATH](https://golang.org/wiki/SettingGOPATH)来设置自定义GOPATH。

GOPATH中列出的每个目录必须有规定的结构：

* src目录包含源代码。src下的路径决定import路径或可执行文件名。
* pkg目录包含安装的包对象文件。如同在Go树中，每个目标操作系统和体系结构对都有其自己的pkg子目录（pkg/GOOS_GOARCH）。

	如果DIR是GOPATH中列出的一个目录，源代码在DIR/src/foo/bar中的包可以被导入为“foo/bar”并且其编译形式的文件会被安装到“DIR/pkg/GOOS_GOARCH/foo/bar.a”。
* bin目录包含编译的命令。每个命令以其源代码目录命名，但只取最后一个元素，不是全部的路径。亦即，源代码在DIR/src/foo/quux中的命令被安装至DIR/bin/quux，而不是DIR/bin/foo/quux。“foo/”前缀被剥掉，因此你可以添加DIR/bin至你的PATH中来访问安装的命令。如果GOBIN环境变量被设置，命令会被安装至其命名的目录而不是DIR/bin。GOBIN必须为绝对路径。

这是一个示例目录布局：

```
GOPATH=/home/user/go

/home/user/go/
	src/
		foo/
			bar/       （bar包中的go代码）
			    x.go
			quux/      （main包中的go代码）
				y.go
	bin/
		quux           （安装的命令）
	pkg/
		linux_amd64/
			foo/
				bar.a  （安装的包对象文件）
```

go搜索GOPATH中列出的每个目录来查找源代码，但新的包总是下载至列表中的第一个目录。

示例参阅[https://golang.org/doc/code.html](https://golang.org/doc/code.html)。

## GOPATH和模块

当使用模块时，GOPATH不再用来解析import导入。然而，其仍然会被用来保存下载的源代码（在GOPATH/pkg/mod中）和编译的命令（在GOPATH/bin中）。

## internal目录

（译注：使用GOPATH模式和模块感知模式，均可使用internale目录。）

在名为“internal”目录中或以下的代码只可以被以“internal”的父目录为根的目录树中的代码导入。这是上面的目录布局扩展版本：

```
/home/user/go/
	src/
		crash/
			bang/         （bang包中的go代码）
				b.go
		foo/              （foo包中的go代码）
			f.go
			bar/          （bar包中的go代码）
				x.go
			internal/
				baz/      （baz包中的go代码）
					z.go
			quux/         （main包中的go代码）
				y.go
```

z.go中的代码被导入为“foo/internal/baz”，但该import导入语句只能出现在以foo为根的子树中的源文件中。源文件foo/f.go、foo/bar/x.go、foo/quux/y.go都可导入“foo/internal/baz”，但源文件crash/bang/b.go不可以。

细节参阅[https://golang.org/s/go14internal](https://golang.org/s/go14internal)。

## vendor目录

go 1.6包含使用外部依赖的本地副本的支持，以满足那些依赖的导入，通常被称为使用vendor（vendoring）。

在名为“vendor”目录以下的代码只可以被以“vendor”的父目录为根的目录树中的代码导入，并且只使用忽略直到并包括vendor元素前缀的导入路径。

这是从上一小节来的例子，但“internal”目录重命名为“vendor”并且添加了一个新的foo/vendor/crash/bang目录：

```
/home/user/go/
	src/
		crash/
			bang/              （bang包中的代码）
				b.go
		foo/                   （foo包中的代码）
			f.go
			bar/               （bar包中的代码）
				x.go
			vendor/
				crash/
					bang/      （bang包中的代码）
						b.go
				baz/           （baz包中的代码）
					z.go
			quux/              （main包中的代码）
				y.go
```

应用和internal相同的可见性规则，但在z.go中的代码导入为“baz”，而不是“foo/vendor/baz”。

在源代码树中较深的vendor目录中的代码隐藏在较高目录中的代码。在以foo为根的子树中“crash/bang”的导入解析为“foo/vendor/crash/bang”，而不是最顶层的“crash/bang”。

vendor目录中的代码不受导入路径检查的影响（参阅“go help importpath”）。

当“go get”检出或更新git仓库时，其现在也更新子模块。

vendor目录不影响第一次被“go get”检出的新仓库的位置：它们总是放置在主GOPATH中，从不在vendor子树中。

细节参阅[https://golang.org/s/go15vendor](https://golang.org/s/go15vendor)。

# gopath-get主题——遗留的GOPATH的go get

```shell
go get [-d] [-f] [-t] [-u] [-v] [-fix] [build flags] [packages]
```

“go get”命令根据go命令运行在模块感知模式还是遗留的GOPATH模式来改变行为。这里的帮助文本，即使在模块感知模式也可作为“go help gopath-get”访问，描述“go get”在遗留的GOPATH模式下的操作。

get下载由导入路径指定名字的包，以及它们的依赖。然后安装指定名字的包，类似“go install”。

标志：

* -d：在下载包之后停止；亦即，其令get不安装包。
* -f：只在-u被设置时有效，强制get -u不验证每个包都是从其导入路径隐含的源代码控制仓库检出。如果源代码是源的本地fork分支这将会有用。
* -fix：在解析依赖和构建代码之前在已下载的包上运行fix工具。
* -t：同时下载指定的包构建测试所需的包。
* -u：使用网络来更新指定名字的包及其依赖。默认情况下，get使用网络来检出缺少的包但不会使用它来查找已存在包的更新。
* -v：启用详细的进度和调试输出。

get也接受构建标志来控制安装。参阅“go help build”。

当检出一个新包时，get创建目标目录GOPATH/src/<import-path>。如果GOPATH包含多个入口，get使用第一个。更多详细信息参阅：“go help gopath”。

当检出或更新包时，get查找匹配Go本地安装版本的分支（branch）或标签（tag）。最重要的规则是，如果本地安装正在运行版本“go1”，get查找名字为“go1”的分支或标签。如果没有这样的版本存在，其检索包的默认分支。

当go get检出或更新一个Git仓库，其也会更新被该仓库引用的任何git子模块。

get从不检出或更新在vendor目录中存储的代码。

关于构建标志的更多信息，参阅“go help build”。

关于指定包的更多信息，参阅“go help packages”。

关于“go get”如何查找下载的源代码的更多信息，参阅“go help importpath”。

这段文字描述当使用GOPATH来管理源代码和依赖时get的行为。如果相反go命令运行在模块感知模式，“go help get”的内容会不同，类似地，get的标志和行为的细节也会改变。参阅“go help modules”和“go help module-get”。

参阅：go build、go install、go clean。

# goproxy主题——模块代理协议

Go模块代理是能响应特定格式URL的GET请求的任何web服务器。请求没有查询（query）参数，因此即使从固定文件系统（包括file://URL）提供服务的站点也能成为模块代理。

GOPROXY上的协议的详细信息，参阅[https://golang.org/ref/mod#goproxy-protocol](https://golang.org/ref/mod#goproxy-protocol)。

# importpath主题——import路径语法

导入路径（参阅“go help packages”）表示存储在本地文件系统的包。通常，导入路径表示或者标准包（如“unicode/utf8”）或者在其中一个工作空间（译注：其概念可参考 **相对导入路径** 小节）找到的包（更多细节参阅：“go help gopath”）。

## 相对导入路径

以./或../开头的导入路径称为相对路径。工具链以两种方式支持相对导入路径作为快捷方式。

第一种，相对路径可以用作命令行上的速记。如果你工作在包含以“unicode”导入的代码的目录中并想为“unicode/utf8”运行测试，你可以输入“go test ./utf8”而不需要指定完整的路径。同样地，在相反的情况下，“go test ..”将从“unicode/utf8”目录测试“unicode”。相对模式也是允许的，如“go test ./...”用来测试所有子目录。关于模式语法的详细信息参阅“go help packages”。

第二种，如果你不是在工作空间中编译Go程序，你可以在该程序import导入语句中使用相对路径来引用附近同样不在工作空间中的代码。这更易于在通常的工作空间外试验使用小型的多包程序，但这种程序不能用“go install”来安装（没有工作空间来安装它们），因此每次它们被构建都会从头开始重新构建。为了避免歧义，Go程序不可以在工作空间中使用相对导入路径。

## 远程导入路径

某些导入路径也描述了如何使用版本控制系统为包获取源代码。

一些常见的代码托管站点有特定的语法：

```
Bitbucket (Git, Mercurial)

	import "bitbucket.org/user/project"
	import "bitbucket.org/user/project/sub/directory"

GitHub (Git)

	import "github.com/user/project"
	import "github.com/user/project/sub/directory"

Launchpad (Bazaar)

	import "launchpad.net/project"
	import "launchpad.net/project/series"
	import "launchpad.net/project/series/sub/directory"

	import "launchpad.net/~user/project/branch"
	import "launchpad.net/~user/project/branch/sub/directory"

IBM DevOps Services (Git)

	import "hub.jazz.net/git/user/project"
	import "hub.jazz.net/git/user/project/sub/directory"
```

对于托管在其它服务器上的代码，导入路径或者用版本控制类型限定，或者go工具可以通过http/https动态地拉取该导入路径并从HTML中的一个<meta>标签发现代码的所在。

要声明代码的位置，`repository.vcs/path`格式的导入路径指定给出的仓库，可带或不带.vcs后缀（译注：见支持的版本控制系统），来使用指定名字的版本控制系统，然后是该仓库中的路径。支持的版本控制系统为：

```
Bazaar      .bzr
Fossil      .fossil
Git         .git
Mercurial   .hg
Subversion  .svn
```

例如，`import "example.org/user/foo.hg"`表示在example.org/user/foo或foo.hg的Mercurial仓库的根目录，`import "example.org/repo.git/foo/bar"`表示在example.org/repo或repo.git的Git仓库的foo/bar目录。

当版本控制系统支持多种协议时，在下载时每一种会轮流尝试。例如，Git下载尝试https://，然后git+ssh://。

默认情况下，下载限于已知的安全协议（例如https、ssh）。要为Git下载覆盖该设置，可以设置GIT_ALLOW_PROTOCOL环境变量（更多细节参阅：“go help environment”）。

如果导入路径不是已知的代码托管站点且也缺少版本控制限定符，go工具尝试通过https/http拉取导入路径并在文档的HTML<head>中查找<meta>标签。

meta标签有如下格式（译注：标签中name="go-import"是固定的）：

```html
<meta name="go-import" content="import-prefix vcs repo-root">
```

import-prefix是和仓库根对应的导入路径。其必须为用“go get”抓取的包的前缀或完全匹配。如果其不是完全匹配，会对前缀生成另一个http请求来验证<meta>标签是匹配的。

meta标签应该尽可能早地在文件中出现。特别地，其应该在任何原始的JavaScript或CSS之前出现，以避免迷惑go命令的受限的解析器。

vcs是“bzr”、“fossil”、“git”、“hg”、“svn”之一。

repo-root是包含方案但不包含.vcs限定符的版本控制系统的根。

例如，`import "example.org/pkg/foo"`将导致以下请求：

```
https://example.org/pkg/foo?go-get=1 （首选的）
http://example.org/pkg/foo?go-get=1  （备选的，只与正确地设置GOINSECURE的使用一起）
```

（译注：假设该页面的请求为https://example.org/exproj?go-get=1）如果该页面包含meta标签`<meta name="go-import" content="example.org git https://code.org/r/p/exproj">`，go工具将验证https://example.org/?go-get=1包含相同的meta标签，并然后git clone https://code.org/r/p/exproj进GOPATH/src/example.org。

当使用GOPATH时，下载的包被写入至在GOPATH环境变量中列出的第一个目录。（参阅“go help gopath-get”和“go help gopath”。）

当使用模块时，下载的包存储在模块缓存中。参阅[https://golang.org/ref/mod#module-cache](https://golang.org/ref/mod#module-cache)。

当使用模块时，go-import meta标签一个额外的变量会被识别，且胜于那些列出版本控制系统的变量。该变量使用“mod”作为内容值中的vcs，如同：

```html
<meta name="go-import" content="example.org mod https://code.org/moduleproxy">
```

该标签表示用以example.org开头的路径从URL为https://code.org/moduleproxy上可用的模块代理拉取模块。关于代理协议的详细信息参阅[https://golang.org/ref/mod#goproxy-protocol](https://golang.org/ref/mod#goproxy-protocol)。

## 导入路径检查

当如上所述的自定义导入路径功能重定向至已知的代码托管站点时，每个结果包都有两个可能的导入路径，使用自定义域名或已知的托管站点。

如果package语句后立即跟着（在下一个新行之前）这两种格式之一的注释，则被称为带有“导入注释”（import comment）：

```go
package math // import "path"
package math /* import "path" */
```

go命令会拒绝安装带有导入注释的包，除非包是被导入注释中的导入路径引用的。用这种办法，导入注释让包作者确保使用自定义导入路径而不是指向底层代码托管站点的直接路径。

对在vendor目录树中找到的代码导入路径检查是禁用的。这令拷贝代码进vendor目录树中的备用位置而无需更新导入注释成为可能。

当使用模块时导入路径检查也是禁用的。导入路径注释被go.mod文件中的module语句所淘汰。

更多细节参阅[https://golang.org/s/go14customimport](https://golang.org/s/go14customimport)。

# module-auth主题——使用go.sum的模块校验

当go命令下载模块zip文件或go.mod文件至模块缓存中时，会计算一个加密哈希并将其与一个已知的值比较，来验证自从首次下载以来文件没有改变。已知的哈希存储在模块根目录中命名为go.sum的文件中。根据GOSUMDB、GOPRIVATE和GONOSUMDB的值，哈希也可从校验和数据库下载。

详细信息，参阅[https://golang.org/ref/mod#authenticating](https://golang.org/ref/mod#authenticating)。

# module-get主题——模块感知的go get

（译注：该主题介绍使用模块管理源代码和依赖的行为，亦即 **go get——添加依赖包至当前模块并安装之** 小节的内容，使用GOPATH的行为描述于 **gopath-get主题——遗留的GOPATH的go get** 小节。）

# modules主题——模块，模块版本，及更多

模块是Go怎样管理依赖。

一个模块是一起发布、版本化和分发的包集合。模块可以直接从版本控制仓库或从模块代理服务器下载。

关于模块的一系列教程，参阅[https://golang.org/doc/tutorial/create-module](https://golang.org/doc/tutorial/create-module)。

关于模块的详细参考，参阅[https://golang.org/ref/mod](https://golang.org/ref/mod)。

默认情况下，go命令可从[https://proxy.golang.org](https://proxy.golang.org)下载模块。其可使用[https://sum.golang.org](https://sum.golang.org)上的校验和数据库校验模块。两服务都由Google的Go团队运营。这些服务的隐私政策分别可在[https://proxy.golang.org/privacy](https://proxy.golang.org/privacy)和[https://sum.golang.org/privacy](https://sum.golang.org/privacy)得到。

go命令的下载行为可以使用GOPROXY、GOSUMDB、GOPRIVATE和其它环境变量配置。更多信息参阅“go help environment”和[https://golang.org/ref/mod#private-module-privacy](https://golang.org/ref/mod#private-module-privacy)。

# packages主题——包列表和模式

许多命令应用于一个包集合：

```shell
go <action> [packages]
```

通常，[packages]是导入路径的列表。

为根路径或以.或..元素开头的导入路径被解析为文件系统路径并表示该目录中的包。

否则，对某些在GOPATH环境变量中列出的DIR，导入路径P表示DIR/src/P目录中找到的包（更多详细信息参阅：“go help gopath”）。

如果没有给出导入路径，操作应用于当前目录中的包。

路径有四个保留的名字，不应该用于使用go工具进行构建的包：

* “all”：扩展为所有GOPATH树中找到的所有包。例如，“go list all”列出本地系统上的所有包。当使用模块时，“all”扩展为主模块及其依赖中的所有包，包括其中任何的测试所需的依赖。
* “cmd”：扩展为Go仓库的命令及其内部库。
* “main”：表示独立的可执行文件中的顶层包。
* “std”：类似all但只扩展为标准Go库中的包。

以“cmd/”开头的导入路径只匹配Go仓库中的源代码。

导入路径是一个模式如果其包含一个或多个“...”通配符，其中每个都可以匹配任何字符串，包括空字符串和含斜杠的字符串。这样的模式扩展为GOPATH树中找到的名字匹配模式的所有包目录。

为了令常见的模式更方便，有两种特殊情况。第一种，模式末尾的/...可以匹配空字符串，因此net/...匹配net和在其子目录中的包，如net/http。第二种，任何包含通配符的斜杠分隔的模式元素从不参与vendor包路径中“vendor”元素的匹配，因此./...不匹配./vendor或./mycode/vendor的子目录中包，但./vendor/...和./mycode/vendor/...可以。然而，注意，自身包含代码的名字为vendor的目录不是vendor的包：cmd/vendor可能是名字为vendor的命令，并且模式cmd/...匹配之。更多关于使用vendor的信息参阅[golang.org/s/go15vendor](golang.org/s/go15vendor)。

导入路径也可以命名要从远程仓库下载的包。详细信息运行“go help importpath”。

程序中的每个包必须有唯一的导入路径。按习惯，这通过使每个路径以属于你的唯一前缀开始来实现。例如，在Google内部使用的路径都以“google”开头，并且表示远程仓库的路径以代码的路径开头，例如“github.com/user/repo”。

程序中的包不必有唯一的包名，但有两个有特殊含义的保留包名。名字main表示命令，而不是库。命令会被构建为二进制且不能被导入。名字documentation表示目录中非Go程序的文档。documentation包中的文件会被go命令忽略。

作为特殊情况，如果包列表是单个目录中的.go文件的列表，命令会被应用于恰好由那些文件构成的单个合成的包，忽略那些文件中的任何构建约束并忽略目录中任何其它文件。

以“.”或“_”开头的目录和文件名会被go工具忽略，如同名字为“testdata”的目录。

# private主题——下载非公共代码的配置

go命令默认从proxy.golang.org上的公共Go模块镜像下载模块。无论来源是什么，其也默认将下载的模块与sum.golang.org上的公共Go校验和数据库校验。对公开可获得的源代码这些默认行为工作得很好。

GOPRIVATE环境变量控制哪些模块go命令认为是私有的（不是公开可获得的）并应该因此不使用代理或校验和数据库。变量是模块路径前缀的glob模式（以Go的path.Match的语法）的逗号分隔的列表。例如，

```
GOPRIVATE=*.corp.example.com,rsc.io/private
```

其令go命令将带有匹配任一模式的路径前缀的任何模块视为私有，包括git.corp.example.com/xyzzy、rsc.io/private、rsc.io/private/quux。

为了对模块下载和校验进行细粒度的控制，GONOPROXY和GONOSUMDB接受相同类型的glob列表，并覆盖GOPRIVATE以分别地对是否使用代理和校验和数据库作特定的决定。

例如，如果公司运行了一个为私有模块服务的模块代理，用户可以如下使用来配置go：

```
GOPRIVATE=*.corp.example.com
GOPROXY=proxy.example.com
GONOPROXY=none
```

GOPRIVATE环境变量也被用来为GOVCS环境变量定义“public”和“private”模式；参阅“go help vcs”。对该用法，GOPRIVATE甚至应用于GOPATH模式中。在那种情况下，其匹配导入路径而不是模块路径。

“go env -w”命令（参阅“go help env”）可以用来为未来的go命令调用设置这些环境变量。

更多详细信息，参阅[https://golang.org/ref/mod#private-modules](https://golang.org/ref/mod#private-modules)。

# testflag主题——测试标志

“go test”命令接受应用于“go test”自身的标志和应用于生成的测试二进制的标志。

标志中的几个控制性能分析（profiling）并写入适合“go tool pprof”的性能分析执行的文件；更多信息运行“go tool pprof -h”。pprof的--alloc_space、--alloc_objects、--show_bytes选项控制信息如何呈现。

以下的标志被“go test”命令识别并控制任何测试的执行：

（译注：以下标志阐述中，单元测试指test，基准测试指benchmark，模糊测试指fuzz，范例指example；性能分析指profile。）

* -bench regexp：只运行那些匹配正则表达式的基准测试。默认情况下，不运行基准测试。要运行所有基准测试，使用“-bench .”或“-bench=.”。正则表达式由无括号的斜杠（/）字符分隔为一系列正则表达式，且基准测试的标识符的每部分必须匹配序列中对应的元素，如果有的话。匹配项的可能的父项以b.N=1运行来标识子基准测试。例如，给定-bench=X/Y，顶层基准测试匹配X且以b.N=1运行来发现任何匹配Y的子基准测试，其是之后会全部运行的项。
* -benchtime t：为每个基准测试运行足够的迭代以消耗时间t，其被指定为time.Duration（例如，-benchtime 1h30s）。默认值为1秒（1s）。特殊的语法Nx表示运行基准测试N次（例如，-benchtime 100x）。
* -count n：运行每个单元测试、基准测试和模糊测试种子n次（默认为1）。如果-cpu被设置，对每个GOMAXPROCS值（译注：-cpu设置的列表中的每个值）运行n次。范例总是只运行一次。-count不应用于被-fuzz匹配的模糊测试。
* -cover：启用覆盖率分析。注意因为覆盖率分析通过在编译前注解源代码来工作，启用覆盖率分析的编译和测试失败可能报告和原来的源代码不对应的行号。
* -covermode set,count,atomic：设置对被测试的包的覆盖率分析模式。默认为“set”除非-race被启用，这种情况下为“atomic”。值为：

	* set（bool）：此语句是否运行？
	* count（int）：此语句运行多少次？
	* atomic（int）：计数，但在多线程测试中正确；明显更高消耗。

	会设置-cover。
* -coverpkg pattern1,pattern2,pattern3：对匹配模式的包的每个单元测试应用覆盖率分析。默认对每个单元测试只分析被测试的包。关于包模式的描述参阅“go help packages”。会设置-cover。
* -cpu 1,2,4：指定GOMAXPROCS（译注：runtime.GOMAXPROCS的返回值）值列表，单元测试、基准测试或模糊测试会以此来执行。默认为GOMAXPROCS的当前值。-cpu不应用于被-fuzz匹配的模糊测试。
* -failfast：在首个单元测试失败之后不再开始新的单元测试。
* -fullpath：在错误信息中展示完整的文件名。
* -fuzz regexp：运行匹配正则表达式的模糊测试。当指定时，命令行参数必需匹配在主模块中的正好一个包，并且正则表达式必需匹配在该包中的正好一个模糊测试。模糊测试将在单元测试、基准测试、其它模糊测试的种子集之后发生，并且范例已经完成。详细信息参阅testing包文档的Fuzzing小节。
* -fuzztime t：在模糊测试期间运行模糊测试目标的足够的迭代以消耗时间t，其被指定为一个time.Duration（例如，-fuzztime 1h30s）。默认为永远运行。特殊的语法Nx表示运行模糊测试目标N次（例如，-fuzztime 1000x）。
* -fuzzminimizetime t：在每个最小限度的尝试期间运行模糊测试目标的足够的迭代以消耗时间t，其被指定为一个time.Duration（例如，-fuzzminimizetime 30s）。默认为60s。特殊的语法Nx表示运行模糊测试目标N次（例如，-fuzzminimizetime 100x）。
* -json：以JSON记录详细的输出和测试结果日志。这以机器可读的格式表示与-v标志相同的信息。
* -list regexp：列出匹配正则表达式的单元测试、基准测试、模糊测试或范例。没有单元测试、基准测试、模糊测试或范例会被运行。这只会列出顶层测试。没有子单元测试或子基准测试会被展示。
* -parallel n：允许调用t.Parallel的单元测试函数并行执行，以及当运行种子集时调用t.Parallel的模糊测试目标并行执行。这个标志的值是同时运行的测试的最大值。当运行模糊测试时，这个标志的值是可以同时调用模糊测试函数的子线程的最大值，不管T.Parallel是否被调用。默认下，-parallel设置为GOMAXPROCS的值。由于CPU争用，设置-parallel为高于GOMAXPROCS的值可能导致降低性能，特别是当运行模糊测试时。注意-parallel只应用于单个测试二进制中。根据-p标志的设置（参阅“go help build”），“go test”命令也可能为多个包并行运行测试。
* -run regexp：只运行匹配正则表达式的那些单元测试、范例和模糊测试。对于单元测试，正则表达式由无括号的斜杠（/）字符分隔为一系列正则表达式，且测试的标识符的每部分必须匹配序列中对应的元素，如果有的话。注意匹配项的可能的父项也会运行，因此-run=X/Y匹配、运行、报告所有匹配X的单元测试的的结果，即使它们并没有匹配Y的子单元测试，因为必须运行它们来查找那些子单元测试。参阅-skip。
* -short：告诉长时间运行的测试来缩短它们的运行时间。默认是关闭的，但在all.bash运行期间会设置，因此安装Go树可以运行健全的检查但不会花费时间运行详尽的测试。
* -shuffle off,on,N：随机化单元测试和基准测试的执行顺序。其默认是off。如果-shuffle设置为on，那么他将使用系统时钟对随机数发生器播种。如果-shuffle设置为一个整数N，那么N将被用作种子的值。在两种情况下，种子都会被报告再现性。
* -skip regexp：只运行不匹配正则表达式的那些单元测试、范例、模糊测试和基准测试。类似于-run和-bench，对单元测试和基准测试，正则表达式由无括号的斜杠（/）字符分隔为一系列正则表达式，且测试的标识符的每部分必须匹配序列中对应的元素，如果有的话。
* -timeout d：如果测试二进制运行长于时长d，会panic。如果d是0，超时会被禁用。默认为10分钟（10m）。
* -v：详细的输出：在测试运行时记录所有测试的日志。即使测试成功也会打印所有由Log和Logf调用产生的文本。
* -vet list：配置“go test”运行期的“go vet”调用来使用vet检查的逗号分隔的列表。如果列表为空，“go test”以一个精心设计的被认为总是值得提及的检查列表运行“go vet”。如果列表为“off”，“go test”根本不会运行“go vet”。

以下标志也会被“go test”识别并能用来在执行期间对测试进行性能分析：

* -benchmem：为基准测试打印内存分配统计数据。
* -blockprofile block.out：当所有测试完成时写入goroutine阻塞性能分析至指定的文件。会如同-c（译注：go test的标志）那样写入测试二进制。
* -blockprofilerate n：通过使用n调用runtime.SetBlockProfileRate来控制在goroutine阻塞性能分析中提供的详细信息。参阅“go doc runtime.SetBlockProfileRate”。性能分析器旨在程序花费于阻塞平均每n纳秒采样一次阻塞事件。默认情况下，如果-test.blockprofile（译注：即-blockprofile标志）设置时不带本标志，所有阻塞事件都会被记录，等同于-test.blockprofilerate=1。
* -coverprofile cover.out：在所有测试通过后写入覆盖率性能分析至文件。会设置-cover。
* -cpuprofile cpu.out：在退出前写入CPU性能分析至指定的文件。会如同-c那样写入测试二进制。
* -memprofile mem.out：在所有测试通过后写入内存分配性能分析至文件。会如同-c那样写入测试二进制。
* -memprofilerate n：通过设置runtime.MemProfileRate启用更多精确的（并昂贵的）内存分配性能分析。参阅“go doc runtime.MemProfileRate”。要对所有内存分配进行性能分析，使用-test.memprofilerate=1。
* -mutexprofile mutex.out：当所有测试完成时写入互斥锁争用性能分析至指定的文件。会如同-c那样写入测试二进制。
* -mutexprofilefraction n：采样n个持有争用互斥锁的goroutine堆栈跟踪信息中的1个。
* -outputdir directory：将性能分析的输出文件放置于指定的目录中，默认为“go test”运行的目录中。
* -trace trace.out：在退出前写入执行跟踪信息至指定的文件。

这些标志的每个也能以可选的“test.”前缀被识别，如同-test.v。然而，当直接调用生成的测试二进制时（“go test -c”的结果），前缀是强制的。

视情况而定，在调用测试二进制之前，“go test”命令重写或移除在可选的包列表之前和之后的已识别的标志。

例如，命令`go test -v -myflag testdata -cpuprofile=prof.out -x`将编译测试二进制并以`pkg.test -test.v -myflag testdata -test.cpuprofile=prof.out`运行（-x标志被移除因为其只应用于go命令的执行，而非测试自身）。

生成性能分析的测试标志（除了用于覆盖率的）也保留测试二进制在pkg.test中以在分析性能分析结果时使用。

当“go test”运行测试二进制时，其会在对应的包源代码目录内运行。根据测试的不同，当直接调用生成的测试二进制时可能需要做同样的事情。由于该目录可能位于模块缓存之中，其可能是只读的且被校验和验证，测试不得向其或在模块中的任何其它目录写入，除非被用户明确地要求（例如以-fuzz标志，其写入失败结果至testdata/fuzz）。

命令行包列表，如果存在，必须出现在任何go test命令不认识的标志之前。继续上面的例子，包列表本应出现在-myflag之前，但可以出现在-v的任一侧。

当“go test”运行在包列表模式时，“go test”缓存成功的包测试结果来避免测试的不必要重复运行。要禁用测试缓存，使用除可缓存标志以外的任意测试标志或参数。显式地禁用测试缓存的习惯方式为使用-count=1。

要避免测试二进制的参数被解析为已知的标志或包名，使用-args（参阅“go help test”），其将命令行中余下部分不解析和不修改地传递至测试二进制。

例如，命令`go test -v -args -x -v`将编译测试二进制并以`pkg.test -test.v -x -v`运行。类似地，`go test -args math`将编译测试二进制并以`pkg.test math`运行。

在第一个例子中，-x和第二个-v不修改地传递至测试二进制且对go命令自身没有影响。在第二个例子中，参数math传递至测试二进制，而不是被解析为包列表。

# testfunc主题——测试函数

“go test”命令期望在与被测试的包对应的“*_test.go”文件中找到测试、基准测试和范例函数。

单元测试函数是一个名为TestXxx（Xxx处不以小写字母开头）且应具有特征：

```go
func TestXxx(t *testing.T) { ... }
```

基准测试函数是一个名为BenchmarkXxx且应具有特征：

```go
func BenchmarkXxx(b *testing.B) { ... }
```

模糊测试是一个名为FuzzXxx且应具有特征：

```go
func FuzzXxx(f *testing.F) { ... }
```

范例函数类似于测试函数，但取代使用*testing.T来报告成功或失败，会打印输出至os.Stdout。如果函数中的最后的注释以“Output:”开头，那么输出会精确地与注释比较（参阅下面的例子）。如果最后的注释以“Unordered output:”开头，那么输出与注释比较，但是忽略行的顺序。没有如此注释的范例会被编译但不会被执行。“Output:”后没有文本的范例会被编译、执行并期望不产生输出。

godoc展示ExampleXxx的函数体来示范函数、常量或变量Xxx的使用。接收类型T或*T的方法M的范例命名为ExampleT_M。对给定的函数、常量或变量可能有多个范例，以尾随的_xxx区分，xxx是不以大写字母开头的后缀。

这是范例的例子：

```go
func ExamplePrintln() {
	Println("The output of\nthis example.")
	// Output: The output of
	// this example.
}
```

这是忽略输出顺序的另一个例子：

```go
func ExamplePerm() {
	for _, value := range Perm(4) {
		fmt.Println(value)
	}

	// Unordered output: 4
	// 2
	// 1
	// 3
	// 0
}
```

当其包含单个范例函数，至少一个其它函数、类型、变量或常量声明，没有单元测试、基准测试或模糊测试时，整个测试文件会作为范例展示。

更多信息参阅testing包的文档。

# vcs主题——用GOVCS控制版本控制

“go get”命令能运行类似git的版本控制命令来下载被导入的代码。这个功能对分散的Go包生态系统是极重要的，在其中代码可以从任何服务器导入，但其也是个潜在的安全问题，如果一个恶意的服务器找到导致被调用的版本控制命令运行未预期代码的方法。

为了平衡功能和安全忧虑，“go get”命令默认将只使用git和hg来从公共服务器下载代码。但其将使用任何已知的版本控制系统（bzr、fossil、git、hg、svn）来从私有服务器——定义为匹配GOPRIVATE环境变量（参阅“go help private”）的那些托管的包——下载代码。只允许Git和Mercurial背后的根本原因是这两个系统最关注作为不可信服务器的客户端运行的问题。相比之下，Bazaar、Fossil和Subversion主要被用于可信的、已认证的环境且没有如同受攻击面那样很好地被仔细检查。

版本控制命令限制只应用于当使用直接的版本控制访问来下载代码时。当从代理下载模块时，“go get”改用代理协议，其总是被允许的。默认情况下，“go get”命令为公共包使用Go模块镜像（proxy.golang.org），且只当为私有包或当镜像拒绝为公共包服务（典型地为法律原因）时才退回至版本控制。因此，默认情况下客户端仍然能访问从Bazaar、Fossil或Subversion仓库提供的公共代码，因为那些下载使用Go模块镜像，其使用自定义沙箱承担了运行版本控制命令的安全风险。

GOVCS环境变量能用来为特定的包（由模块或导入路径标记）改变允许的版本控制系统。当在模块感知模式和GOPATH模式下构建包时，GOVCS环境变量都会应用。当使用模块时，模式与模块路径匹配。当使用GOAPTH时，模式与对应于版本控制仓库的根的导入路径匹配。

GOVCS设置的一般形式为pattern:vcslist规则的逗号分隔的列表。pattern是必需匹配一个或多个模块或导入路径的前导元素的glob模式。vcslist是允许的版本控制命令的管道符分隔的列表，或“all”来允许使用任意已知的命令，或“off”来禁止所有命令。注意如果模块匹配vcslist为“off”的模式，其仍可被下载，如果源服务器使用“mod”方案，其指示go命令使用GOPROXY协议下载模块。应用列表中最先匹配的模式，即使后面的模式也可以匹配。

例如，考虑：

```
GOVCS=github.com:git,evil.com:off,*:git|hg
```

以此设置，以github.com/开头的模块或导入路径的代码只可以使用git；evil.com上的路径不可以使用任何版本控制命令，且所有其它路径（*匹配一切）只可以使用git或hg。

特殊的模式“public”和“private”匹配公共的和私有的模块或导入路径。路径是私有的，如果其匹配GOPRIVATE环境变量；否则其是公共的。

如果GOVCS环境变量中没有规则匹配一个特定的模块或导入路径，“go get”命令应用其默认规则，即现可被概括为GOVCS中的表示法“public:git|hg,private:all”。

要允许为任何包任何版本控制系统的无限制的使用，使用：

```
GOVCS=*:all
```

要禁止版本控制的所有使用，使用：

```
GOVCS=*:off
```

“go env -w”命令（参阅“go help env”）可以用来为未来的go命令调用设置GOVCS环境变量。
