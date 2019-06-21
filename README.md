# 自分用のAWSの便利Tips

## IAMポリシー編

### CloudWatchLogsにログを吐き出したい時
以下ののものをIAM ポリシーに追加する。

```
logs:PutLogEvents
logs:CreateLogStream
```

## VPC

### NATインスタンス
NATインスタンスを使用する場合は送信元チェック機能を無効にする<br>
https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/VPC_NAT_Instance.html#EIP_Disable_SrcDestCheck