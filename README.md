在Windows下使用WSL进行编译，系统版本：Ubuntu-20.04

基础安装：
```bash
wsl --install Ubuntu-20.04

sudo apt install g++
sudo apt install clang
sudo apt install make   

sudo apt update
sudo apt install llvm-dev
```
安装Python2环境，注意要提前安装下依赖库再安装。


项目的Makefile里使用了llvm-config，可以通过如下命令检查是否安装正确：
```bash
which llvm-config
llvm-config --version
```

修改Makefile里面c++11 为17，一共两处。
缺少一些头文件，可以从AOSP里下载使用：
```bash
git clone https://android.googlesource.com/platform/art
cd art
git checkout android-5.1.1_r29
```


解决编译出错问题，可以通过git提交记录查看修改点，不完全记录的修改点如下：

修改 `safe_map.h`  的 Impl 定义：
```bash
using Impl = std::map<K, V, Comparator, Allocator>;
```

```bash
using Impl = std::map<K, V, Comparator, typename std::allocator_traits<Allocator>::template rebind_alloc<std::pair<const K, V>>>;
```

因为是在Windows下使用WSL进行的编译，所以ARCH只能选择x86_64，修改Makefile，增加：
```Makefile
TARGET_ARCH := x86_64
ART_SUPPORTED_ARCH := x86_64
ART_BUILD_TARGET_ARCH := x86_64
ART_BUILD_TARGET := true
```

编译的时候也会有很多错误，也有一些是AOSP本身代码的问题，根据错误信息一一修改即可，不是很难。



增加：
https://android.googlesource.com/platform/art/+/android-5.1.1_r29/compiler/dex/reg_location.h

