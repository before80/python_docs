+++
title = "7. 在 iOS 上使用 Python"
date = 2024-11-14T22:13:29+08:00
weight = 60
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/using/ios.html](https://docs.python.org/zh-cn/3.13/using/ios.html)
>
> 收录该文档的时间：`2024-11-14T22:13:29+08:00`

# 7. 在 iOS 上使用 Python

作者:

​	Russell Keith-Magee (2024-03)

​	在 iOS 上的 Python 不同于桌面平台上的 Python。 在桌面平台上，Python 通常是作为系统资源安装并可供该计算机上的任何用户使用。 用户将通过运行 **python** 可执行文件并在交互提示符下输入命令，或者通过运行 Python 脚本的方式与 Python 进行交互。

​	在 iOS，不存在作为系统资源安装种概念。 唯一的软件分发单元是 "app"。 也不存在可以让你运行 **python** 可执行文件，或与 Python REPL 进行交互的控制台。

​	因此，你在 iOS 上使用 Python 的唯一方式是嵌入模式 —— 也就是说，通过编写原生的 iOS 应用，并使用 `libPython` 嵌入一个 Python 解释器，然后使用 [Python 嵌入式 API](https://docs.python.org/zh-cn/3.13/extending/embedding.html#embedding) 来发起调用 Python 代码。 完整的 Python 解释器、标准库，以及所有 Python 代码都将被打包为可通过 iOS App Store 发布的独立软件包。

​	如果你想要上手尝试以 Python 来编写 iOS app，像 [BeeWare](https://beeware.org/) 和 [Kivy](https://kivy.org/) 这样的项目将提供更方便的用户体验。 这些项目能够管理支持 iOS 项目运行的相关复杂问题，这样你只需要处理 Python 代码本身。

## 7.1. iOS 上的 Python 运行时

### 7.1.1. iOS 版本兼容性

​	受支持的最低 iOS 版本是在编译时指定的，对 `configure` 使用 [`--host`](https://docs.python.org/zh-cn/3.13/using/configure.html#cmdoption-host) 选项。 在默认情况下，当针对 iOS 编译时，Python 编译将设置受支持的最低 iOS 版本为 13.0。 我使用不同的最低 iOS 版本，请将版本号作为 `--host` 参数的一部分提供 —— 例如，`--host=arm64-apple-ios15.4-simulator` 将编译一份部署目标为 15.4 的 ARM64 模拟器构建版。

### 7.1.2. 平台识别

​	当在 iOS 上执行时，`sys.platform` 将报告为 `ios`。 无论 app 是在模拟器还是物理设备上运行，都将在 iPhone 或 iPad 上返回该值。

​	有关特定运行时环境的信息，包括 iOS 版本、设备型号以及设备是否为模拟器，可使用 [`platform.ios_ver()`](https://docs.python.org/zh-cn/3.13/library/platform.html#platform.ios_ver) 来获取。 [`platform.system()`](https://docs.python.org/zh-cn/3.13/library/platform.html#platform.system) 将根据具体设备报告为 `iOS` 或 `iPadOS`。

[`os.uname()`](https://docs.python.org/zh-cn/3.13/library/os.html#os.uname) 报告内核等级的详情；它将报告系统名称 `Darwin`。

### 7.1.3. 标准库可用性

​	Python 标准库在standard library has some notable omissions and restrictions on iOS 上有一点需要注意的缺失和限制。 请参阅 [针对 iOS 的 API 可用性指南](https://docs.python.org/zh-cn/3.13/library/intro.html#mobile-availability) 了解详情。

### 7.1.4. 二进制扩展模块

​	作为一个平台的 iOS 与别家的显著不同之处在于 App Store 分发机制对应用的打包方式设置了硬性要求。其中一项要求规定了应当如何分发二进制扩展模块。

​	iOS App Store 要求 iOS app 中的 *所有* 二进制模块都必须为动态库，包含在具有适当元数据的框架中，保存于被打包 app 的 `Frameworks` 文件夹下。 每个框架只能有一个二进制模块，而在 `Frameworks` 文件夹之外不能有可执行的二进制文件。

​	这与 Python 通常用于发布二进制文件的方式存在冲突，此方式允许从 `sys.path` 上的任何位置加载二进制扩展模块。 为确保符合 App Store 的政策，iOS 项目必须对任何 Python 包进行后期处理，将 `.so` 二进制模块转换为具有适当元数据和签名的单个独立框架。 有关如何执行后期处理的更多细节，请参阅 [将 Python 添加到你的项目](https://docs.python.org/zh-cn/3.13/using/ios.html#adding-ios) 指南。

​	为帮助 Python 在新位置中发现二进制文件，在 `sys.path` 中的原始 `.so` 文件将替换为 `.fwork` 文件。 此文件是一个包含框架二进制文件相对于 app 捆绑包位置的文本文件。 为允许框架将其解析回原始位置，框架必须包含一个 `.origin` 文件，其中包含 `.fwork` 相对对 app 捆绑包的位置。

​	例如，考虑导入 `from foo.bar import _whiz` 的情况，其中 `_whiz` 是使用二进制模块 `sources/foo/bar/_whiz.abi3.so` 实现的，这里 `sources` 是在 `sys.path` 中注册的相对于 app 包的位置。 此模块 *必须* 发布为 `Frameworks/foo.bar._whiz.framework/foo.bar._whiz` (根据模块的完整导入路径创建框架名称)，并通过 `.framework` 目录中的 `Info.plist` 文件将二进制文件标识为一个框架。 `foo.bar._whiz` 模块在原始位置中以一个 `sources/foo/bar/_whiz.abi3.fwork` 标记文件来代表，其中包含路径 `Frameworks/foo.bar._whiz/foo.bar._whiz`。 该框架还要包含 `Frameworks/foo.bar._whiz.framework/foo.bar._whiz.origin`，其中包含 `.fwork` 文件的路径。

​	当在 iOS 上运行时，Python 解释器将安装一个能够读取并导入 `.fwork` 文件的 [`AppleFrameworkLoader`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.AppleFrameworkLoader)。 一旦被导入，该二进制模块的 `__file__` 属性将报告为 `.fwork` 文件的位置。 不过，被加载模块的 [`ModuleSpec`](https://docs.python.org/zh-cn/3.13/library/importlib.html#importlib.machinery.ModuleSpec) 则会将 `origin` 报告为框架文件夹下的二进制文件的位置。

### 7.1.5. 编译器存根二进制文件

​	Xcode 不会暴露针对 iOS 的显式编译器；作为替代，它会使用一个能解析出完整编译器路径的 `xcrun` 脚本 (例如，`xcrun --sdk iphoneos clang` 将获得针对 iPhone 设备的 `clang`)。 不过，使用此脚本会导致两个问题：

- `xcrun` 的输出包括特定机器专属的路径，这导致无法在用户之间共享 sysconfig 模块；并且
- 它会导致包括空格的 `CC`/`CPP`/`LD`/`AR` 定义。 有大量 C 生态系统工具假定你可以在第一个空格处拆分命令行来获取编译器可执行文件的路径；当使用 `xcrun` 这是不行的。

​	为避免这些问题，Python 提供了针对这些工具的程序段。 这些代码段是在下层 `xcrun` 工具之上的 shell 脚本包装器，发布在随同已编译的 iOS 框架一起分发的 `bin` 文件夹中。 这些脚本可以被重新调整位置，并且总是会解析到正确的本机系统路径。 通过在配合框架的 bin 文件夹中包括这些脚本，`sysconfig` 模块的内容将可适用于要编译他们自己的模块的最终用户。 在为 iOS 编译第三方 Python 模块时，你应当确保这些程序段二进制文件位于你的路径中。

## 7.2. 在 iOS 上安装 Python

### 7.2.1. 构建 iOS 应用程序的工具

​	针对 iOS 构建需要使用 Apple 的 Xcode 工具集。 强烈建议你使用 Xcode 最新稳定发布版。 这将需要使用最新（或者次新）发布的 macOS 版本，因为 Apple 不会为更旧的 macOS 版本维护 Xcode。 Xcode 命令行工具对于 iOS 开发来说是不够的；你需要 *完整的* Xcode 安装版。

​	如果你想要在 iOS 模拟器上运行你的代码，你还需要安装 iOS 模拟器平台。 当你首次运行 Xcode 应该会提示你选择一个 iOS 模拟器平台。 或者，你也可以通过在 Settings 面板的 Platforms 选项卡中添加一个 iOS 模拟器平台。



### 7.2.2. 在 iOS 项目中添加 Python

​	Python 可以被添加到任何 iOS 项目，不论它是使用 Swift 还是 Objective C。 下面的例子将使用 Objective C；如果你是使用 Swift，你可能会发现 [PythonKit](https://github.com/pvieito/PythonKit) 这样的库非常有用。

​	要在 iOS Xcode 项目中添加 Python：

1. 构建或是获取一个 Python `XCFramework`。 请参阅 [iOS/README.rst](https://github.com/python/cpython/tree/3.13/iOS/README.rst) (在 CPython 源代码发布包中) 中的说明了解有关如何构建 Python `XCFramework` 的细节。 要满足最基本要求，你需要一个支持 `arm64-apple-ios`，再加上 `arm64-apple-ios-simulator` 或 `x86_64-apple-ios-simulator` 之一的构建版。

2. 将 `XCframework` 拖到你的 iOS 项目中。 在下面的说明中，我们将假定你已将 `XCframework` 放到你的项目的根目录下；不过，你也可以通过按需调整路径来使用任何其他你想要的位置。

3. 将 `iOS/Resources/dylib-Info-template.plist` 文件拖到你的项目中，并确保它已关联到 app 目标。

4. 将你的应用程序代码作为一个文件夹添加到你的 Xcode 项目中。 在下面的说明中，我们将假定你的用户代码位于你的项目中名为 `app` 的文件夹；你也可以通过按需调整路径来使用任何其他位置。 请确保该文件夹已关联到你的 app 目标。

5. 通过选择你的 Xcode 项目的根节点来选择 app 目标，然后在边栏中出现的目标名称进行选择。

6. 在 "General" 设置中，在 "Frameworks, Libraries and Embedded Content" 之下，增加了 `Python.xcframework`，并选中 "Embed & Sign"。

7. 在"构建设置"选项卡中，修改以下内容：

   - 构建选项
     - 用户脚本沙盒：否
     - 启用可测试性：是
   - 搜索路径
     - 框架搜索路径： `$(PROJECT_DIR)`
     - 头文件搜索路径： `"$(BUILT_PRODUCTS_DIR)/Python.framework/Headers"`
   - Apple Clang - 警告 - 所有语言
     - 引用包含在框架头文件中：否

8. 添加一个用于将 Python 标准库拷贝到你的 app 中的构建步骤。 在 "Build Phases" 选项卡中，在 "Embed Frameworks" 步骤 *之前*，"Copy Bundle Resources" 步骤 *之后* 添加一个新的 "Run Script" 构建步骤。 将该步骤命名为 "Install Target Specific Python Standard Library"，取消 "Based on dependency analysis" 复选框，并将脚本内容设为：

   ```
   set -e
   
   mkdir -p "$CODESIGNING_FOLDER_PATH/python/lib"
   if [ "$EFFECTIVE_PLATFORM_NAME" = "-iphonesimulator" ]; then
       echo "Installing Python modules for iOS Simulator"
       rsync -au --delete "$PROJECT_DIR/Python.xcframework/ios-arm64_x86_64-simulator/lib/" "$CODESIGNING_FOLDER_PATH/python/lib/"
   else
       echo "Installing Python modules for iOS Device"
       rsync -au --delete "$PROJECT_DIR/Python.xcframework/ios-arm64/lib/" "$CODESIGNING_FOLDER_PATH/python/lib/"
   fi
   ```

   请注意 XCframework 中的模拟器名称 "slice" 可能有所不同，具体取决于你的 `XCFramework` 所支持的 CPU 架构。

9. 再添加第二个用于将标准库中的二进制扩展模块处理为 "Framework" 格式的构建步骤。 紧随你在第 8 步添加的步骤 *之后立即* 添加一个 "Run Script" 构建步骤，命名为 "Prepare Python Binary Modules"。 它应当取消选择 "Based on dependency analysis"，并将脚本内容设为：

   ```
   set -e
   
   install_dylib () {
       INSTALL_BASE=$1
       FULL_EXT=$2
   
       # 扩展文件的名称
       EXT=$(basename "$FULL_EXT")
       # 扩展文件相对与捆绑包的位置
       RELATIVE_EXT=${FULL_EXT#$CODESIGNING_FOLDER_PATH/}
       # 扩展文件相对于安装目录的路径
       PYTHON_EXT=${RELATIVE_EXT/$INSTALL_BASE/}
       # 扩展模块带点号的完整名称，根据文件目录来构造。
       FULL_MODULE_NAME=$(echo $PYTHON_EXT | cut -d "." -f 1 | tr "/" ".");
       # 捆绑包标识；未实际使用，但为 Xcode 框架打包所必需
       FRAMEWORK_BUNDLE_ID=$(echo $PRODUCT_BUNDLE_IDENTIFIER.$FULL_MODULE_NAME | tr "_" "-")
       # 框架文件夹的名称。
       FRAMEWORK_FOLDER="Frameworks/$FULL_MODULE_NAME.framework"
   
       # 如果框架文件夹不存在，则创建它。
       if [ ! -d "$CODESIGNING_FOLDER_PATH/$FRAMEWORK_FOLDER" ]; then
           echo "Creating framework for $RELATIVE_EXT"
           mkdir -p "$CODESIGNING_FOLDER_PATH/$FRAMEWORK_FOLDER"
           cp "$CODESIGNING_FOLDER_PATH/dylib-Info-template.plist" "$CODESIGNING_FOLDER_PATH/$FRAMEWORK_FOLDER/Info.plist"
           plutil -replace CFBundleExecutable -string "$FULL_MODULE_NAME" "$CODESIGNING_FOLDER_PATH/$FRAMEWORK_FOLDER/Info.plist"
           plutil -replace CFBundleIdentifier -string "$FRAMEWORK_BUNDLE_ID" "$CODESIGNING_FOLDER_PATH/$FRAMEWORK_FOLDER/Info.plist"
       fi
   
       echo "Installing binary for $FRAMEWORK_FOLDER/$FULL_MODULE_NAME"
       mv "$FULL_EXT" "$CODESIGNING_FOLDER_PATH/$FRAMEWORK_FOLDER/$FULL_MODULE_NAME"
       # 创建占位用 .fwork 文件指明 .so 所在位置
       echo "$FRAMEWORK_FOLDER/$FULL_MODULE_NAME" > ${FULL_EXT%.so}.fwork
       # 在框架中创建指向 .so 文件位置的反向引用
       echo "${RELATIVE_EXT%.so}.fwork" > "$CODESIGNING_FOLDER_PATH/$FRAMEWORK_FOLDER/$FULL_MODULE_NAME.origin"
    }
   
    PYTHON_VER=$(ls -1 "$CODESIGNING_FOLDER_PATH/python/lib")
    echo "Install Python $PYTHON_VER standard library extension modules..."
    find "$CODESIGNING_FOLDER_PATH/python/lib/$PYTHON_VER/lib-dynload" -name "*.so" | while read FULL_EXT; do
       install_dylib python/lib/$PYTHON_VER/lib-dynload/ "$FULL_EXT"
    done
   
    # 清理 dylib 模板
    rm -f "$CODESIGNING_FOLDER_PATH/dylib-Info-template.plist"
   
    echo "Signing frameworks as $EXPANDED_CODE_SIGN_IDENTITY_NAME ($EXPANDED_CODE_SIGN_IDENTITY)..."
    find "$CODESIGNING_FOLDER_PATH/Frameworks" -name "*.framework" -exec /usr/bin/codesign --force --sign "$EXPANDED_CODE_SIGN_IDENTITY" ${OTHER_CODE_SIGN_FLAGS:-} -o runtime --timestamp=none --preserve-metadata=identifier,entitlements,flags --generate-entitlement-der "{}" \;
   ```

10. 添加 Objective C 代码来初始化并使用嵌入模式的 Python 解释器。 你应当确保：

> - [`UTF-8 模式`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyPreConfig.utf8_mode) 被 *启用*；
> - [`带缓冲的 stdio`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.buffered_stdio) 被 *禁用*；
> - [`写入字节码`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.write_bytecode) 被 *禁用*；
> - [`信号处理器`](https://docs.python.org/zh-cn/3.13/c-api/init_config.html#c.PyConfig.install_signal_handlers) 被 *启用*；
> - 用于解释器的 `PYTHONHOME` 将被配置为指向你的 app 捆绑包的 `python` 子文件夹；并且
> - 用于解释器的 `PYTHONPATH` 包括：
>   - 你的 app 的封包的 `python/lib/python3.X` 子文件夹，
>   - 你的 app 的封包的 `python/lib/python3.X/lib-dynload` 子文件夹，以及
>   - 你的 app 的封包的 `app` 子文件夹
>
> ​	你的 app 捆绑包的位置可使用 `[[NSBundle mainBundle] resourcePath]` 来确定。

​	这些说明的步骤 8, 9 和 10 均假定你有一个单独的纯 Python 应用程序代码文件夹，其名称为 `app`。 如果在你的 app 中还存在第三方二进制模块，则还需要一些额外的步骤：

- 你需要确保任何包含第二方二进制文件的文件夹均已关联到 app 目标，或已作为第 8 步的一部分被拷贝。 第 8 步还应当清理任何对于特定构建目标的平台来说不适用的二进制文件（即当你要构建以该模拟器为目标的 app 就应删除任何设备二进制文件）。
- 任何包含第三方二进制文件的文件夹都必须通过第 9 步处理为框架形式。 可以拷贝并调整对于处理 `lib-dynload` 文件夹的 `install_dylib` 发起调用的代码以符合这一目的。
- 如果你使用了不同的文件夹来存放第三方包，请确保该文件夹已在第 10 步中被包括为 `PYTHONPATH` 配置的一部分。

## 7.3. App Store 合规性

​	将 app 发布到第三方 iOS 设备的唯一机制是将 app 提交到 iOS App Store；提交发布的 app 必须通过 Apple 的 app 审核进程。 此进程包括一组在所提交的应用程序包中自动检查有问题代码的验证规则。

​	Python 标准库包含了一些已知会违反这些自动规则的代码。 虽然这些违规情况看来是属于误报，但 Apple 的审核规则是不可挑战的；因此，有必要修改 Python 标准库以便 app 能够通过 App Store 的审核。

​	Python 源代码树包含 [一个补丁文件](https://github.com/python/cpython/tree/3.13/Mac/Resources/app-store-compliance.patch) 可移除所有已知的会导致 App Store 审核过程出现问题的代码。 这个补丁会在针对 iOS 进行构建时自动应用。
