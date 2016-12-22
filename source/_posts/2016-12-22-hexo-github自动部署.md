---
title: hexo+github自动部署
date: 2016-12-22 18:06:11
tags: hexo,github,travis-ci
---

## 相关知识

1. github
2. hexo
3. travis-ci
4. markdown

## 配置步骤

> 假设你已经在github上有了两个项目分别为site和yourgithubusername.github.io(一下称项目web),site项目为hexo的博客项目,web为我们要部署的hexo静态文件项目,我想现在要实现的是我们在site项目中写了一偏新的文章并提交到site项目中,自动发布更新到web项目实现自动部署

## travis配置

```
# 自动化的环境hexo所需要的环境
language: node_js

node_js:
  - "4"

cache:
  apt: true
  directories:
    - node_modules

addons:
  apt:
    packages:
      - graphicsmagick

env:
  - CXX=g++-4.8

addons:
  apt:
    sources:
      - ubuntu-toolchain-r-test
    packages:
      - g++-4.8
  ssh_known_hosts: github.com

before_install:
  # 配置git的配置（你的github配置）
  - git config --global user.name "yourgithubusername"
  - git config --global user.email "yourgithubuseremail"
  - git config --global push.default simple

script:
  # 从github克隆web项目
  - git clone https://${GH_TOKEN}@github.com/yourgithubname/yourgithubname.github.io.git .deploy_git -v
  # Update docs.
  - git --version
  # Install dependencies
  - npm install --no-optional
  # - npm info hexo-renderer-marked

after_script:
  # hexo生成博客静态文件
  - hexo generate
  - cp -r public/* .deploy_git/
  - cd .deploy_git
  # 提交生成后的博客静态文件到web项目
  - git add -A
  - git commit -am "Auto deploy from Travis-CI."
  - git push --force

branches:
  only:
    - source
```

## github配置

1. token生成https://github.com/settings/tokens页面生成token
2. 


