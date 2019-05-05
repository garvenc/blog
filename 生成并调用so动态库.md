本文更新于2019-01-03。

# 生成库

头文件fn.h如下：

```cpp
#ifndef __FN_H__
#define __FN_H__

#ifdef __cplusplus
extern "C"
{
#endif

typedef int (*FnAdd)(int a, int b);

int add(int a, int b);

#ifdef __cplusplus
}
#endif

#endif // __FN_H__
```

源文件fn.c如下：

```c
#include "fn.h"

int add(int a, int b) {
	return a+b;
}
```

编译生成libfn.so库：

```shell
gcc -fPIC -shared fn.c -o libfn.so
```

若不使用extern "C"，且使用g++编译或使用gcc编译cpp文件，则生成C++形式的库。

# 调用库

源文件main.c如下：

```c
#include <stdio.h>
#include <stdlib.h>
#include <dlfcn.h>

#include "fn.h"

int main() {
	void* handle = dlopen("./libfn.so", RTLD_LAZY);
	if (handle == NULL) {
		printf("dlopen: %s\n", dlerror());
		return -1;
	}
	
	FnAdd add = (FnAdd)dlsym(handle, "add");
	if (add == NULL) {
		printf("dlsym: %s\n", dlerror());
		return -1;
	}
	
	int a = 1;
	int b = 2;
	printf("add %d+%d=%d\n", a, b, add(a, b));

	dlclose(handle);
	return 0;
}
```

编译生成可执行文件a.out：

```shell
gcc -rdynamic -ldl main.c -o a.out
```

如使用dlopen调用库时没指定库文件路径，只指定库文件名（如libfn.so），则执行程序前需导出环境变量LD_LIBRARY_PATH，将库文件所在目录加入其中。

```shell
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:dir
```
