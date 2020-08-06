本文更新于2020-04-08。

翻译自Command go官方文档（[https://golang.org/cmd/go/](https://golang.org/cmd/go/)，国内可使用[https://golang.google.cn/cmd/go/](https://golang.google.cn/cmd/go/)），章节段落结构稍作改变，对应的go版本为1.14。

[TOC]

# go

```shell
go <command> [arguments]
```

go是管理Go源代码的工具。

command可为以下命令：

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

# go bug——启动bug报告

```shell
go bug
```

打开默认浏览器并启动新的bug报告，报告包括有用的系统信息。

# go build——编译包及其依赖包

```shell
go build [-o output] [-i] [build flags] [packages]
```

编译指定导入路径的包，以及其依赖包，但不安装结果。

如果构建的参数为单个目录下的.go文件列表时，会将它们当作指定单个包的源文件列表。

当编译包时，忽略以“_test.go”结尾的文件。

当编译单个main包时，生成的可执行文件会被写入到以第一个源文件（如：“go build ed.go rx.go”写入“ed”或“ed.exe”）或源代码目录名（如：“go build unix/sam”写入“sam”或“sam.exe”）命名的输出文件中。当写入Windows下的可执行文件时会添加“.exe”后缀。

当编译多个包或单个非main包时，build编译包但会丢弃目标对象文件，行为只用于类似检查包是否可以构建。

标志：

* -i：同时安装目标的依赖包。
* -o output：强制将结果可执行文件或对象文件写入指定名字的输出文件或目录，而不是在本节最后两个段落所描述的默认行为。如果指定的名字为一个已存在的目录，则所有结果可执行文件将写入到该目录中。

构建标志（build flags）会被build、clean、get、install、list、run、test命令共享：

* -a：强制重新构建已经是最新的包。
* -asmflags '[pattern=]arg list'：传递给每次go tool asm调用的参数。
* -buildmode mode：使用的构建模式，更多信息参阅“go help buildmode”。
* -compiler name：使用的编译器名，即runtime.Complier（gccgo或gc）。
* -gccgoflags '[pattern=]arg list'：传递给每次gccgo编译器/链接器调用的参数。
* -gcflags '[pattern=]arg list'：传递给每次go tool complie调用的参数。
* -installsuffix suffix：在包安装目录名字中使用的后缀，以便使输出文件与默认构建分开。如使用-race标志，安装后缀会自动设置为race，或如果显式设置则在其后追加_race。对-msan也同样。使用需要使用非默认编译标志的-buildmode选项也又类似效果。
* -ldflags '[pattern=]arg list'：传递给每次go tool link调用的参数。
* -linkshared：链接到的之前使用-buildmode=shared生成的共享库。
* -mod mode：使用的模块下载模式：readonly、vendor或mod。更多信息参阅“go help modules”。
* -modcacherw：令保留在模块缓存中的新创建的目录可读写，而不是令它们只读。
* -modfile file：在模块感知模式下，读取（并且可能写入）备用的go.mod文件而不是在模块根目录下的go.mod文件。名字为“go.mod”的文件仍然必须存在用来确定模块根目录，但其不会被访问。当-modfile被指定，备用的go.sum文件也会被使用：其路径从-modfile标志产生，通过去除“.mod”扩展名并追加“.sum”。
* -msan：启用与内存清理程序的互操作，只支持linux/amd64、linux/arm64，并且只能使用clang/llvm作为宿主C编译器。在linux/arm64上，pie构建模式将被使用。
* -n：打印实际需执行的命令，但不运行。
* -p n：可以并行运行的程序——如构建命令或测试二进制文件——的数量。默认为可用的CPU的数量。
* -pkgdir dir：从dir而不是通常的位置安装和加载所有包（pkg）。例如，当使用非标准设置构建时，使用-pkgdir来令在特定的目录生成包。
* -race：启用数据竞态检测，只支持linux/amd64、freebsd/amd64、darwin/amd64、windows/amd64、linux/ppc64le、linux/arm64（只对48位VMA）。
* -tags tag,list：构建期间需满足的构建标记，为逗号分隔的列表。关于构建标记，参阅go/build包文档中关于构建约束的描述。（早期Go版本使用空白分隔的列表，已经被弃用，但仍然能识别。）
* -toolexec 'cmd args'：用来调用如如vet和asm等工具链程序的程序。例如，使用go命令运行“cmd args /path/to/asm <arguments for asm>”，而不是运行asm。
* -trimpath：从目标可执行文件中移除所有文件系统路径。作为文件系统绝对路径的替代，记录下的文件名将以“go”（对标准库），或“模块路径@版本”（当使用模块），或“import路径”（当使用GOPATH）开头。
* -v：打印编译的包名。
* -work：打印临时工作目录并在退出时不删除之。
* -x：打印实际需执行的命令，并运行。

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
* -i：删除相关的已安装的归档文件或二进制文件（由“go install”创建）。
* -modcache：删除整个模块下载缓存，包括指定版本依赖的已解压的源代码。
* -n：打印实际需执行的命令，但不运行。
* -r：递归地应用于所有由导入路径指定的依赖包。
* -testcache：令go build缓存中所有测试结果过期。
* -x：打印实际需执行的命令，并运行。

关于构建标志的更多信息，参阅“go help build”。

关于指定包的更多信息，参阅“go help packages”。

# go doc——查看包或符号的文档

```shell
go doc [-u] [-c] [package|[package.]symbol[.methodOrField]]
```

打印由参数（包、常量、函数、类型、变量、方法、结构体字段）指定的项相关的文档注释，该项后跟该项下的每个第一级项（该项为包即为包级声明，该项为类型即为其方法，依次类推）的一行概要。

doc命令接受0个、1个或2个参数。

如使用0个参数，即运行：

```shell
go doc
```

会打印当前目录下包的包文档。如果包是命令的包（main包），包的导出符号会从展示输出中省略，除非指定了-cmd标志。

当使用一个参数运行时，参数会被视为要展示文档的项的Go语法表示。参数指定的项取决于GOROOT和GOPATH中安装的内容，以及参数的格式。参数的格式如下所示：

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

当使用两个参数运行时，第一个参数必须是完整的包路径（而不仅仅是后缀），第二个参数是一个符号、符号的方法或符号的结构体字段。这与godoc接受的语法类似：

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
go fix [packages]
```

在通过导入路径指定的包上运行Go fix命令。

关于fix的更多信息，参阅“go doc cmd/fix”。关于指定包的更多信息，参阅“go help packages”。

如需使用特定的选项，运行“go tool fix”。

参阅：go fmt，go vet。

# go fmt——gofmt（重新格式化）包代码

```shell
go fmt [-n] [-x] [packages]
```

在导入路径指定的包上运行“gofmt -l -w”命令。将打印被修改的文件名。

关于gofmt的更多信息，参阅“go doc cmd/gofmt”。关于指定包的更多信息，参阅“go help packages”。

标志：

* -mod：标志的值设置要使用那种模块下载模式：readonly还是vendor。更多信息参阅“go help modules”。
* -n：打印实际需执行的命令，但不运行。
* -x：打印实际需执行的命令，并运行。

如需使用特定的选项，直接运行gofmt。

参阅：go fix，go vet。

# go generate——通过执行源代码生成Go文件

```shell
go generate [-run regexp] [-n] [-v] [-x] [build flags] [file.go... | packages]
```

通过在现存文件中描述的指令执行命令。命令可以运行任何进程，但目的是创建或更新Go源文件。

go generate永远不会通过go build、go get、go test等自动运行。必须明确地运行。

go generate扫描文件搜索指令，指令是如下格式的行：

```
//go:generate command argument...
```

(注意：开头没有空白，“//go”中间也没有空白)其中command是要运行的生成器，对应于可在本地运行的可执行文件。其必须在shell PATH中（如gofmt），或为一个完全限定路径（如/usr/you/bin/mytool），或为一个命令别名，如下所述。

为了向人类和机器工具传达，代码是自动生成的，生成的源代码应有一行匹配如下的正则表达式（以Go语法）：

```
^// Code generated .* DO NOT EDIT\.$
```

该行可以出现在文件的任何位置，但通常放在开头附近，以便更容易发现。

注意go generate不解析文件，因此在注释或多行字符串中看起来像指令的行将会被视为指令。

指令的参数是空白分隔的记号，记号可为双引号引起的字符串，在生成器运行时每个记号作为独立的参数传递给生成器。

带引号的字符串使用Go语法，并在执行前进行计算；一个带引号的字符串作为生成器的一个单独的参数。

go generate在运行生成器时会设置几个环境变量：

* $GOARCH：执行时的体系结构（arm、amd64等）。
* $GOOS：执行时的操作系统（linux、windows等）。
* $GOFILE：文件的基本名（base name）。
* $GOLINE：指令在源文件中的行号。
* $GOPACKAGE：包含指令的文件的包名。
* $DOLLAR：一个美元符号。

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

如果任何一个生成器返回了错误退出状态，“go generate”会跳过该包所有接续的处理。

生成器运行在包的源代码目录中。

标志：

* -run=""：如果非空，指定一个正则表达式来选择执行的指令，该指令的完整原始源文本（忽略结尾处的空白和换行）匹配正则表达式。

也接受标准的构建标志，包括-v、-n和-x。

* -n：打印实际需执行的命令，但不运行。
* -v：打印正在处理的包名和文件名。
* -x：打印实际需执行的命令，并运行。

关于构建标志的更多信息，参阅“go help build”。

关于指定包的更多信息，参阅“go help packages”。

# go get——添加依赖包至当前模块并安装之

```shell
go get [-d] [-t] [-u] [-v] [-insecure] [build flags] [packages]
```

get为当前开发的模块解析并添加依赖，然后构建并安装依赖。

第一步是解析需要添加哪些依赖。

对于每个命名的包或包模式，get必须决定相关的模块使用哪个版本。默认情况下，get查找最后的带标签（tag）的发布版本（release），如v0.4.5或v1.2.3。如果没有带标签的发布版本，get查找最后的带标签的预发布版本（pre-release），如v0.0.1-pre1。如果根本没有带标签的版本，get查找最后的已知提交。如果该模块不是已被依赖要求使用较后的版本（例如，比最后的发布版本较新的预发布版本），get将使用它查找到的版本。否则，get将使用当前依赖要求的版本。

默认的版本选择可通过向包名参数添加@version后缀来覆盖，如“go get golang.org/x/text@v0.3.0”所示。版本version也可以是一个前缀：@1表示以v1开头的最后的可用版本。完整的查询语法参阅“go help modules”中“模块查询”标题下的内容。

对于在源代码控制仓库中存储的模块，版本version后缀也可以是一次提交哈希、分支标识符、或其他源代码控制系统已知的语法，如“'go get golang.org/x/text@master”。注意被其它模块查询语法覆盖的分支名不能显式地选择。例如，后缀@v2表示以v2开头的最后的版本，而不是名字为v2的分支。

如果正在解析的模块已经是当前开发模块的依赖，get将更新依赖要求的版本。指定一个比当前依赖要求的版本较早的版本是有效的，并且会降级依赖。版本version后缀为@none表示依赖应该被完全删除，根据需要降级或删除其上的依赖模块。

版本version后缀@latest显式要求使用指定路径命名的模块的最后的次版本（minor）的发布版本。后缀@upgrade类似@latest，但如果当前已被依赖要求使用比最后的发布版本较新的修订版本（revision）或预发布版本，将不会降级模块。后缀@patch要求使用最后的补丁版本（patch）：和当前依赖要求的版本有相同主版本（major）和次版本的最后的发布版本。和@upgrade类似，当已依赖要求使用较新的版本时，@patch不会降级模块。如果指定路径的模块还没有被依赖要求，@upgrade和@patch等同于@latest。

虽然get默认使用包含命名包的模块的最后版本，但其不会使用该模块的依赖模块的最后版本。相反，其偏向于使用该模块要求的特定依赖版本。例如，最后版本的模块A依赖要求使用模块B的v1.2.3，同时模块B的v1.2.4和v1.3.1也可用，那么“go get A”将使用模块A的最后版本但使用模块B的v1.2.3，就如模块A要求的那样。（如果对特定模块有依赖要求冲突，那么“go get”将通过使用最大化依赖版本来解决依赖要求冲突。）

通常，添加一个新依赖可能需要升级现有的依赖以保证正常构建，并且“go get”会自动完成。同样，降级一个依赖可能需要降级其他的依赖，并且“go get”也会自动完成。

第二步是下载（如果需要）、构建、安装指定名字的包。

如果参数命名一个模块但不命名一个包（因为在模块的根目录中没有Go源代码），那么该参数将跳过安装步骤，而不会导致构建失败。例如“go get golang.org/x/perf”仍然会成功，即使该导入路径没有相关的代码。

注意，包模式是允许的，且会在解析模块版本后进行扩展。例如，“go get golang.org/x/perf/cmd/...”添加最后的golang.org/x/perf并然后安装最后版本中的命令（cmd目录下）。

如果没有指定包名参数，“go get”应用于当前目录下的Go包，如果有的话。特别地，“go get -u”和“go get -u=patch”更新该包的所有依赖。如无包名参数也没有-u标志，“go get”等同于“go install”，“go get -d”等同于“go list”。

标志：

* -d：下载构建指定名字的包所需的源代码，包括下载所需的依赖的源代码，但不构建和安装它们。
* -insecure：允许使用如HTTP等不安全的方案，从仓库拉取以及解析自定义域名。谨慎使用。
* -t：同时添加命令行中指定的包构建测试所需的模块。
* -u：同时更新命令行中指定的包的依赖模块，令依赖模块使用较新的次版本的发布版本或补丁版本的发布版本，前提是这些版本可用。继续前面的例子，“go get -u A”会使用模块A的最后版本和模块B的v1.3.1（而不是v1.2.3）。如果模块B依赖要求模块C，但模块C不是构建模块A的依赖（不包括测试），那么模块C将不会被更新。
* -u=patch：（不同于-u）同样更新依赖模块，但不同之处在于默认选择补丁版本的发布版本。继续前面的例子，“go get -u=patch A@latest”将使用模块A的最后版本和模块B的v1.2.4（而不是v1.2.3），而“go get -u=patch A”将使用模块A的补丁版本的发布版本。

当-t和-u一起使用时，将也会更新构建测试所需的模块的依赖模块。

关于模块的更多信息，参阅“go help modules”。

关于指定包的更多信息，参阅“go help packages”。

上文介绍了使用模块来管理源代码和依赖的行为。如果相反，go命令运行在GOPATH模式下，“go help get”的内容会不同，类似地，get的标志和行为的细节也会改变。参阅“go help modules”和“go help gopath-get”。

参阅：go build、go install、go clean、go mod。

# go help——查看帮助信息

```shell
go help <command>
go help <topic>
```

使用“go help \<command>”获取关于命令（command，即 **go** 小节中的命令）的更多信息。使用“go help \<topic>”获取关于主题（topic）的更多信息。

额外的帮助主题为：

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
* modules：模块，模块版本，及更多。
* module-get：模块感知的go get。
* module-auth：使用go.sum的模块校验。
* module-private：非公共模块的模块配置。
* packages：包列表和模式。
* testflag：测试标志。
* testfunc：测试函数。

# go install——编译并安装模块及其依赖

```shell
go install [-i] [build flags] [packages]
```

编译并安装导入路径指定的包。

可执行文件安装在GOBIN环境变量指定的目录中，默认为$GOPATH/bin或如果GOPATH环境变量没有设置时为$HOME/go/bin。$GOROOT中的可执行文件安装在$GOROOT/bin或$GOTOOLDIR，而不是$GOBIN。

当模块感知模式禁用，非可执行文件的其他包将安装在目录$GOPATH/pkg/$GOOS_$GOARCH中。当模块感知模式启用，非可执行文件的其他包将会被构建和缓存，但不会被安装。

标志：

* -i：同时安装指定名字的包的依赖。

关于构建标志的更多信息，参阅“go help build”。关于指定包的更多信息，参阅“go help packages”。

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

标志（其他标志的说明需参阅-f或-m）：

* -complied：将CompiledGoFiles设置为提交给编译器的Go源文件。通常，这意味着其会重复GoFiles列出的文件，然后也会添加通过处理CgoFiles和SwigFiles生成的Go代码。Imports列表包含来自GoFiles和CompiledGoFiles所有导入的并集。
* -deps：不只是遍历指定名字的包，也会遍历它们的依赖。list以深度优先后序遍历的方式遍历它们，因此一个包会在它的所有依赖之后列出。未在命令行中显式列出的包将有一个设置为true的DepOnly字段。
* -e：改变对错误包的处理方式，那些错误包或者不存在或者内容残缺。默认情况下，list命令为每个错误包打印一条错误信息至标准错误输出中，然后在普通的打印中忽略这些错误包。使用-e标志，list命令将不会打印错误信息至标准错误输出中，而是以普通打印的方式处理错误包。错误包将有非空的ImportPath和非nil的Error字段；其他信息可能会也可能不会缺失（置为零值）。
* -export：将Export字段设置为包含指定包最新导出信息的文件名。
* -f：指定list使用的替换格式，使用template包的语法。默认的输出等同于-f '{{.ImportPath}}'。传递给模板的结构体为：

	```go
	type Package struct {
		Dir           string   // 包含包源代码的目录
		ImportPath    string   // 目录中包的导入路径
		ImportComment string   // 包说明中导入注释的路径
		Name          string   // 包名
		Doc           string   // 包文档字符串
		Target        string   // 安装路径
		Shlib         string   // 包含本包的共享库（只在设置-linkshared时使用）
		Goroot        bool     // 本包是否在GOROOT中
		Standard      bool     // 本包是否是Go标准库之一
		Stale         bool     // “go install”会否为本包进行一些处理
		StaleReason   string   // 当Stale==true时的解析原因
		Root          string   // 包含本包的GOROOT或GOPATH目录
		ConflictDir   string   // 本目录在$GOPATH中的同名目录
		BinaryOnly    bool     // 是否仅为二进制包（不再支持）
		ForTest       string   // 包只在该指定名字包的测试下使用
		Export        string   // 包含导出数据的文件（当使用-export时）
		Module        *Module  // 包包含的模块的信息，如果有的话（可以为nil）
		Match         []string // 匹配本包的命令行模式
		DepOnly       bool     // 如为true，则包只是一个依赖，未在命令行中显式列出
		
		// 源文件
		GoFiles         []string // .go源文件（不包括CgoFiles、TestGoFiles、XTestGoFiles中的文件）
		CgoFiles        []string // import "C"的.go源文件
		CompiledGoFiles []string // 提交给编译器的.go文件（当使用-compiled时）
		IgnoredGoFiles  []string // 由于构建限制被忽略的.go源文件
		CFiles          []string // .c源文件
		CXXFiles        []string // .cc/.cxx/.cpp源文件
		MFiles          []string // .m源文件
		HFiles          []string // .h/.hh/.hpp/.hxx源文件
		FFiles          []string // .f/.F/.for/.f90的Fortran源文件
		SFiles          []string // .s源文件
		SwigFiles       []string // .swig文件
		SwigCXXFiles    []string // .swigcxx文件
		SysoFiles       []string // 添加到归档的.syso对象文件
		TestGoFiles     []string // 包中的_test.go文件
		XTestGoFiles    []string // 包外的_test.go文件
		
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
		UseAllFiles   bool     // 是否使用所有文件，从而忽略使用+build指定行和文件名
		Compiler      string   // 在计算目标路径时假定的编译器
		BuildTags     []string // 构建约束，用以匹配+build指定的行
		ReleaseTags   []string // 与当前发布版本兼容的发布版本
		InstallSuffix string   // 安装目录名字中使用的后缀
	}
	```
	
	关于这些字段含义的更多信息，参阅go/build包的Context类型的文档。
	
	Dir、Target、Shlib、Root、ConflictDir和Export中的文件路径均为绝对路径。
	
	默认情况下，GoFiles、CgoFiles等列表中的名字为Dir目录中的文件（亦即，相对于Dir的路径，而不是绝对路径）。当使用-complied和-test标志时添加的生成文件为指向生成的Go源代码的缓存拷贝的绝对路径。即使它们为Go源文件，路径可能不是以“.go”结尾。
* -find：找出指定名字的包但不解析它们的依赖：Imports和Deps列表将会为空。
* -json：包数据以JSON格式打印，而不是使用template包的格式。
* -m：列出模块而不是包。

	当列出模块时，-f标志仍然指定一个应用于Go结构体的格式模板，但现在为一个Module结构体：
	
	```go
	type Module struct {
		Path      string       // 模块路径
		Version   string       // 模块版本
		Versions  []string     // 可获得的模块版本（当使用-versions时）
		Replace   *Module      // 被此模块替代
		Time      *time.Time   // 版本创建的时间
		Update    *Module      // 可获得的更新，如果有的话（当使用-u时）
		Main      bool         // 是否为主模块？
		Indirect  bool         // 该模块是否只为主模块的间接依赖？
		Dir       string       // 该模块保存文件的目录，如果有的话
		GoMod     string       // 该模块go.mod文件的路径，如果有的话
		GoVersion string       // 模块中使用的go版本
		Error     *ModuleError // 加载模块时的错误
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
* -text：不只是报告指定名字的包，也报告它们的测试二进制文件（对带有测试的包），从而将测试二进制文件是怎样构建的准确地传递给源代码分析工具。测试二进制文件的报告的导入路径为包的导入路径添加“.text”后缀，如“math/rand.test”。当构建测试时，有时候需要重新构建测试特定的某些依赖（最常见的是被测试的包本身）。为特定的测试二进制文件重新编译的包的报告的导入路径，会添加一个空格和以括号括起的测试二进制文件的名字，如“math/rand [math/rand.test]”或“regexp [sort.test]”。ForTest也会设置为正在被测试的包名（在此前的例子中为“math/rand”或“sort”）。
* -u：添加关于可用升级的信息。当给定模块的最后版本比当前模块较新时，-u设置Module的Update字段为关于较新模块的信息。Module的String方法通过在当前版本后面格式化以括号括起的较新版本来表示一个可获得的升级。例如，“go list -m -u all”可能打印：
	
	```
	my/main/module
	golang.org/x/text v0.3.0 [v0.4.0] => /tmp/text
	rsc.io/pdf v0.1.1 [v0.1.2]
	```
	
	（对工具来说，“go list -m -u -json all”可能更便于解析。）
* -versions：设置Module的Versions字段为该模块的所有已知版本的列表，根据语义版本进行排序，从最早至最后。该标志也会改变默认的输出格式来显示模块路径，其后接着以空格分隔的版本列表。

关于构建标志的更多信息，参阅“go help build”。

关于指定包的更多信息，参阅“go help packages”。

关于模块的更多信息，参阅“go help modules”。

# go mod——模块维护

```shell
go mod <command> [arguments]
```

command可为以下命令：

* download：下载模块至本地缓存中。
* edit：通过工具或脚本编辑go.mod。
* graph：打印模块依赖要求图。
* init：在当前目录初始化新模块。
* tidy：添加缺少的模块并删除未使用的模块。
* vendor：生成依赖的vendor副本。
* verify：验证依赖有预期的内容。
* why：解释为什么需要该包或模块。

go mod提供对模块操作的访问。

注意对模块的支持内置于所有go命令，不只是“go mod”。例如，依赖的日常添加、删除、升级、降级应该使用“go get”来完成。关于模块功能的概述，参阅“go help modules”。

关于命令的更多信息，参阅“go help mod <command>”。

## go mod download——下载模块至本地缓存中

```shell
go mod download [-x] [-json] [modules]
```

download下载指定名字的模块，可为选择主模块依赖的模块模式，或path@version形式的模块查询。不带参数，download应用于主模块的所有依赖。

go命令将在常规执行期间根据需要自动下载模块。“go mod download”命令主要用于预填充本地缓存或计算Go模块代理的结果。

默认情况下，download不向标准输出写入内容。其可能打印进度信息和错误至标准错误输出。

标志：

* -json：打印一系列JSON对象至标准输出，描述每个下载的模块（或失败），相当于该Go结构体：
	
	```go
	type Module struct {
		Path     string // 模块路径
		Version  string // 模块版本
		Error    string // 加载模块时的错误
		Info     string // 缓存的.info文件绝对路径
		GoMod    string // 缓存的.mod文件绝对路径
		Zip      string // 缓存的.zip文件绝对路径
		Dir      string // 缓存的源文件根目录绝对路径
		Sum      string // 路径、版本的校验和（如go.sum中所示）
		GoModSum string // go.mod的校验和（如go.sum中所示）
	}
	```
* -x：打印实际需执行的命令，并运行。
	
关于模块查询的更多信息，参阅“go help modules”。

## go mod edit——通过工具或脚本编辑go.mod

```shell
go mod edit [editing flags] [go.mod]
```

edit提供一个编辑go.mod的命令行接口，主要提供给工具或脚本使用。它只读取go.mod；不查找涉及模块的信息。默认情况下，edit读写主模块的go.mod文件，但也可以在标志后指定不同的目标文件。

标志：

* -dropexclude=path@version：删除给定模块路径和版本的排除项。
* -dropreplace=old[@v]：删除给定模块路径和版本的替代。如果@v省略，删除该模块不带版本的替代。
* -droprequire=path：删除给定的模块路径依赖要求的模块。该标志主要提供给工具用以理解模块图。用户应该使用“go get path@none”，可令其它go.mod根据需要调整来满足其它模块施加的限制。
* -exclude=path@version：添加给定模块路径和版本的排除项。注意如果排除项已经存在-exclude=path@version是无操作的。
* -fmt：重新格式化go.mod文件，不作其他改变。使用或重写go.mod文件的任何其他修改也意味着这种重新格式化。需要该标志的唯一情形是没有指定其它标志，如“go mod edit -fmt”。
* -go=version：设置期望的Go语言版本。
* -json：以JSON格式打印最终的go.mod，而不是将其写回go.mod。JSON输出对应于这些Go类型：

	```go
	type Module struct {
		Path string
		Version string
	}
	
	type GoMod struct {
		Module  Module
		Go      string
		Require []Require
		Exclude []Module
		Replace []Replace
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
	```
* -module：修改模块路径（go.mod文件的模块行）。
* -print：以其文本格式打印最终的go.mod，而不是将其写回go.mod。
* -replace=old[@v]=new[@v]：添加给定模块路径和版本对的替代。如果old@v中的@v省略，则添加左侧模块不带版本的替代，适用于左侧模块路径的所有版本。如果new@v中的@v省略，新路径应为本地模块根目录，而不是模块路径。注意-replace覆盖old[@v]任何冗余的替代，因此省略@v将删除对特定版本的现有替代。
* -require=path@version：添加给定的模块路径和版本依赖要求的模块。注意-require覆盖该路径任何已存在的依赖要求的模块。该标志主要提供给工具用以理解模块图。用户应该使用“go get path@version”，可令其它go.mod根据需要调整来满足其它模块施加的限制。

-require、-droprequire、-exclude、-dropexclude、-replace、-dropreplace标志可以重复，根据给定的顺序应用修改。

注意这只描述go.mod文件自身，不描述其他间接引用的模块。对于构建可使用的的模块的完整集合，使用“go list -m -json all”。

例如，工具可以通过解析“go mod edit -json”的输出以数据结构体的方式获取go.mod，然后可通过使用-require、-exclude等调用“go mod edit”来作出修改，等等。

## go mod graph——打印模块依赖要求图

```shell
go mod graph
```

以文本形式打印模块依赖要求图（已应用替代）。输出的每行有两个空格分隔的字段：模块和其依赖要求中的一个。每个模块都被标记为path@version形式的字符串，除了主模块，其没有@version后缀。

## go mod init——在当前目录初始化新模块

```shell
go mod init [module]
```

初始化并写入一个新的go.mod至当前目录中，实际上是创建一个以当前目录为根的新模块。文件go.mod必须不存在。如果可能，init会从import注释（参阅“go help importpath”）或从版本控制配置猜测模块路径。要覆盖此猜测，提供模块路径作为参数。

## go mod tidy——添加缺少的模块并删除未使用的模块

```shell
go mod tidy [-v]
```

确保go.mod与模块中的源代码一致。它添加构建当前模块的包和依赖所必须的任何缺少的模块，删除不提供任何有价值的包的未使用的模块。它也会添加任何缺少的条目至go.mod并删除任何不需要的条目。

标志：

* -v：打印被删除的模块的信息至标准错误输出。

## go mod vendor——生成依赖的vendor副本

```shell
go mod vendor [-v]
```

重置主模块的vendor目录，使其包含构建和测试所有主模块的包所需要的所有包。不包括vendor中的包的测试代码。

* -v：打印vendor的模块和包的名字至标准错误输出。

## go mod verify——验证依赖有预期的内容

```shell
go mod verify
```

检查存储在本地下载源代码缓存中的当前模块的依赖，是否自从下载之后未被修改。如果所有模块都未被修改，打印“all modules verified”。否则报告哪个模块已经被修改并令“go mod”以非0状态退出。

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

# go run——编译并运行Go程序

```shell
go run [build flags] [-exec xprog] package [arguments...]
```

编译并运行指定名字的main Go包。通常包是作为一个单独的目录中的.go源文件列表指定的，但也可以是导入路径、文件系统路径、或匹配单独的已知包的模式，例如“go run .”或“go run my/cmd”。

标志：

* -exec：默认情况下，“go run”直接运行编译的二进制文件：“a.out arguments...”。如果给定-exec标志，“go run”使用xprog调用二进制文件：“xprog a.out arguments...”。

	如果-exec标志未给定，GOOS或GOARCH与系统默认值不同，一个名字为go_$GOOS_$GOARCH_exec的程序可在当前查找目录下找到，“go run”使用该程序调用二进制文件，如“go_nacl_386_exec a.out arguments...”。当模拟器或其他执行方法可用时，这允许交叉编译的程序执行。
	
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

“go test”将与名字匹配文件模式“*_test.go”的任何文件一起，重新编译每个包。这些附加的文件可以包含test函数、benchmark函数、example函数。更多信息参阅“go help testfunc”。每个列出的包都会令到执行单独的测试二进制文件。以“_”（包括“_test.go”）或“.”开头的文件会被忽略。

声明带后缀“_test”的包的测试文件将被编译为单独的包，然后与main包的测试二进制文件链接并运行。

go tool将忽略名字为“testdata”的目录，令其可以持有被测试所需的辅助数据。

作为构建测试二进制文件的一部分，go test在包及其测试源文件上运行go vet来发现显而易见的问题。如果go vet发现任何问题，go test报告它们且不运行测试二进制文件。只使用默认go vet检查的高可信的子集。该自己是：“atomic”、“bool”、“buildtags”、“nilfunc”和“printf”。你可以通过“go doc cmd/vet”查看这些及其它vet测试的文档。要禁止运行go vet，使用-vet=off标志（参阅 **testflag主题——测试标志** 小节）。

所有的测试输出和概要行都打印至go命令的标准输出，即使test打印它们至它自己的标准错误输出。（go命令的标准错误输出被保留为打印构建测试的错误。）

go test以两种不同的模式运行：

第一种，称为本地目录模式，发生在当go test不使用包参数调用时（例如，“go test”或“go test -v”）。在这种模式下，go test编译在当前目录下找到的包源文件和测试文件，然后运行生成的测试二进制文件。在这种模式下，缓存（在下面讨论）被禁用。包测试完成后，go test打印一行概要行展示测试状态（“ok”或“FAIL”）、包名和消耗的时间。

第二种，称为包列表模式，发生在当go test使用显式的包参数调用时（例如“go test math”、“go test ./...”甚至“go test .”）。在这种模式下，go test编译并测试每个在命令行列出的包。如果一个包测试通过，go test只打印最终的“ok”概要行。如果一个包测试失败，go test打印完整的测试输出。如果使用-bench或-v标志调用（参阅 **testflag主题——测试标志** 小节），即使通过了包测试，go test也打印完整的输出，以便展示要求的基准测试结果或详细日志。对所有列出的包的包测试完成后，且它们的输出被打印，如果任一个包测试失败，go test打印最终的“FAIL”状态。

只在包列表模式，go test缓存成功的包测试结果来避免不必要的测试重复运行。当测试的结果可以从缓存恢复时，go test将重新展示之前的输出而不是再次运行测试二进制文件。当这种情况发生时，go test在概要行中打印“(cached)”取代消耗的时间。

缓存匹配的规则为，运行只涉及相同的测试二进制文件，且命令行中的标志完全来自一个受限的“可缓存”测试标志集合，定义为-cpu、-list、-parallel、-run、-short和-v。如果go test的运行有任何不在这个集合中的测试或非测试标志，结果是不被缓存的。要禁用测试缓存，使用可缓存标志以外的测试标志或参数。显式禁用测试缓存的惯用方式为使用-count=1。在包源代码根目录（通常为$GOPATH）内打开文件或查询环境变量的测试只匹配将来的文件和环境变量未改变的测试运行。缓存的测试结果被视为立即执行的，因此一个成功的包测试结果将被缓存且重用时忽略-timeout设置。

除了构建标志，“go test”自身处理的标志还有：

* -args：传递命令行中余下的参数（所有在-args后面的参数）至测试二进制文件，不解析且不修改。因为该标志使用命令行中余下的参数，包列表（如果有）必须出现在该标志之前。
* -c：编译测试二进制文件为pkg.test但不运行之（pkg是包的导入路径的最后的元素）。文件名可以被-o标识修改。
* -exec xprog：使用xprog运行测试二进制文件。其行为与“go run”相同。详细信息参阅“go help run”。
* -i：安装测试的依赖包。不运行测试。
* -json：转换测试输出为适合自动化执行的JSON格式。关于编码的详细信息参阅“go doc test2json”。
* -o file：编译测试二进制文件至指定名字的文件。测试仍会运行（除非指定-c或-i）。

测试二进制文件也接受控制测试执行的标志；这些参数也可被“go test”使用。详细信息参阅“go help testflag”。

关于构建标志的更多信息，参阅“go help build”。关于指定包的更多信息，参阅“go help packages”。

参阅：go build、go vet。

# go tool——运行指定的go tool

```shell
go tool [-n] command [args...]
```

运行参数指定的go tool命令。不带参数则打印已知的工具列表。

标志：

* -n：打印将会执行的命令但不执行之。

关于每个tool命令的更多信息，参阅“go doc cmd/<command>”。

# go version——打印Go版本

```shell
go version [-m] [-v] [file ...]
```

打印Go可执行文件的构建信息。

go version报告用来构建每个指定名字的可执行文件的Go版本。

如果命令行中没有指定文件，go version打印其自身的版本信息。

标志：

* -m：当可以获得的时候，令go version打印每个可执行文件的内嵌的模块版本信息。在输出中，模块信息包含跟在版本行之后的多行，每行由前导制表符缩进。
* -v：如果指定一个目录，go version递归地遍历该目录，查找可以识别的Go二进制文件并报告它们的版本。默认情况下，go version不报告在目录扫描期间发现的无法识别的文件。-v标志令其报告无法识别的文件。

参阅：go doc runtime/debug.BuildInfo。

# go vet——报告包中有可能的错误

```shell
go vet [-n] [-x] [-vettool prog] [build flags] [vet flags] [packages]
```

在通过导入路径指定名字的包上运行go vet命令。

关于vet和其标志的更多信息，参阅“go doc cmd/vet”。关于指定包的更多信息，参阅“go help packages”。关于检查程序及其标志的列表，参阅“go tool vet help”。关于诸如“printf”的特定检查程序的详细信息，参阅“go tool vet help printf”。

标志：

* -n：打印将会执行的命令但不执行之。
* -vettool=prog：选择带有备选或附加检查的不同的分析工具。例如，可以使用这些命令来构建和运行“shadow”分析器：

	```
	go install golang.org/x/tools/go/analysis/passes/shadow/cmd/shadow
	go vet -vettool=$(which shadow)
	```
* -x：打印将会执行的命令并执行之。

go vet支持的构建标志是那些控制包解析和执行的标志，如-n、-x、-v、-tags和-toolexec。关于这些标志的更多信息，参阅“go help build”。

参阅：go fmg、go fix。

# buildmode主题——构建模式

“go build”和“go install”命令使用-buildmode参数，用来表示要构建哪一种对象文件。当前支持的值为：

（译注：下述构建模式如无特别说明，均可列出多个包同时构建。）

* -buildmode=archive：构建列出的非main包生成.a文件。main包被忽略。
* -buildmode=c-archive：构建列出的main包，加上其导入的所有包，生成C归档文件。可调用的符号只为那些使用cgo的// export comment导出的函数。要求只能列出一个main包。
* -buildmode=c-shared：构建列出的main包，加上其导入的所有包，生成C共享库。可调用的符号只为那些使用cgo的// export comment导出的函数。要求只能列出一个main包。
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

GODEBUG环境变量可以启用打印关于缓存状态的调试信息：

* GODEBUG=gocacheverify=1：令go命令绕过任何缓存条目的使用，取而代之重新构建所有内容并检查结果是否与现有的缓存条目匹配。
* GODEBUG=gocachehash=1：令go命令打印为其使用来构建缓存查找键的所有内容哈希值的输入。输出是冗长的但能对调试缓存有用。
* GODEBUG=gocachetest=1：令go命令打印其关于是否重用缓存测试结果的决策的详细信息。

# environment主题——环境变量

go命令及其调用的工具查询环境变量以进行配置。如果环境变量未设置，go命令使用一个合理的默认设置。要查看变量<NAME>的有效设置，运行“go env <NAME>”。要修改默认设置，运行“go env -w <NAME>=<VALUE>”。使用“go env -w”修改的默认值记录在存储在每个用户一个的配置目录中的Go环境变量配置文件中，该目录如同os.UserConfigDir所报告的。配置文件的位置可以通过设置环境变量GOENV修改，且“go env GOENV”打印有效的位置，但“go env -w”不能修改默认位置。详细信息参阅“go help env”。

通用的环境变量：

* GCCGO：为“go build -compiler=gccgo”运行的gccgo命令。
* GOARCH：要为之编译代码的体系结构或处理器。例如amd64、386、arm、ppc64。
* GOBIN：“go install”安装命令使用的目录。
* GOCACHE：go命令存储缓存信息用以在将来的构建中重用的目录。
* GODEBUG：启用多种调试功能。详细信息参阅“go doc runtime”。
* GOENV：Go环境变量配置文件位置。不能使用“go env -w”设置。
* GOFLAGS：一个空白分隔的-flag=value设置列表，当给定的标志被当前命令已知时，会被默认应用于go命令。每个条目必须是单独的标志。因为条目是空白分隔的，标志值必须不包含空白。在命令行列出的标志在这个列表之后应用并因此覆盖之。
* GOINSECURE：模块路径前缀的glob模式（以Go的path.Match的语法）的逗号分隔的列表，其应总是以不安全的方式拉取。只应用于被直接拉取的依赖。
* GONOPROXY：见GOPRIVATE。
* GONOSUMDB：见GOPRIVATE。
* GOOS：要为之编译代码的操作系统。例如linux、darwin、windows、netbsd。
* GOPATH：更新详细信息参阅：“go help gopath”。
* GOPRIVATE：模块路径前缀的glob模式（以Go的path.Match的语法）的逗号分隔的列表，其应该总是直接拉取或不应该与校验和数据库比较。参阅“go help module-private”。
* GOPROXY：Go模块代理URL。参阅“go help modules”。
* GOROOT：go树的根。
* GOSUMDB：使用的校验和数据库的名字，及可选地其公钥和URL。参阅“go help module-auth”。
* GOTMPDIR：go命令写入临时源文件、包和二进制文件的目录。

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

* GO386：对GOARCH=386，浮点指令集。有效值为387、sse2。
* GOARM：对GOARCH=arm，要为之编译的ARM体系结构。有效值为5、6、7。
* GOMIPS：对GOARCH=mips{,le}，是否使用浮点指令集。有效值为hardfloat（默认）、softfloat。
* GOMIPS64：对GOARCH=mips64{,le}，是否使用浮点指令集。有效值为hardfloat（默认）、softfloat。
* GOWASM：对GOARCH=wasm，要使用的实验性WebAssembly特性的逗号分隔的列表。有效值为satconv、signext。

特殊用途的环境变量：

* GCCGOTOOLDIR：如果设置，表示在哪可以找到gccgo工具，例如cgo。默认值基于gccgo是怎么配置的。
* GIT_ALLOW_PROTOCOL：由Git定义。允许与git fetch/clone一起使用的冒号分隔的方案（scheme）列表。如果设置，任何未明确提及的方案会被“go get”认为是不安全的。因为该变量是被Git定义，默认值不可以使用“go env -w”设置。
* GO_EXTLINK_ENABLED：当与使用cgo的代码一起使用-linkmode=auto时，链接器是否应该使用外部链接模式。设为0禁用外部链接模式，1为启用之。
* GOROOT_FINAL：已安装的Go树的根，用在当其安装在其构建位置以外的地方时。堆栈跟踪中的文件名从GOROOT重写为GOROOT_FINAL。

从“go env”可获得的附加信息但不是从环境变量读取的：

* GOEXE：可执行文件名后缀（在Windows上为“.exe”，在其它系统上为“”）。
* GOGCCFLAGS：提供给CC命令的空白分隔的参数列表。
* GOHOSTARCH：Go工具链二进制文件的体系结构（GOARCH）。
* GOHOSTOS：Go工具链二进制文件的操作系统（GOOS）。
* GOMOD：主模块的go.mod的绝对路径。如果模块感知模式启用，但没有go.mod，GOMOD将为os.DevNull（在类Unix系统上为“/dev/null”，在Windows上为“NUL”）。如果模块感知模式禁用，GOMOD将为空字符串。
* GOTOOLDIR：go工具（compile、cover、doc等等）安装的目录。

# filetype主题——文件类型

go命令检查每个目录中限定的一组文件的内容。其根据文件名的扩展名识别哪些文件要检查。这些扩展名为：

* .c/.h：C源文件。如果包使用cgo或SWIG，这些文件将使用操作系统本地编译器（通常为gcc）编译；否则它们将触发一个错误。
* .cc/.cpp/.cxx/.hh/.hpp/.hxx：C++源文件。只在使用cgo或SWIG时有用，并总是使用操作系统本地编译器编译。
* .go：Go源文件。
* .m：Objective-C源文件。只在使用cgo时有用，并总是使用操作系统本地编译器编译。
* .s/.S：汇编源文件。如果包使用cgo或SWIG，它们将使用操作系统本地汇编器（通常为gcc（sic））汇编；否则它们将使用Go汇编器汇编。
* .swig/.swigcxx：SWIG定义文件。
* .syso：系统对象文件。

这些类型除.syso之外的每个类型的文件都可能包含构建约束，但go命令在文件中不是空行或//-style行注释的第一项处停止扫描构建约束。更多详细信息参阅go/build包文档。

# go.mod主题——go.mod文件

模块版本是由源文件树定义的，在其根目录中有一个go.mod文件。当go命令运行时，它查找当前目录并然后查找相继的父目录来找出go.mod，go.mod标记主（当前）模块的根。

go.mod文件自身是面向行的，带有//注释但没有/**/注释。每行包含单个指令，由一个动词后跟参数组成。例如：

```
module my/thing
go 1.12
require other/thing v1.0.2
require new/thing/v2 v2.3.4
exclude old/thing v1.2.3
replace bad/thing v1.4.5 => good/thing v1.4.5
```

动词为：

* module：定义模块路径。
* go：设置期望的语言版本。
* require：依赖要求一个给定版本或之后的特定模块。
* exclude：从使用中排除特定模块版本。
* replace：以一个不同的模块版本替代另一个模块版本。

exclude和replace只应用在主模块的go.mod，并在依赖中会被忽略。详细信息参阅[https://research.swtch.com/vgo-mvs](https://research.swtch.com/vgo-mvs)。

开头的动词可以被从相邻的行中分解出来以创建一个块，类似在Go import导入中：

```
require (
	new/thing v2.3.4
	old/thing v1.2.3
)
```

go.mod文件被设计为既可以被直接编辑也可以容易地被工具更新。“go mod edit”命令可以被用来从程序和工具解析和编辑go.mod文件。参阅“go help mod edit”。

go命令在每次其使用模块图时自动更新go.mod，以保证go.mod总是准确地反映实际情况并正确地格式化。例如，考虑该go.mod文件：

```
module M

require (
	A v1
	B v1.0.0
	C v1.0.0
	D v1.2.3
	E dev
)

exclude D v1.2.3
```

更新将重写不规范的版本标识符为语义版本（semver）格式，因此A的v1变成v1.0.0，E的dev变成dev分支上最后提交的伪版本号，可能为v0.0.0-20180523231146-b3f5c0f6e5f1。

更新将修改依赖要求规则以遵守排除规则，因此在已经排除的D v1.2.3上的依赖要求被更新为使用D的下一个可获得的版本号，可能为D v1.2.4或D v1.3.0。

更新将删除冗余的或误导性的依赖要求。例如，如果A v1.0.0自身依赖要求B v1.2.0和C v1.0.0，那么go.mod的B v1.0.0依赖要求是误导性的（被A对v1.2.0的需求所取代），且其C v1.0.0的依赖要求是冗余的（被A对相同版本的需求所暗指），因此它们都会被删除。如果模块M包含从B或C直接导入包的包，那么依赖要求将被保留但更新为使用的实际版本号。

最后，更新将以规范的格式重新格式化go.mod，以使将来机械般的修改也将导致最小的差异。

因为模块图定义了import语句的含义，任何加载包的命令也会使用并因此更新go.mod，包括go build、go get、go install、go list、go test、go mod graph、go mod tidy、go mod why。

由go指令设置的期望的语言版本决定了当编译模块时哪些语言特性可获得。在该版本可用的语言特性将可以使用。在早期版本移除的或在后续版本添加的语言特性将不可获得。注意语言版本不影响构建标志，它是由使用的Go发布版本决定的。

# gopath主题——GOPATH环境变量

GOPATH用于解析import语句，其被go/build包实现并在go/build包中记录文档。

GOPATH环境变量列出查找Go代码的位置。在Unix上，值是冒号分隔的字符串。在Windows上，值是分号分隔的字符串。在Plan 9上，值是一个列表。

如果环境变量未设置，GOPATH默认为在用户的主目录中名字为“go”的子目录（在Unix上为$HOME/go，在Windows上为%USERPROFILE%\go），除非该目录包含Go发行版。运行“go env GOPATH”来查看当前的GOPATH。

参阅[https://golang.org/wiki/SettingGOPATH](https://golang.org/wiki/SettingGOPATH)来设置自定义GOPATH。

GOPATH中列出的每个目录必须有规定的结构：

src目录包含源代码。src下的路径决定import路径或可执行文件名。

pkg目录包含安装的包对象文件。如同在Go树中，每个目标操作系统和体系结构对都有其自己的pkg子目录（pkg/GOOS_GOARCH）。

如果DIR是GOPATH中列出的一个目录，源代码在DIR/src/foo/bar中的包可以被导入为“foo/bar”并且其编译形式的文件会被安装到“DIR/pkg/GOOS_GOARCH/foo/bar.a”。

bin目录包含编译的命令。每个命令以其源代码目录命名，但只取最后一个元素，不是全部的路径。亦即，源代码在DIR/src/foo/quux中的命令被安装至DIR/bin/quux，而不是DIR/bin/foo/quux。“foo/”前缀被剥掉，因此你可以添加DIR/bin至你的PATH中来访问安装的命令。如果GOBIN环境变量被设置，命令会被安装至其命名的目录而不是DIR/bin。GOBIN必须为绝对路径。

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
go get [-d] [-f] [-t] [-u] [-v] [-fix] [-insecure] [build flags] [packages]
```

“go get”命令根据go命令运行在模块感知模式还是遗留的GOPATH模式来改变行为。这里的帮助文本，即使在模块感知模式也可作为“go help gopath-get”访问，描述“go get”在遗留的GOPATH模式下的操作。

get下载由导入路径指定名字的包，以及它们的依赖。然后安装指定名字的包，类似“go install”。

标志：

* -d：在下载包之后停止；亦即，其令get不安装包。
* -f：只在-u被设置时有效，强制get -u不验证每个包都是从其导入路径隐含的源代码控制仓库检出。如果源代码是源的本地fork分支这将会有用。
* -fix：在解析依赖和构建代码之前在已下载的包上运行fix工具。
* -insecure：允许使用如HTTP等不安全的方案，从仓库拉取以及解析自定义域名。谨慎使用。
* -t：同时下载指定的包构建测试所需的包。
* -u：使用网络来更新指定名字的包及其依赖。默认情况下，get使用网络来检出缺少的包但不会使用它来查找已存在包的更新。
* -v：启用详细的进度和调试输出。

get也接受构建标志来控制安装。参阅“go help build”。

当检出一个新包时，get创建目标目录GOPATH/src/<import-path>。如果GOPATH包含多个入口，get使用第一个。更多详细信息参阅：“go help gopath”。

当检出或更新包时，get查找匹配Go本地安装版本的分支（branch）或标签（tag）。最重要的规则是，如果本地安装正在运行版本“go1”，get查找名字为“go1”的分支或标签。如果没有这样的版本存在，其检索包的默认分支。

当go get检出或更新一个Git仓库，其也会更新被该仓库引用的任何git子模块。

get从不检出或更新在vendor目录中存储的代码。

关于指定包的更多信息，参阅“go help packages”。

关于“go get”如何查找下载的源代码的更多信息，参阅“go help importpath”。

这段文字描述当使用GOPATH来管理源代码和依赖时get的行为。如果相反go命令运行在模块感知模式，“go help get”的内容会不同，类似地，get的标志和行为的细节也会改变。参阅“go help modules”和“go help module-get”。

参阅：go build、go install、go clean。

# goproxy主题——模块代理协议

Go模块代理是能响应特定格式URL的GET请求的任何web服务器。请求没有查询（query）参数，因此即使从固定文件系统（包括file://URL）提供服务的站点也能成为模块代理。

发送至Go模块代理的GET请求为：

* GET $GOPROXY/<module>/@v/list：返回给定模块的所有已知版本的列表，一行一个。
* GET $GOPROXY/<module>/@v/<version>.info：返回关于给定模块给定版本的JSON格式的元数据。
* GET $GOPROXY/<module>/@v/<version>.mod：返回给定模块给定版本的go.mod文件。
* GET $GOPROXY/<module>/@v/<version>.zip：返回给定模块给定版本的zip归档文件。
* GET $GOPROXY/<module>/@latest：以与<module>/@v/<version>.info相同的格式返回关于给定模块最后的已知版本的JSON格式的元数据。最后的版本应该为如果<module>/@v/list为空或者没有列出的版本是合适的时候go命令可以使用的模块版本。<module>/@latest是可选的且可能不被模块代理实现。

当解析模块的最后版本时，go命令将请求<module>/@v/list，然后，如果没有发现合适的版本，则请求<module>/@latest。go命令偏向于，按顺序为：语义上最高的发布版本，语义上最高的预发布版本，按时间顺序最近的伪版本。在Go 1.12及更早，go命令认为<module>/@v/list中的伪版本为预发布版本，但从Go 1.13起不再这样。

为了避免从大小写敏感的文件系统提供服务时的问题，<module>和<version>元素进行大小写编码，以一个感叹号后跟对应的小写字母替换每个大写字母：github.com/Azure编码为github.com/!azure。

给定模块的JSON格式的元数据对应此Go数据结构，其可能在将来扩展：

```go
type Info struct {
    Version string    // 版本字符串
    Time    time.Time // 提交（commit）时间
}
```

给定模块给定版本的zip归档文件是标准的zip文件，其包含对应于模块的源代码和相关的文件的文件树。归档文件使用斜杠分隔的路径，归档文件中每个文件路径必须以<module>@<version>/开始，其中module和version是直接替换的，不进行大小写编码。模块文件树的根对应于归档文件中的<module>@<version>/前缀。

即使从版本控制系统直接下载时，go命令也合成显式的info、mod和zip文件，并将它们存储在其本地缓存$GOPATH/pkg/mod/cache/download，如同从代理直接下载它们一样。缓存的布局与代理URL空间相同，因此在$GOPATH/pkg/mod/cache/download提供服务（或拷贝其至）https://example.com/proxy能令其它用户以GOPROXY=https://example.com/proxy访问那些缓存的模块版本。

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

要声明代码的位置，`repository.vcs/path`格式的导入路径指定给出的仓库，可带或不带.vcs后缀（译注：见支持的版本控制系统），使用指定名字的版本控制系统，然后是该仓库中的路径。支持的版本控制系统为：

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

如果导入路径不是已知的代码托管站点且也缺少版本控制限定符，go工具尝试通过https/http拉取导入路径并在文档的HTML <head>中查找<meta>标签。

meta标签有如下格式：

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
http://example.org/pkg/foo?go-get=1  （备选的，只与-insecure一起）
```

（译注：假设该页面的请求为https://example.org/exproj?go-get=1）如果该页面包含meta标签`<meta name="go-import" content="example.org git https://code.org/r/p/exproj">`，go工具将验证https://example.org/?go-get=1包含相同的meta标签，并然后git clone https://code.org/r/p/exproj进GOPATH/src/example.org。

当使用GOPATH时，下载的包被写入至在GOPATH环境变量中列出的第一个目录。（参阅“go help gopath-get”和“go help gopath”。）

当使用模块时，下载的包存储在模块缓存中。（参阅“go help module-get”和“go help goproxy”。）

当使用模块时，go-import meta标签一个额外的变量会被识别，且胜于那些列出版本控制系统的变量。该变量使用“mod”作为内容值中的vcs，如同：

```html
<meta name="go-import" content="example.org mod https://code.org/moduleproxy">
```

该标签表示用以example.org开头的路径从URL为https://code.org/moduleproxy上可用的模块代理拉取模块。关于代理协议的详细信息参阅“go help goproxy”。

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

# modules主题——模块，模块版本，及更多

模块是关联的Go包的集合。模块是源代码交换和版本控制的单元。go命令对使用模块工作有直接支持，包括记录和解决对其它模块的依赖。模块替代旧的基于GOPATH的方法来指定在给定的构建中使用哪些源文件。

## 模块支持

go命令包含对Go模块的支持。每当go.mod文件在当前目录或当前目录某个父目录中找到时，模块感知模式默认是激活的。

利用模块支持最快的方式是检出你的仓库，在那创建go.mod文件（在下一节中描述），在该文件树中运行go命令。

对于更多细粒度的控制，go命令继续遵守临时环境变量GO111MODULE，其可以被设置为三个字符串值之一：off、on或auto（默认值）。如果GO111MODULE=on，那么go命令要求使用模块，从不参考GOPATH。我们称之为命令是模块感知的或运行在“模块感知模式”。如果GO111MODULE=off，那么go命令从不使用模块支持。相反其搜索vendor目录和GOPATH来查找依赖；我们现在称之为“GOPATH模式”。如果GO111MODULE=auto或未设置，那么go命令基于当前目录来启用或禁用模块支持。只在当前目录包含go.mod文件或在包含go.mod文件的目录之下时，模块支持才启用。

在模块感知模式，GOPATH不再在构建期间定义导入的含义，但其仍然存储下载的依赖（在GOPATH/pkg/mod中）和安装的命令（在GOPATH/bin中，除非GOBIN被设置）。

## 定义模块

模块被在树的根目录中有go.mod文件的Go源文件树所定义。包含go.mod文件的目录被称为模块根。典型地，模块根也对应于源代码仓库根（但一般来说不需要）。模块是在模块根及其子目录中的所有Go包的集合，但不包括有其自己的go.mod文件的子树。

“模块路径”是对应于模块根的导入路径前缀。go.mod文件定义模块路径，并通过给出模块路径和版本，来列出在构建期间解析导入时应该使用的其它模块的特定版本。

例如，此go.mod文件声明了包含它的目录是路径为example.com/m的模块的根，且其也声明了模块依赖golang.org/x/text和gopkg.in/yaml.v2的特定版本：

```go
module example.com/m

require (
	golang.org/x/text v0.3.0
	gopkg.in/yaml.v2 v2.1.0
)
```

go.mod文件也可以指定只在直接构建模块时应用的替代和排除的版本；当模块合并至更大的构建时它们会被忽略。更多关于go.mod文件，参阅“go help go.mod”。

要启动一个新模块，只需要在模块的目录树的根创建go.mod文件，其中只包含模块声明。“go mod init”命令可以用来做此事：

```shell
go mod init example.com/m
```

在一个已使用如godep、glide、dep等现存的依赖管理工具的项目中，“go mod init”也会添加匹配已有配置的require语句。

一旦go.mod文件存在，不需要额外的步骤：诸如“go build”、“go test”甚至“go list”等go命令会根据需要自动地添加新的依赖来满足导入。

## 主模块和构建列表

“主模块”是包含go命令运行的目录的模块。go命令通过在当前目录、或当前目录的父目录、或父目录的父目录等等，搜索go.mod来查找模块根。

主模块的go.mod文件通过require、replace和exclude语句，定义了被go命令可用的包的精确集合。通过require语句后找到的依赖模块也影响该包集合的定义，但只通过其go.mod文件的require语句：在依赖模块中的任何replace和exclude语句都会被忽略。因此replace和exclude语句允许主模块完全控制其自身构建，不会也服从于依赖的完全控制。

提供要构建包的模块集合被称为“构建列表”。构建列表最初只包含主模块。然后go命令将被已在列表中的模块依赖要求的精确模块版本添加到列表中，递归地操作，直到没有可添加至列表中。如果特定模块的多个版本被添加至列表中，那么最后只有最后的版本（基于语义版本排序）保留以用于构建中。

“go list”命令提供关于主模块和构建列表的信息。例如：

```shell
go list -m              # 打印主模块的路径
go list -m -f={{.Dir}}  # 打印主模块的根目录
go list -m all          # 打印构建列表
```

## 维护模块依赖要求

go.mod文件对程序员和工具都意味着可读的和可编辑的。go命令自身自动地更新go.mod文件来维护标准的格式和require语句的准确性。

任何发现陌生import的go命令会查找包含该import的模块并自动地添加该模块的最后版本至go.mod。因此，在大多数情况下，添加import至源代码并运行“go build”、“go test”甚至“go list”足以：作为分析包的一部分，go命令将发现并解析该import并更新go.mod文件。

即使是在只考虑模块中单个包时，go命令也可以决定一个模块依赖要求是缺少的且必须被添加。另一方面，决定一个模块依赖要求不再需要且可以被删除要求得到模块中所有包的全貌，遍及所有可能的构建配置（体系结构、操作系统、构建标志，等等）。“go mod tidy”命令生成该全貌并然后添加任何缺少的模块依赖要求及删除不需要的。

作为维护go.mod中require语句的一部分，go命令跟踪哪些提供被当前模块直接导入的包，及哪些提供被其它模块依赖间接使用的包。在go.mod文件中，只被间接使用所需的依赖要求以“// indirect”注释标记。一旦被其它直接依赖要求隐含，间接依赖要求会被自动从go.mod文件中删除。间接依赖要求只出现在当使用无法说明其自身某些依赖的那些模块，或当显式地升级模块的依赖至领先其自身声明的依赖要求。

因为这样的自动维护，go.mod中的信息是构建的最新的、可读的描述。

“go get”命令更新go.mod来修改在构建中使用的模块版本。一个模块的升级可能隐含升级其它的，并同样地一个模块的降级可能隐含地降级其它的。“go get”命令也会进行这些隐含的修改。如果go.mod被直接修改，类似“go build”或“go list”等命令将假设升级是有意的，并自动地进行所有隐含的升级，并更新go.mod来反映之。

“go mod”命令提供其他用于维护和理解模块及go.mod文件的功能。参阅“go help mod”。

-mod构建标志提供对更新和使用go.mod的额外控制。

* 如果以-mod=mod调用，即使存在vendor目录go命令也从模块缓存加载模块。
* 如果以-mod=readonly调用，go命令不允许go.mod如上所述的隐式自动更新。相反，当需要对go.mod的任何修改时，其会失败。该设置用来检查go.mod不需要更新是最有用的，例如在持续集成和测试系统中。即使使用-mod=readonly，“go get”命令仍然允许更新go.mod，且“go mod”命令不使用-mod标志（或任何其它构建标志）。
* 如果以-mod=vendor调用，go命令假设vendor目录包含依赖的正确拷贝并忽略在go.mod中的依赖描述。

## 伪版本

go.mod文件和go命令更通常地使用语义版本作为标准格式来描述模块版本，因此版本可以被比较来决定哪个应被认为比另一个更早或更晚。类似v1.2.3的模块版本通过在底层的源代码仓库打上修订标签来引入。未打修订标签的可使用如v0.0.0-yyyymmddhhmmss-abcdefabcdef的“伪版本”来引用，其中的时间是UTC的提交时间，且最后的后缀是提交哈希的前缀。时间部分确保两个伪版本可以被比较来决定哪个发生得更晚，提交哈希标记底层的提交，且前缀（在这个例子中为v0.0.0-）从提交图中这次提交前最近的打标签的版本中得到。

有三种伪版本格式：

* vX.0.0-yyyymmddhhmmss-abcdefabcdef：用于当在目标提交之前没有带合适主版本的更早版本的提交。（这是原来唯一的格式，因此一些较旧的go.mod文件即使对遵守标签规则的提交也使用这种格式。）
* vX.Y.Z-pre.0.yyyymmddhhmmss-abcdefabcdef：用于当在目标提交之前最近的带版本的提交为vX.Y.Z-pre时。
* vX.Y.(Z+1)-0.yyyymmddhhmmss-abcdefabcdef：用于当在目标提交之前最近的带版本的提交为vX.Y.Z时。

伪版本从不需手工输入：go命令会接受提交哈希值并自动地翻译其为伪版本（或如果可用的话为打标签的版本）。该转换是模块查询的一个例子（译注：参阅 **模块查询** 小节）。

## 模块查询

在命令行中和主模块的go.mod文件中go命令都接受“模块查询”来替代模块版本。（在评估主模块的go.mod文件中找到的查询之后，go命令更新文件以用其结果代替查询）。

完全指定的语义版本，如“v1.2.3”，评估为该指定的版本。

语义版本前缀，如“v1”或“v1.2”，评估为带该前缀的最后可用的打标签的版本。

语义版本比较，如“<v1.2.3”或“>=v1.5.6”，评估为最接近比较目标的可用的打标签的版本（对<和<=为最后的版本，对>和>=为最早的版本）。

字符串“latest”匹配最后的可用的打标签的版本，否则的话为底层源代码仓库的最后的未打标签的修订。

字符串“upgrade”类似“latest”，但如果模块当前依赖要求比“latest”会选择的版本更后的版本（例如，较新的预发布版本），“upgrade”将反而选择较后的版本。

字符串“patch”匹配与当前依赖要求的版本有相同的主、次版本号的模块的最后的可用的打标签的版本。如果当前没有依赖要求版本，“patch”等同于“latest”。

底层源代码仓库的修订标识符，例如提交哈希前缀、修订标签、分支名，将选择该特定的代码修订。如果修订标识符还用于语义版本打标签，则查询评估为该语义版本。否则查询评估为提交的伪版本。注意带有被其它查询语法匹配的名字的分支和标签不能以这种方式被选择。例如，查询“v2”表示以“v2”开头的最后的版本，不是名字为“v2”的分支。

查询偏向于发布版本而非预发布版本。例如，“<v1.2.3”会偏向于返回“v1.2.2”而不是“v1.2.3-pre1”，即使“v1.2.3-pre1”更靠近比较目标。

被主模块的go.mod中的exclude语句禁止的模块版本被认为是不可用的且不会被查询返回。

例如，这些命令全是有效的：

```shell
go get github.com/gorilla/mux@latest    # 相同（@latest是“go get”的默认值）
go get github.com/gorilla/mux@v1.6.2    # 记录为v1.6.2
go get github.com/gorilla/mux@e3702bed2 # 记录为v1.6.2
go get github.com/gorilla/mux@c856192   # 记录为v0.0.0-20180517173623-c85619274f5d
go get github.com/gorilla/mux@master    # 记录master的当前含义
```

## 模块兼容性和语义版本控制

go命令要求模块使用语义版本并期望版本准确描述兼容性：其假设v1.5.4是对v1.5.3、v1.4.0甚至v1.0.0后向兼容的替代。更通常地go命令期望包遵守“导入兼容性规则”，表述为：

“如果旧的包和新的包有相同的导入路径，新的包必须和旧的包后向兼容。”

因为go命令假设了导入兼容性规则，模块定义只能设置其依赖之一的最低依赖要求版本：其不能设置最高的或排除选择的版本。不过，导入兼容性规则不是保证：可能v1.5.4是有误的且不是v1.5.3后向兼容的替代。因为如此，go命令从不对未要求的模块从较旧的版本升级至较新的版本。

在语义版本控制中，修改主版本号表示缺少与较早版本的后向兼容性。为了保留导入兼容性，go命令要求主版本为v2或更新的模块使用以主版本作为最后的元素的模块路径。例如，example.com/m的版本v2.0.0必须使用模块路径example.com/m/v2，且该模块中的包将使用该路径作为其导入路径前缀，就如example.com/m/v2/sub/pkg。以这种方式在模块路径和导入路径中包含主版本号称为“语义导入版本控制”。主版本为v2或更后的模块的伪版本以该主版本开头，而不是v0，就如v2.0.0-20180326061214-4fc5987536ef。

作为一个特殊情况，以gopkg.in/开头的模块路径继续使用在该系统上建立的约定：主版本总是出现，且其前面是点号而不是斜杠：gopkg.in/yaml.v1和gopkg.in/yaml.v2，而非gopkg.in/yaml和gopkg.in/yaml/v2。

go命令将带有不同模块路径的模块视为无关的：其不会在example.com/m和example.com/m/v2之间建立关联。带有不同主版本的模块可以在构建中一起使用，并因为事实上它们的包使用不同的导入路径而保持分离。

在语义版本控制中，主版本v0用于最初的开发，表示没有稳定性或后向兼容性的期望。主版本v0不会出现在模块路径中，因为那些版本是为v1.0.0准备的，且v1也不会出现在模块路径中。

在语义导入版本控制约定引入之前编写的代码，可能使用主版本v2或更后的来描述如同在v0和v1中使用的不带版本的导入路径的相同集合。为了顾及这样的代码，如果源代码仓库对没有go.mod的文件树有v2.0.0或更后的标签，版本被认为是v1模块的可用的版本的一部分，且转换为模块版本时给出一个+incompatible后缀，如同v2.0.0+incompatible。+incompatible标签也会应用于从这种版本产生的伪版本，如同v2.0.1-0.yyyymmddhhmmss-abcdefabcdef+incompatible。

通常，在构建列表（如同“go list -m all”所报告）中有对v0版本、预发布版本、伪版本、+incompatible版本的依赖，预示着当升级依赖时更可能出问题，因为对它们没有兼容性期望。

关于语义导入版本控制的更多信息参阅[https://research.swtch.com/vgo-import](https://research.swtch.com/vgo-import)，关于语义版本控制的更多信息参阅[https://semver.org/](https://semver.org/)。

## 模块代码布局

目前，关于如何将版本控制系统中的源代码映射到模块文件树的信息参阅[https://research.swtch.com/vgo-module](https://research.swtch.com/vgo-module)。

## 模块下载和校验

根据GOPROXY环境变量（参阅“go help env”）的设置，go命令可以从代理或直接连接到源代码控制服务器来拉取模块。GOPROXY的默认设置为“https://proxy.golang.org,direct”，表示尝试从Google运行的Go模块镜像，且如果代理报告其没有该模块（HTTP错误404或410）则退回直接连接。关于服务的隐私政策参阅[https://proxy.golang.org/privacy](https://proxy.golang.org/privacy)。如果GOPROXY设置为字符串“direct”，下载使用到源代码控制服务器的直接连接。设置GOPROXY为“off”禁止从任何来源下载模块。否则，GOPROXY期望为模块代理URL的逗号分隔的列表，在这种情况下go命令将从那些代理拉取模块。对每个请求，go命令依次尝试每个代理，只在如果当前代理返回404或410的HTTP响应时才移动至下一个。字符串“direct”可以出现在代理列表中，令到在搜索中该查找点尝试直接的连接。任何在“direct”之后列出的代理从不会被查询。

GOPRIVATE和GONOPROXY环境变量允许为选择的模块绕过代理。详细信息参阅“go help module-private”。

不管模块的来源是什么，go命令都会将下载结果与已知的校验和校对，来发现任何特定模块版本的内容中从这一天到下一天未预期的改变。该校对首先查看当前模块的go.sum文件，但会回退至Go校验和数据库，其由GOSUMDB和GONOSUMDB环境变量控制。详细信息参阅“go help module-auth”。

关于代理协议以及缓存的下载包格式的详细信息参阅“go help goproxy”。

## 模块和使用vendor

当使用模块时，go命令完全地忽略vendor目录。

默认情况下，go命令通过从其源下载模块并使用那些下载的副本（在校验之后，如之前的小节所描述）来满足依赖。为了允许与Go的较旧版本互操作，或者为了确保构建使用的所有文件在单独的文件树中保存在一起，“go mod vendor”在主模块的根目录创建名字为vendor的目录，并在该处存储依赖模块的所有包，它们需要用来支持构建和测试主模块中的包。

要使用主模块的顶级vendor目录来满足依赖以进行构建（禁止使用通常的网络源和本地缓存），使用“go build -mod=vendor”。注意只有主模块的顶层vendor目录才被使用；在其他位置的vendor目录仍然被忽略。

# module-get主题——模块感知的go get

（译注：该主题介绍使用模块管理源代码和依赖的行为，亦即 **go get——添加依赖包至当前模块并安装之** 小节的内容，使用GOPATH的行为描述于 **gopath-get主题——遗留的GOPATH的go get** 小节。）

# module-auth主题——使用go.sum的模块校验

go命令尝试校验每个下载的模块，检查今天为特定模块版本下载的比特是否匹配昨天下载的比特。这确保可重复的构建并发现意想不到的修改的引入，无论是否恶意。

在每个模块的根，和go.mod一起，go命令维护一个名字为go.sum的文件，其包含模块依赖的加密校验和。

go.sum中每行的格式为三个字段：

```
<module> <version>[/go.mod] <hash>
```

每个已知的模块版本在go.sum文件中产生两行。第一行给出模块版本的文件树的哈希。第二行附加“/go.mod”至版本version后并给出仅限于模块版本的（可能是合成的）go.mod文件的哈希。仅限于go.mod的哈希允许下载和校验用来计算依赖图的模块版本的go.mod文件，不用也下载所有模块的源代码。

哈希hash以格式为“h<N>:”的算法前缀开头。唯一定义的算法前缀为“h1:”，其使用SHA-256。

## 模块校验失败

go命令维护一份下载的包的缓存，并在下载时计算和记录每个包的加密校验和（译注：可能位于$GOPATH/pkg/mod/cache/download/<module>/@v/<version>.ziphash）。在一般的操作中，go命令将主模块的go.sum文件和这些预先计算的校验和校对，而非在每次命令调用时重新计算之。“go mod verify”命令检查模块下载的缓存副本仍然匹配其记录的校验和及go.sum中的条目。

在日常的开发中，给定模块版本的校验和应该从不改变。每次依赖被给定的主模块使用时，go命令将其本地缓存副本——无论是否新近下载——与主模块的go.sum校对。如果校验和不匹配，go命令报告该不匹配为安全错误并拒绝运行构建。当此发生时，谨慎继续行事：代码意外地修改意味着今天的构建将不会匹配昨天的，且意外的修改不可能是有用的。

如果go命令报告go.sum中的不匹配，为报告的模块版本下载的代码则不匹配在主模块之前的构建中使用的那些代码。在此刻找出正确的校验和是什么是很重要的，来决定go.sum是错的还是下载的代码是错误。通常go.sum是正确的：你希望使用与你昨天使用相同的代码。

如果下载的模块还未在go.sum中被包含且其为公共可用的模块，go命令查询Go校验和数据库来拉取期望的go.sum行。如果下载的代码不匹配那些行，go命令报告该不匹配并退出。注意对已经在go.sum中列出的模块版本不再查询数据库。

如果报告了go.sum不匹配，总是值得调查为什么今天下载的代码与昨天下载的不同。

GOSUMDB环境变量标记了使用的校验和数据库名字及可选地其公钥和URL，如同：

```
GOSUMDB="sum.golang.org"
GOSUMDB="sum.golang.org+<publickey>"
GOSUMDB="sum.golang.org+<publickey> https://sum.golang.org"
```

go命令知道sum.golang.org的公钥，并且名字sum.golang.google.cn（在中国大陆中可用）连接至sum.golang.org校验和数据库；使用任何其它的数据库要求显式地给出公钥。URL默认为“https://”后跟数据库名字。

GOSUMDB默认为“sum.golang.org”，为被Google运行的Go校验和数据库。关于服务的隐私政策参阅[https://sum.golang.org/privacy](https://sum.golang.org/privacy)。

如果GOSUMDB设置为“off”，或如果“go get”以-insecure标志调用，不会查询校验和数据库，并且接受所有未识别的模块，以放弃验证对所有模块的可重复下载的安全保证作为代价。对特定模块绕过校验和数据库的更好方式为使用GOPRIVATE或GONOSUMDB环境变量。详细信息参阅“go help module-private”。

“go env -w”命令（参阅“go help env”）可以用来为以后的go命令调用设置这些环境变量。

# module-private主题——非公共模块的模块配置

go命令默认从proxy.golang.org上的公共Go模块镜像下载模块。无论来源是什么，其也默认将下载的模块与sum.golang.org上的公共Go校验和数据库校验。对公开可获得的源代码这些默认行为工作得很好。

GOPRIVATE环境变量控制哪些模块go命令认为是私有的（不是公开可获得的）并应该因此不使用代理或校验和数据库。变量是模块路径前缀的glob模式（以Go的path.Match的语法）的逗号分隔的列表。例如，

```
GOPRIVATE=*.corp.example.com,rsc.io/private
```

其令go命令将带有匹配任一模式的路径前缀的任何模块视为私有，包括git.corp.example.com/xyzzy、rsc.io/private、rsc.io/private/quux。

GOPRIVATE环境变量可能也会被其它工具使用来标记非公共模块。例如，编辑器可以使用GOPRIVATE来决定是否将包import超链接至godoc.org页面。

为了对模块下载和校验进行细粒度的控制，GONOPROXY和GONOSUMDB接受相同类型的glob列表，并覆盖GOPRIVATE以分别地对是否使用代理和校验和数据库作特定的决定。

例如，如果公司运行了一个提供私有模块服务的模块代理，用户可以如下使用来配置go：

```
GOPRIVATE=*.corp.example.com
GOPROXY=proxy.example.com
GONOPROXY=none
```

这将告诉go命令和其它工具以corp.example.com子域名开头的模块是私有的，但对下载公共和私有模块都应使用公司代理，因为GONOPROXY已被设置为不会匹配任何模块的模式，从而覆盖GOPRIVATE。

“go env -w”命令（参阅“go help env”）可以用来为以后的go命令调用设置这些环境变量。

# packages主题——包列表和模式

许多命令应用于一个包集合：

```shell
go action [packages]
```

通常，[packages]是导入路径的列表。

为根路径或以.或..元素开头的导入路径被解析为文件系统路径并表示该目录中的包。

否则，对某些在GOPATH环境变量中列出的DIR，导入路径P表示DIR/src/P目录中找到的包（更多详细信息参阅：“go help gopath”）。

如果没有给出导入路径，操作应用于当前目录中的包。

路径有四个保留的名字不应该用于使用go工具进行构建的包：

* “all”：扩展为所有GOPATH树中找到的所有包。例如，“go list all”列出本地系统上的所有包。当使用模块时，“all”扩展为主模块及其依赖中的所有包，包括其中任何的测试所需的依赖。
* “cmd”：扩展为Go仓库的命令及其内部库。
* “main”：表示独立的可执行文件中的顶层包。
* “std”：类似all但只扩展为标准Go库中的包。

以“cmd/”开头的导入路径只匹配Go仓库中的源代码。

导入路径是一个模式如果其包含一个或多个“...”通配符，其中每个都可以匹配任何字符串，包括空字符串和含斜杠的字符串。这样的模式扩展为GOPATH树中找到的名字匹配模式的所有包目录。

为了令常见的模式更方便，有两种特殊情况。第一种，模式末尾的/...可以匹配空字符串，因此net/...匹配net和在其子目录中的包，如net/http。第二种，任何包含通配符的斜杠分隔的模式元素从不参与vendor包路径中“vendor”元素的匹配，因此./...不匹配./vendor或./mycode/vendor的子目录中包，但./vendor/...和./mycode/vendor/...可以。然而，注意，自身包含代码的名字为vendor的目录不是vendor的包：cmd/vendor可能是名字为vendor的命令，并且模式cmd/...匹配之。更多关于使用vendor的信息参阅[golang.org/s/go15vendor](golang.org/s/go15vendor)。

导入路径也可以命名要从远程仓库下载的下载的包。详细信息运行“go help importpath”。

程序中的每个包必须有唯一的导入路径。按习惯，这通过使每个路径以属于你的唯一前缀开始来实现。例如，在Google内部使用的路径都以“google”开头，并且表示远程仓库的路径以代码的路径开头，例如“github.com/user/repo”。

程序中的包不必有唯一的包名，但有两个有特殊含义的保留包名。名字main表示命令，而不是库。命令会被构建为二进制且不能被导入。名字documentation表示目录中非Go程序的文档。documentation包中的文件会被go命令忽略。

作为特殊情况，如果包列表是单个目录中的.go文件的列表，命令会被应用于恰好由那些文件构成的单个合成的包，忽略那些文件中的任何构建约束并忽略目录中任何其它文件。

以“.”或“_”开头的目录和文件名会被go工具忽略，如同名字为“testdata”的目录。

# testflag主题——测试标志

“go test”命令接受应用于“go test”自身的标志和应用于生成的测试二进制的标志。

标志中的几个控制性能分析（profiling）并写入适合“go tool pprof”的性能分析执行的文件；更多信息运行“go tool pprof -h”。pprof的--alloc_space、--alloc_objects、--show_bytes选项控制信息如何呈现。

以下的标志被“go test”命令识别并控制任何测试的执行：

（译注：以下标志阐述中，测试（大多数）指test，基准测试指benchmark，范例指example；性能分析指profile。）

* -bench regexp：只运行那些匹配正则表达式的基准测试。默认情况下，不运行基准测试。要运行所有基准测试，使用“-bench .”或“-bench=.”。正则表达式由无括号的斜杠（/）字符分隔为一系列正则表达式，且基准测试的标识符的每部分必须匹配序列中对应的元素，如果有的话。匹配项的可能的父项以b.N=1运行来标识子基准测试。例如，给定-bench=X/Y，顶层基准测试匹配X且以b.N=1运行来发现任何匹配Y的子基准测试，其是之后会全部运行的项。
* -benchtime t：为每个基准测试运行足够的迭代以消耗时间t，其被指定为time.Duration（例如，-benchtime 1h30s）。默认值为1秒（1s）。特殊的语法Nx表示运行基准测试N次（例如，-benchtime 100x）。
* -count n：运行每个测试和基准测试n次（默认为1）。如果-cpu被设置，对每个GOMAXPROCS值（译注：-cpu设置的列表中的每个值）运行n次。范例总是只运行一次。
* -cover：启用覆盖率分析。注意因为覆盖率分析通过在编译前注解源代码来工作，启用覆盖率分析的编译和测试失败可能报告和原来的源代码不对应的行号。
* -covermode set,count,atomic：设置对被测试的包的覆盖率分析模式。默认为“set”除非-race被启用，这种情况下为“atomic”。值为：

	* set（bool）：此语句是否运行？
	* count（int）：此语句运行多少次？
	* atomic（int）：计数，但在多线程测试中正确；明显更高消耗。

	会设置-cover。
* -coverpkg pattern1,pattern2,pattern3：对匹配模式的包的每个测试应用覆盖率分析。默认对每个测试只分析被测试的包。关于包模式的描述参阅“go help packages”。会设置-cover。
* -cpu 1,2,4：指定GOMAXPROCS（译注：runtime.GOMAXPROCS的返回值）值列表，测试或基准测试会以此来执行。默认为GOMAXPROCS的当前值。
* -failfast：在首个测试失败之后不再开始新的测试。
* -list regexp：列出匹配正则表达式的测试、基准测试或范例。没有测试、基准测试或范例会被运行。这只会列出顶层测试。没有子测试或子基准测试会被展示。
* -parallel n：允许调用t.Parallel的测试函数并行执行。这个标志的值是同时运行的测试的最大值；默认下，其设置为GOMAXPROCS的值。注意-parallel只应用于单个测试二进制中。根据-p标志的设置（参阅“go help build”），“go test”命令也可能为多个包并行运行测试。
* -run regexp：只运行匹配正则表达式的那些测试和范例。对于测试，正则表达式由无括号的斜杠（/）字符分隔为一系列正则表达式，且测试的标识符的每部分必须匹配序列中对应的元素，如果有的话。注意匹配项的可能的父项也会运行，因此-run=X/Y匹配、运行、报告所有匹配X的测试的的结果，即使它们并没有匹配Y的子测试，因为必须运行它们来查找那些子测试。
* -short：告诉长时间运行的测试来缩短它们的运行时间。默认是关闭的，但在all.bash运行期间会设置，因此安装Go树可以运行健全的检查但不会花费时间运行详尽的测试。
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

当“go test”运行测试二进制时，其会在对应的包源代码目录内运行。根据测试的不同，当直接调用生成的测试二进制时可能需要做同样的事情。

命令行包列表，如果存在，必须出现在任何go test命令不认识的标志之前。继续上面的例子，包列表本应出现在-myflag之前，但可以出现在-v的任一侧。

当“go test”运行在包列表模式时，“go test”缓存成功的包测试结果来避免测试的不必要重复运行。要禁用测试缓存，使用除可缓存标志以外的任意测试标志或参数。显式地禁用测试缓存的习惯方式为使用-count=1。

要避免测试二进制的参数被解析为已知的标志或包名，使用-args（参阅“go help test”），其将命令行中余下部分不解析和不修改地传递至测试二进制。

例如，命令`go test -v -args -x -v`将编译测试二进制并以`pkg.test -test.v -x -v`运行。类似地，`go test -args math`将编译测试二进制并以`pkg.test math`运行。

在第一个例子中，-x和第二个-v不修改地传递至测试二进制且对go命令自身没有影响。在第二个例子中，参数math传递至测试二进制，而不是被解析为包列表。

# testfunc主题——测试函数

“go test”命令期望在与被测试的包对应的“*_test.go”文件中找到测试、基准测试和范例函数。

测试函数是一个名为TestXxx（Xxx处不以小写字母开头）且应具有特征：

```go
func TestXxx(t *testing.T) { ... }
```

基准测试函数是一个名为BenchmarkXxx且应具有特征：

```go
func BenchmarkXxx(b *testing.B) { ... }
```

范例函数类似于测试函数，但取代使用*testing.T来报告成功或失败，会打印输出至os.Stdout。如果函数中的最后的注释以“Output:”开头，那么输出精确地与注释比较（参阅下面的例子）。如果最后的注释以“Unordered output:”开头，那么输出与注释比较，但是忽略行的顺序。没有如此注释的范例会被编译但不会被执行。“Output:”后没有文本的范例会被编译、执行并期望不产生输出。

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

整个测试文件会作为范例展示，当其包含单个范例函数，至少一个其它函数、类型、变量或常量声明，没有测试或基准测试函数时。

更多信息参阅testing包的文档。
