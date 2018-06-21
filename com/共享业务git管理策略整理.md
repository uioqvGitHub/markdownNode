共有  stable_test, prepub, master三个主要分支

feature, hotfix 两种辅助分支

### 1. 创建/合并约定

#### 原则：不同上线时间点的需求分开创建开发分支

1. feature分支从master创建，   后续合并到 stable-test, prepub, master分支, 
2. hotfix分支从master创建， 后续合并到 prepub, master分支
3. stable-test, prepub分支 只允许合并代码进来， 不能合并代码出去

### api版本约定

#### 分支版本命名： master版本+需求名称

| 开发                 | 测试                       | 预发                         | 线上           |修复|
| -------------------- | -------------------------- | ---------------------------- | -------------- |---------------|
| 1.0.0-name1-snapshot | 1.0.0-stable-test-snapshot | 1.0.0-stable-prepub-snapshot | 1.0.0-snapshot |1.0.0-name1-snapshot|
|feature/feature-name1|feature/feature-stable-test|prepub|master|hotfix/hotfix-name1|

?:线上版本建标签还是分支

1. 开发分支版本为 最新master版本+1
2. 每次开发分支代码合并到stable-test, prepub需要  `mvn clean deplog`;

