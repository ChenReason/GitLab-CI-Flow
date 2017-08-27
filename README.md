# GitLab-CI-Flow

## 脚本作用：
针对 iOS 项目或者 pod 项目发布流程繁琐且混乱制定的一套测试发布上线的 git flow 工作流。

## 脚本依赖：
* [Gitlab-CI](https://about.gitlab.com/features/gitlab-ci-cd/)
* [Gitlab-Runnber](https://docs.gitlab.com/runner/)
* [Fastlane](https://github.com/fastlane/fastlane)
* branch_info 文件

## 发布流程介绍：
`前提`：项目 tag 标准及格式为 x.y.z，feature merge 升 x，refactor merge 升 y，bugfix merge 升 z。

`分支`：所有项目一律会有三个受保护分支，分别是 master、develop 和 release，master 用于发布上线包，release 用于发布内部封测包，
develop 用于平时开发作为主分支。

`要求`：
* feature 分支必须从 develop 分支 checkout 出来
* feature 分支只能向 develop 分支提交 merge request
* bugfix 和 refactor 分支必须从 release 分支 checkout 出来
* bugfix 和 refactor 分支也只能向 release 分支提交 merge request

`各分支关系及流程图`：

               +-----------+  update  +-----------+checkout +-----------+
               |           |+-------->|           |+------->|           |
               |  master   |          |  develop  |         |  feature  |
               |           |          |           |<-------+|           |
               +-----------+          +-----------+  merge  +-----------+
                    ^                       +
                    |                       |
                    |                       |merge
                    |                       |
                    |                       v
                    |                 +-----------+checkout +-----------+
                    |      merge      |           |+------->|           |
                    +----------------+|  release  |         |  refactor |
                                      |           |<-------+|           |
                                      +-----------+  merge  +-----------+
                                         ^     +
                                         |     |
                                    merge|     |checkout
                                         |     |
                                         +     v
                                      +-----------+
                                      |           |
                                      |  bugfix   |
                                      |           |
                                      +-----------+
`脚本的作用`：
* feature、bugfix 和 refactor 发生变动检查 branch_info 文件是否有进行同步更改
* 当有分支成功 merge 之后，自动更新 tag
* master 分支有更新时，会自动同步代码到 develop 分支
