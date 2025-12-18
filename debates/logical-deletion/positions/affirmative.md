# 肯定派（論理削除支持）基本主張

## 立場の要約

論理削除（削除フラグ）は、適切に設計・運用すれば有効なパターンである。物理削除では得られない重要なメリットがあり、ビジネス要件によっては最適な選択肢となる。

---

## 主要な主張

### 1. データ復旧の容易性

**誤削除からの即座の復旧が可能**

- 削除フラグを戻すだけで、データを完全に復元できる
- 物理削除の場合、バックアップからの復旧は時間とコストがかかる
- 「うっかり削除」は現実のシステムで頻繁に発生する

> 参考: [1分でわかる論理削除 - Qiita](https://qiita.com/wanko5296/items/f1af9c7bf020e867c2dd)

### 2. 監査・コンプライアンス要件への対応

**履歴追跡と監査証跡の確保**

- 「誰が」「いつ」「何を」削除したかの記録が残る
- 金融・医療・法的要件で求められる監査証跡を満たせる
- GDPR等の規制においても、一定期間のデータ保持が必要な場合がある

> 参考: [To Delete or to Soft Delete - Jmix](https://www.jmix.io/blog/to-delete-or-to-soft-delete-that-is-the-question/)

### 3. 参照整合性の維持

**関連データの整合性を保護**

- 外部キーで参照されているレコードを物理削除すると、整合性が崩れる
- 長期実行ビジネスプロセスで必要なデータを保持できる
- カスケード削除による意図しないデータ損失を防止

> 参考: [DZone - To Delete or to Soft Delete](https://dzone.com/articles/to-delete-or-to-soft-delete-that-is-the-question)

### 4. 可用性グループでの利点

**分散システムでの運用メリット**

- 高速な削除取り消し（undelete）
- 削除履歴の追跡
- 災害復旧時のフェイルオーバーでの照合が容易
- セカンダリレプリカへのワークロード軽減

> 参考: [DoltHub - So you want Soft Deletes?](https://www.dolthub.com/blog/2022-11-03-soft-deletes/)

### 5. ビジネスロジックとの整合性

**削除は「状態変更」として自然**

- ユーザーアカウントの「削除」は、実際には「無効化」「退会」という状態
- 記事の「削除」は「非公開」という状態に近い
- ビジネス的には「存在しなくなる」のではなく「見えなくなる」ことが多い

---

## 批判への反論準備

| 批判 | 反論 |
|------|------|
| WHERE句の付け忘れ | ビュー、ORM、リポジトリパターンで隠蔽可能 |
| UNIQUE制約の問題 | 部分インデックス、複合キーで解決可能 |
| パフォーマンス劣化 | パーティショニング、定期的なアーカイブで対処可能 |
| データ肥大化 | 保持期間後の物理削除ポリシーで管理可能 |

---

## 適用が推奨されるケース

1. **金融システム**: 取引履歴の完全な保持が法的に必要
2. **医療システム**: 患者記録の改ざん防止と監査対応
3. **ECサイト**: 注文履歴に紐づく商品・ユーザー情報の保持
4. **エンタープライズシステム**: 承認ワークフローの履歴追跡

---

## 参考資料

- [1分でわかる論理削除 - Qiita](https://qiita.com/wanko5296/items/f1af9c7bf020e867c2dd)
- [To Delete or to Soft Delete - Jmix](https://www.jmix.io/blog/to-delete-or-to-soft-delete-that-is-the-question/)
- [Soft Deletes vs Hard Deletes - HiBit](https://www.hibit.dev/posts/129/soft-deletes-vs-hard-deletes-making-the-right-choice)
- [DoltHub - So you want Soft Deletes?](https://www.dolthub.com/blog/2022-11-03-soft-deletes/)
