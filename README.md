## step 0 配置ndkbuild环境

我使用的是 Android Studio 自带的ndk-bundle，版本是15


## step 1 下载 解压 assimp3.3.1 源码

链接地址 https://github.com/assimp/assimp/releases/tag/v3.3.1/  

## step 2  生成revision.h文件

到解压后的目录 运行  

    cmake .

## step 3  修改编译脚本

进入${assimp dir}/workspaces/Android-NDK/jni  

编辑Application.mk文件，删除原有内容加入以下变量  

```
ifeq ($(CC),clang)
    NDK_TOOLCHAIN_VERSION := $(CC)
    $(info "Use llvm Compiler")
endif

APP_PLATFORM := android-16
NDK_TOOLCHAIN_VERSION := 4.9
APP_CPPFLAGS := -std=gnu++11 -frtti
APP_ABI := armeabi-v7a
APP_STL := gnustl_static
```

编辑Android.mk，找到LOCAL_C_INCLUDES变量，加入以下属性  

    $(LOCAL_PATH)/contrib/rapidjson/include

## step 4  加入代码文件std_extention.h

进入${assimp dir}/include/assimp，新建文件std_extention.h  

```
#ifndef TESTFST_STD_EXTENSION_H
#define TESTFST_STD_EXTENSION_H

#include <string>
#include <sstream>

namespace std{

    template <typename T>
    std::string to_string(T value)
    {
        std::ostringstream os ;
        os << value ;
        return os.str() ;
    }
}
#endif //TESTFST_STD_EXTENSION_H
```

## step 5  修改代码文件

打开文件${assimp dir}/include/assimp/types.h,在include部分加入代码
```
// std::toString
#include "assimp/std_extension.h"
```

打开文件${assimp dir}/code/StringUtils.h,在include部分加入代码

```
// atof
#include <stdlib.h>
```



## step 6 编译

进入${assimp dir}/workspaces/Android-NDK/jni目录运行  
```
ndk-build
```
编译正常结束后，会在${assimp dir}/workspaces/Android-NDK/libs/armeabi-v7a/目录下生成库文件  
libassimp.so 这个给c++用  
libjassimp.so 这个给java用，具体进入${assimp dir}/port/jassimp/目录查看  
二选一即可  

good luck
