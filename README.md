# 自分用のAWSの便利Tips

## IAMポリシー編

### CloudWatchLogsにログを吐き出したい時
以下ののものをIAM ポリシーに追加する。

```
logs:PutLogEvents
logs:CreateLogStream
```