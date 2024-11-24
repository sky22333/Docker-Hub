### 工作流默认变量`GITHUB_TOKEN`的提权语法



| 权限类别           | 资源类型              | 权限级别   | 说明                                                                                          | 配置语法示例                                                                                       | 示例                                                               |
|--------------------|-----------------------|------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| **contents**        | 仓库内容（代码、文件）  | `read`     | 只读权限，允许读取仓库内容，如查看文件和提交历史。                                               | `contents: read`                                                                                 | 允许查看仓库文件和提交历史。                                         |
|                    |                       | `write`    | 写权限，允许修改、推送、更改文件。                                                               | `contents: write`                                                                                | 允许推送代码、修改文件、创建新的提交。                               |
|                    |                       | `delete`   | 删除权限，允许删除仓库内容。                                                                     | `contents: delete`                                                                               | 允许删除仓库中的文件和内容。                                         |
| **metadata**        | 仓库元数据（标签、Release）| `read`     | 只读权限，允许读取仓库的标签和 Release 信息。                                                    | `metadata: read`                                                                                 | 允许读取 Release 和标签信息。                                        |
|                    |                       | `write`    | 写权限，允许创建、更新或删除仓库的标签、Release 等元数据。                                      | `metadata: write`                                                                                | 允许创建、修改、删除 Release 和标签。                               |
| **packages**        | GitHub 容器（GHCR）     | `read`     | 只读权限，允许读取容器镜像或包。                                                                  | `packages: read`                                                                                 | 允许拉取 Docker 镜像或其他包。                                       |
|                    |                       | `write`    | 写权限，允许推送容器镜像或其他包。                                                                | `packages: write`                                                                                | 允许推送 Docker 镜像到 GHCR 或上传其他包。                          |
|                    |                       | `delete`   | 删除权限，允许删除 GitHub 容器中的镜像或包。                                                      | `packages: delete`                                                                               | 允许删除容器中的镜像或包。                                           |
| **actions**         | GitHub Actions         | `read`     | 只读权限，允许查看工作流和运行状态。                                                              | `actions: read`                                                                                  | 允许查看工作流执行状态和日志。                                       |
|                    |                       | `write`    | 写权限，允许触发、管理和更新工作流的状态。                                                        | `actions: write`                                                                                 | 允许触发、暂停、取消、重新触发工作流。                               |
|                    |                       | `delete`   | 删除权限，允许删除工作流运行历史记录。                                                             | `actions: delete`                                                                                | 允许删除工作流运行历史记录。                                         |
| **issues**          | 问题（Issue）           | `read`     | 只读权限，允许查看、评论问题。                                                                   | `issues: read`                                                                                   | 允许查看问题和评论。                                                 |
|                    |                       | `write`    | 写权限，允许创建、编辑、关闭问题。                                                               | `issues: write`                                                                                  | 允许创建、编辑、关闭问题。                                           |
| **pull-requests**   | 拉取请求（PR）          | `read`     | 只读权限，允许查看和评论 PR。                                                                   | `pull-requests: read`                                                                             | 允许查看拉取请求和评论。                                             |
|                    |                       | `write`    | 写权限，允许创建、更新、合并拉取请求。                                                           | `pull-requests: write`                                                                            | 允许创建、更新、合并 PR。                                           |
| **workflows**       | 工作流（workflow）      | `read`     | 只读权限，允许查看工作流定义和状态。                                                             | `workflows: read`                                                                                 | 允许查看工作流和工作流运行的状态。                                   |
|                    |                       | `write`    | 写权限，允许触发、管理、更新工作流。                                                             | `workflows: write`                                                                                | 允许触发、更新、暂停和管理工作流。                                   |
| **commit-status**   | 提交状态                | `read`     | 只读权限，允许查看提交的状态。                                                                   | `commit-status: read`                                                                             | 允许查看提交的状态。                                                |
|                    |                       | `write`    | 写权限，允许设置提交的状态。                                                                     | `commit-status: write`                                                                            | 允许设置提交的状态。                                                |
| **contents**        | 文件树和内容操作        | `read`     | 只读权限，允许查看文件、目录等内容。                                                             | `contents: read`                                                                                 | 允许查看文件、目录结构及内容。                                       |
|                    |                       | `write`    | 写权限，允许修改、创建或删除文件。                                                               | `contents: write`                                                                                | 允许修改、创建、删除文件。                                           |
| **deployments**     | 部署操作                | `read`     | 只读权限，允许查看部署的状态。                                                                   | `deployments: read`                                                                               | 允许查看部署的状态和历史。                                           |
|                    |                       | `write`    | 写权限，允许触发部署操作。                                                                       | `deployments: write`                                                                              | 允许触发部署操作，更新部署状态。                                     |

### 示例：完整的权限配置

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write    # 允许推送代码，修改仓库文件
      metadata: write    # 允许操作仓库的 Release 和标签
      packages: write    # 允许推送 Docker 镜像到 GitHub 容器注册表
      actions: read      # 允许查看工作流执行状态和日志
      workflows: write   # 允许触发和管理工作流
      issues: write      # 允许创建、编辑和关闭问题
      pull-requests: write # 允许创建和合并拉取请求
