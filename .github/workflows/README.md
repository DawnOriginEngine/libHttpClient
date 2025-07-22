# GitHub Actions 工作流说明

本目录包含用于自动构建 libHttpClient 项目的 GitHub Actions 工作流文件。

## 工作流文件

### 1. windows-x64-build.yml
**用途**: 专门针对 Windows x64 平台的构建工作流

**特性**:
- 支持 Debug 和 Release 配置
- 专注于 x64 平台构建
- 包含 OpenSSL 依赖项构建（libssl, libcrypto）
- 构建主要的 libHttpClient 库
- 构建 Win32 HTTP 示例
- 尝试构建单元测试（TAEF）
- 执行基本的冒烟测试
- 上传构建产物

**触发条件**:
- 推送到 main/master 分支
- 针对 main/master 分支的 Pull Request
- 手动触发

### 2. windows-full-build.yml
**用途**: 完整解决方案构建工作流

**特性**:
- 支持 Debug 和 Release 配置
- 支持 x64 和 Win32 平台
- 构建整个 Visual Studio 解决方案
- 详细的构建产物验证
- 全面的构建产物上传

**触发条件**:
- 推送到 main/master 分支
- 针对 main/master 分支的 Pull Request
- 手动触发

### 3. linux-build.yml
**用途**: Linux 平台多架构构建工作流

**特性**:
- 支持 Debug 和 Release 配置
- 支持 AMD64 (x86_64) 和 ARM64 (aarch64) 架构
- 自动设置交叉编译环境（ARM64）
- 构建 OpenSSL 依赖项（libssl, libcrypto）
- 构建 cURL 依赖项
- 构建 libHttpClient 静态和动态库
- 智能缓存机制优化构建速度
- 基本库测试和验证
- 创建发布包（tar.gz 格式）
- 上传构建产物和发布包

**触发条件**:
- 推送到 main/master 分支
- 针对 main/master 分支的 Pull Request
- 手动触发

### 4. android-build.yml
**用途**: Android 平台多架构构建工作流

**特性**:
- 支持 Debug 和 Release 配置
- 支持 armeabi-v7a、x86、x86_64 和 ARM64 (arm64-v8a) 架构
- 使用 Android NDK r25c 进行原生代码编译
- 基于 Gradle 和 CMake 的构建系统
- 构建 libcrypto、libssl 和 libHttpClient
- 智能缓存机制（Gradle 包和构建输出）
- 构建产物验证和测试
- 创建发布包（tar.gz 格式）
- 上传构建产物和发布包
- 跨架构构建验证

**触发条件**:
- 推送到 main/master/develop 分支
- 针对 main/master/develop 分支的 Pull Request
- 手动触发（可选择配置）

## 构建要求

### Windows 构建系统要求
- Windows 最新版本的 GitHub Actions runner
- Visual Studio 2022 (v143 工具集)
- MSBuild
- NuGet

### Linux 构建系统要求
- Ubuntu 最新版本的 GitHub Actions runner
- GCC/Clang 编译器
- CMake 3.6+
- 交叉编译工具链（ARM64 构建）
- 构建工具：make, autoconf, automake, libtool

### Android 构建系统要求
- Ubuntu 最新版本的 GitHub Actions runner
- JDK 11 (Temurin 发行版)
- Android NDK r25c
- Gradle 构建系统
- CMake 3.6+

### 通用依赖项
- Git 子模块（自动递归克隆）
- OpenSSL（作为项目的一部分构建）
- cURL（Linux 构建需要）
- zlib 开发库
- Windows SDK 10.0.19041.0（仅 Windows）

## 构建产物

构建完成后，以下产物将被上传为 GitHub Actions artifacts：

### Windows 构建产物
- **静态库文件**: `*.lib` 文件
- **可执行文件**: `*.exe` 文件
- **调试信息**: `*.pdb` 文件
- **动态链接库**: `*.dll` 文件（如果有）

### Linux 构建产物
- **静态库文件**: `*.a` 文件
- **动态链接库**: `*.so` 和 `*.so.*` 文件
- **发布包**: `*.tar.gz` 压缩包（包含库文件和头文件）

### Android 构建产物
- **静态库文件**: `*.a` 文件
- **动态链接库**: `*.so` 文件
- **发布包**: `*.tar.gz` 压缩包（包含库文件和头文件）
- **支持架构**: armeabi-v7a, x86, x86_64, ARM64 (arm64-v8a)

### 产物保留期
- **常规构建产物**: 30 天
- **发布包**: 90 天（仅 Release 配置）

## 使用说明

### 查看构建状态
1. 转到项目的 GitHub 页面
2. 点击 "Actions" 标签
3. 选择相应的工作流查看构建状态和日志

### 下载构建产物
1. 在 Actions 页面中，点击完成的工作流运行
2. 在 "Artifacts" 部分下载所需的构建产物

### 手动触发构建
1. 转到 Actions 页面
2. 选择要运行的工作流
3. 点击 "Run workflow" 按钮
4. 选择分支并点击 "Run workflow"

## 故障排除

### 常见问题

#### Windows 构建问题

1. **子模块未正确克隆**
   - 确保使用 `submodules: recursive` 选项
   - 检查 `.gitmodules` 文件的配置

2. **NuGet 包恢复失败**
   - 检查网络连接
   - 验证 NuGet 包源的可用性

3. **构建失败**
   - 检查构建日志中的具体错误信息
   - 确认所有依赖项都已正确配置
   - 验证 Windows SDK 版本兼容性

4. **找不到构建产物**
   - 检查输出目录路径（Out/, Int/）
   - 验证构建配置和平台设置

#### Linux 构建问题

1. **交叉编译失败（ARM64）**
   - 确认交叉编译工具链已正确安装
   - 检查 CMake 工具链文件配置
   - 验证环境变量设置（CC, CXX, AR 等）

2. **OpenSSL 构建失败**
   - 检查 OpenSSL 子模块是否正确克隆
   - 验证构建脚本的执行权限
   - 确认系统依赖项已安装（autoconf, automake, libtool）

3. **cURL 构建失败**
   - 确保 OpenSSL 已成功构建
   - 检查 cURL 配置选项
   - 验证交叉编译设置（ARM64 构建）

4. **库依赖问题**
   - 使用 `ldd` 命令检查动态库依赖
   - 确认所有必需的系统库已安装
   - 检查库文件路径和权限

5. **缓存问题**
   - 清除 GitHub Actions 缓存
   - 检查缓存键是否正确
   - 验证缓存路径配置

#### Android 构建问题

1. **NDK 版本不兼容**
   - 确认使用的 NDK 版本与项目要求一致（r25c）
   - 检查 `build.gradle` 中的 `ndkVersion` 设置
   - 验证 NDK 路径环境变量

2. **Gradle 构建失败**
   - 检查 JDK 版本兼容性（需要 JDK 11）
   - 验证 Gradle Wrapper 的执行权限
   - 确认 Android SDK 和构建工具已正确安装

3. **CMake 配置错误**
   - 检查 CMakeLists.txt 文件中的路径配置
   - 验证 Android 工具链文件
   - 确认目标架构设置（x86, arm64-v8a）

4. **依赖库构建失败**
   - 确保 libcrypto 和 libssl 按正确顺序构建
   - 检查 OpenSSL 源码子模块
   - 验证 Android 平台 API 级别设置

5. **架构特定问题**
   - armeabi-v7a: 检查 ARMv7 兼容性和 NEON 支持
   - x86: 检查模拟器兼容性设置
   - x86_64: 验证 64 位模拟器支持
   - ARM64: 验证交叉编译工具链
   - 确认 ABI 过滤器配置

6. **缓存和清理问题**
   - 清除 Gradle 缓存（`~/.gradle/caches`）
   - 删除 `Binaries/Android` 目录
   - 重新同步 Gradle 项目

### 调试技巧

#### Windows 调试

1. **启用详细日志**
   - 将 `/verbosity:minimal` 改为 `/verbosity:normal` 或 `/verbosity:detailed`

2. **检查构建输出**
   - 使用 "List build outputs" 步骤查看生成的文件

3. **本地复现**
   - 使用相同的 MSBuild 命令在本地环境中复现问题

#### Linux 调试

1. **启用详细构建日志**
   - 在 CMake 命令中添加 `--verbose` 选项
   - 在 make 命令中添加 `VERBOSE=1` 参数

2. **检查交叉编译设置**
   - 验证工具链文件内容
   - 检查环境变量是否正确设置
   - 使用 `which $CC` 确认编译器路径

3. **调试库依赖**
   - 使用 `file` 命令检查库文件架构
   - 使用 `nm` 或 `objdump` 检查符号表
   - 使用 `readelf` 检查 ELF 文件信息

4. **本地复现**
   - 在 Ubuntu 环境中使用相同的构建脚本
   - 设置相同的环境变量和工具链
   - 逐步执行构建过程以定位问题

#### Android 调试

1. **启用详细 Gradle 日志**
   - 在 Gradle 命令中添加 `--info` 或 `--debug` 选项
   - 使用 `--stacktrace` 查看完整错误堆栈

2. **检查 NDK 和工具链**
   - 验证 `ANDROID_NDK_ROOT` 环境变量
   - 检查 NDK 工具链路径：`$NDK/toolchains/llvm/prebuilt/linux-x86_64/bin/`
   - 确认目标架构编译器存在

3. **调试 CMake 配置**
   - 在 CMake 中添加 `--debug-output` 选项
   - 检查生成的 `CMakeCache.txt` 文件
   - 验证 Android 工具链文件加载

4. **分析构建产物**
   - 使用 `file` 命令检查 `.so` 文件架构
   - 使用 `readelf -h` 查看 ELF 头信息
   - 使用 `objdump -f` 检查文件格式

5. **本地复现**
   - 安装相同版本的 Android SDK 和 NDK
   - 设置相同的环境变量
   - 在本地运行相同的 Gradle 命令
   - 逐个构建子项目以定位问题

6. **Gradle 项目调试**
   ```bash
   # 清理项目
   ./gradlew clean
   
   # 查看项目结构
   ./gradlew projects
   
   # 查看任务依赖
   ./gradlew :libHttpClient.Android:dependencies
   
   # 单独构建特定架构
   ./gradlew :libHttpClient.Android:assembleDebug -PANDROID_ABI=arm64-v8a
   ```

## 自定义配置

### 修改构建配置
要修改构建配置，可以编辑工作流文件中的以下部分：

```yaml
strategy:
  matrix:
    configuration: [Debug, Release]  # 添加或移除配置
    platform: [x64]                 # 添加或移除平台
```

### 添加新的构建步骤
在相应的工作流文件中添加新的步骤：

```yaml
- name: 自定义构建步骤
  run: |
    # 你的构建命令
```

### 修改构建产物
编辑 "Upload build artifacts" 步骤中的 `path` 部分来包含或排除特定文件。

## 贡献

如果你需要修改这些工作流文件，请：

1. 创建一个新分支
2. 修改工作流文件
3. 测试修改（可以通过手动触发工作流）
4. 提交 Pull Request

确保任何修改都经过充分测试，并且不会破坏现有的构建流程。