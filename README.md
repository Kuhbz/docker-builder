# Docker Builder

一个中央化的、按需服务的 Docker 镜像构建中心。

## 功能概述

这个仓库提供了一个通用的 GitHub Actions 工作流程，让你可以：

1. **手动触发**: 通过在 GitHub 页面的表单中填写参数，来启动一个构建任务
2. **构建任何仓库**: 指定任何你有权访问的 GitHub 仓库地址、代码分支或标签
3. **灵活配置**: 定义要使用的 Dockerfile 路径和最终生成的镜像名称
4. **统一输出**: 将构建好的镜像自动推送到统一的 GitHub 容器镜像仓库 (GHCR) 中

## 使用方法

### 1. 手动触发构建

1. 访问此仓库的 GitHub 页面
2. 点击 "Actions" 标签
3. 选择 "Docker Image Builder" 工作流程
4. 点击 "Run workflow"
5. 填写以下参数：
   - **repository_url**: 要构建的 GitHub 仓库地址 (例如: `owner/repo`)
   - **ref**: 分支、标签或提交哈希 (例如: `main`, `v1.0.0`)
   - **dockerfile_path**: Dockerfile 路径 (例如: `./Dockerfile`)
   - **image_name**: 最终镜像名称 (例如: `my-app`)
   - **image_tag**: 镜像标签 (例如: `latest`, `v1.0.0`)

### 2. 参数说明

| 参数 | 必需 | 默认值 | 说明 |
|------|------|--------|------|
| `repository_url` | 是 | - | GitHub 仓库地址，格式为 `owner/repo` |
| `ref` | 是 | `main` | 分支名、标签或提交哈希 |
| `dockerfile_path` | 是 | `./Dockerfile` | 相对于仓库根目录的 Dockerfile 路径 |
| `image_name` | 是 | - | 最终生成的镜像名称 |
| `image_tag` | 是 | `latest` | 镜像标签 |

### 3. 构建输出

- 构建完成后，镜像会自动推送到 `ghcr.io/${{ github.repository_owner }}/${{ image_name }}:${{ image_tag }}`
- 你可以在 GitHub Packages 中查看所有构建的镜像
- 构建日志会显示完整的镜像地址和拉取命令

## 权限要求

此工作流程需要以下权限：
- `contents: read` - 读取源代码仓库
- `packages: write` - 推送到容器镜像仓库

## 示例使用场景

### 场景 1: 构建 main 分支的应用

```
repository_url: my-organization/my-app
ref: main
dockerfile_path: ./Dockerfile
image_name: my-app
image_tag: latest
```

### 场景 2: 构建特定版本的应用

```
repository_url: my-organization/my-app
ref: v2.1.0
dockerfile_path: ./docker/Dockerfile.prod
image_name: my-app
image_tag: v2.1.0
```

### 场景 3: 构建第三方仓库的应用

```
repository_url: docker-library/nginx
ref: main
dockerfile_path: ./1.25/Dockerfile
image_name: nginx-custom
image_tag: latest
```

## 工作原理

1. **源码检出**: 工作流程首先会检出你指定的仓库和分支
2. **Docker 构建**: 使用指定的 Dockerfile 进行镜像构建
3. **镜像推送**: 将构建好的镜像推送到 GitHub Container Registry
4. **缓存优化**: 使用 GitHub Actions 缓存来加速构建过程

## 安全考虑

- 此工作流程只能构建你有权限访问的仓库
- 推送的镜像会继承你的 GitHub 权限
- 建议定期清理不再需要的镜像版本

## 故障排除

如果构建失败，请检查：
1. 仓库地址是否正确
2. 分支/标签是否存在
3. Dockerfile 路径是否正确
4是否有足够的权限访问目标仓库

## 许可证

此项目采用 MIT 许可证。