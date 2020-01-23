# GitLab CI 部署
***
*部署环境 Centos7*
## 安装
### gitlab-ci-multi-runner
```sh
curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.rpm.sh | sudo bash
yum install gitlab-ci-multi-runner
```

### 获取项目的地址、token
- 点进项目
- Setting
- CI/CD
- runner
- Set up a specific Runner manually

### 设置项目地址、token
```sh
gitlab-ci-multi-runner register

Please enter the gitlab-ci coordinator URL: gitlab地址
Please enter the gitlab-ci token for this runner: 项目的token
Please enter the gitlab-ci description for this runner ：描述，可以进行修改
Please enter the gitlab-ci tags for this runner (comma separated): runner的标签，需要慎重填写，gitlab CI 配置文件根据标签定位runner
Whether to run untagged builds [true/false]: 询问是否在未加标签的Build上运行 true
Please enter the executor: 询问是否锁定最近项目  true
```

### 运行
```sh
gitlab-runner restart | start | stop
```

## 项目配置
在项目根目录下新建文件：.gitlab-ci.yml（gitlab会自动识别），填入下面的内容

```sh
stages:
  - deploy
deploy:
  stage: deploy # 对应stages的步骤
  script: # 执行的命令
    - echo "start deploy"
    - cd /home/nssas/projects/Visual
    - mvn clean package -Dmaven.test.skip=true
    - echo "end deploy"
  only:
    - master # 设置master分支提交的时候触发
  tags:
    - master # 触发标签为master的runner
```

## 运行自动集成
*需要确保文件的权限正确，chown gitlab-runner:gitlab-runner /home/nssas/projects/Visual/target/ -R*
&ensp;&ensp;&ensp;&ensp;代码进行提交即可进行自动集成

## 持续集成的步骤
- 复制主分支用于提交代码测试（CI的触发条件是提交代码，如果失败了那代码就处于异常状态，导致不能允许，当测试分支通过以后合并）
- 检查代码格式是不是符合规范（这个怎么运行这个检查？这种应该放到本地）,Alibaba Java Coding Guidelines
- 运行单元测试代码（测试覆盖率这个怎么搞？这种应该放到本地）
- 运行集成测试（脚本实现？复杂的测试放到集成服务器上）
- 部署上线流程（DevOps？生成Docker Image，对Image进行测试？）

- 如果保证测试唯一性，在测试不通过的时候不能提交代码？
- GitLab有可视化的集成监视器吗？

## 参考链接
- [Linux下递归改变目录及其子目录属性](http://blog.sciencenet.cn/blog-350278-743765.html)
- [超详细Gitlab Runner环境配置中文教程](https://blog.csdn.net/u011215669/article/details/80446624)
- [利用GitLab提供的GitLab-CI以及GitLab-Runner搭建持续集成/部署环境](https://juejin.im/entry/5ad8627d6fb9a045d639b043)
- [GitLab-CI与GitLab-Runner](https://www.jianshu.com/p/2b43151fb92e)
- [GitLab-CI 从安装到差点放弃](https://segmentfault.com/a/1190000007180257)
- [Git gitlab私库clone地址](https://blog.csdn.net/benpaodelulu_guajian/article/details/83822059)