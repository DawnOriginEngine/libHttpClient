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

## 构建要求

### 系统要求
- Windows 最新版本的 GitHub Actions runner
- Visual Studio 2022 (v143 工具集)
- MSBuild
- NuGet

### 依赖项
- Git 子模块（自动递归克隆）
- OpenSSL（作为项目的一部分构建）
- Windows SDK 10.0.19041.0

## 构建产物

构建完成后，以下产物将被上传为 GitHub Actions artifacts：

- **库文件**: `*.lib` 文件
- **可执行文件**: `*.exe` 文件
- **调试信息**: `*.pdb` 文件
- **动态链接库**: `*.dll` 文件（如果有）

产物保留期为 30 天。

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

### 调试技巧

1. **启用详细日志**
   - 将 `/verbosity:minimal` 改为 `/verbosity:normal` 或 `/verbosity:detailed`

2. **检查构建输出**
   - 使用 "List build outputs" 步骤查看生成的文件

3. **本地复现**
   - 使用相同的 MSBuild 命令在本地环境中复现问题

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