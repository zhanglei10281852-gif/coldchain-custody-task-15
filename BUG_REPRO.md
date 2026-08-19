# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

运输单已有一条待确认交接，但对账结果显示没有待办交接，也没有对应阻塞项。请先不要修改代码，定位数据库计数如何转换为对账状态以及单条待办为何被忽略，并给出证据。 调查全程不要修改目标仓库中的生产代码、测试代码或配置。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/coldchain-custody-task-15
- 仓库地址：https://github.com/zhanglei10281852-gif/coldchain-custody-task-15.git
- parent SHA：badbd7a8b88f4489123cb3865aa4e16ff4aad051

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/coldchain-custody-task-15.git bug-repro
cd bug-repro
git checkout --detach badbd7a8b88f4489123cb3865aa4e16ff4aad051
go test ./internal/service -run "^TestReconciliationShowsPendingHandoff$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestReconciliationShowsPendingHandoff$" -count=1
--- FAIL: TestReconciliationShowsPendingHandoff (0.49s)
    annotation_behavior_test.go:184: reconciliation = {ShipmentID:ship_3160dcc6905af64740b8b75f ShipmentState:dispatched ExpectedBatchCount:1 ReceivedBatchCount:0 ReleasedBatchCount:0 DestroyedBatchCount:0 QuarantinedCount:0 PendingHandoff:false OpenExcursion:false LastReadingAt:<nil> Complete:false Blockers:[]}
FAIL
FAIL	github.com/zhanglei10281852-gif/coldchain-custody-base/internal/service	0.493s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestReconciliationShowsPendingHandoff$" -count=1
--- FAIL: TestReconciliationShowsPendingHandoff (1.20s)
    annotation_behavior_test.go:184: reconciliation = {ShipmentID:ship_d83bf6d5dc7dc0aa47cc132b ShipmentState:dispatched ExpectedBatchCount:1 ReceivedBatchCount:0 ReleasedBatchCount:0 DestroyedBatchCount:0 QuarantinedCount:0 PendingHandoff:false OpenExcursion:false LastReadingAt:<nil> Complete:false Blockers:[]}
FAIL
FAIL	github.com/zhanglei10281852-gif/coldchain-custody-base/internal/service	1.384s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

## 通过条件

准确定位根因，指出具体 Go 文件和符号，解释错误行为如何导致题面症状，并给出实际复现、调用链或持久化证据。 完成时目标仓库代码、测试和配置零改动。
