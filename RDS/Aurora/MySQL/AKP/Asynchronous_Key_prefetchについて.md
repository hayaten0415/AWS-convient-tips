# Asynchronous Key Prefetch 
----------

## Asynchronous Key Prefetch(AKP)とは
Amazon Aurora MySQLにおいて、**buffer poolやquery cacheにない場合**のBatched Key Access(BKA)Joinアルゴリズムと
Multi-Range Read(MRR)最適化を使⽤するクエリのJOIN性能を10倍にするもの。Auroraのバージョン1.15から使用可能。<br/>


## 使用するために
- MySQLサーバー変数 aurora_use_key_prefetch を onにする
* BKA 結合アルゴリズムを有効にして、コストベースの MRR 機能を無効にしなければ上の変数をonにできない

**※セッションレベルでのみ設定可能**

### 以下のように設定
|パラメータ|設定値|
|-----|----|
|aurora_use_key_prefetch|on|
|batched_key_access|on|
|mrr_cost_based|off|

AKPを有効にする場合
```
mysql> set @@session.aurora_use_key_prefetch=on;
mysql> set @@session.optimizer_switch='batched_key_access=on,mrr_cost_based=off';
```

元に戻す場合
```
mysql> set @@session.aurora_use_key_prefetch=off;
mysql> set @@session.optimizer_switch='batched_key_access=off,mrr_cost_based=on';
```

### AKPが適用可能なクエリを調べる方法
```EXPLAIN EXTENDED```を実行しExtra列に以下の値のいずれかが含まれていること
- Using Key Prefetching
- Using join buffer (Batched Key Access with Key Prefetching)

-------
# BKAアルゴリズム
BKAアルゴリズムを使用する場合join_buffer_sizeの値によって、ストレージエンジンへの個々のリクエストでの<br/>
キーのバッチの大きさが定義される。(MySQL公式ドキュメントより)<br/>
**AuroraとMySQLはアーキテクチャが違うので値を変えてうまくいくかは分からない**

## join_buffer_size
- スレッドバッファのひとつ。Aurora MySQLのパラメータグループでも変更可能。
- インデックスを使用しないために、フルスキャンを実行するJOINで使用されるバッファサイズ
- BKAを使用しない限り、この値を大きくする利点は少ない
- MySQL5.7でのデフォルト値は262144(256KiB)

##### 参考リンク
- https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.BestPractices.html#Aurora.BestPractices.AKP
- https://aws.amazon.com/jp/about-aws/whats-new/2017/10/amazon-aurora-mysql-speeds-join-queries-by-more-than-10x-with-asynchronous-key-prefetch/
- https://qiita.com/hmatsu47/items/3f9e06edfae803835d5e
- https://dev.mysql.com/doc/refman/5.7/en/bnl-bka-optimization.html
- https://aws-ref.s3.amazonaws.com/aurora/Amazon+Aurora.pdf
- https://sawara.me/mysql/1428/
- https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Reference.html#AuroraMySQL.Reference.Parameters.Instance
- https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size



