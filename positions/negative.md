# 否定派（論理削除反対）基本主張

## 立場の要約

論理削除（削除フラグ）は、多くの場合アンチパターンである。一見便利に見えるが、実装・運用の複雑化、バグの温床、設計の歪みを招く。より適切な代替手段を採用すべき。

---

## 主要な主張

### 1. バグの温床となる

**WHERE句の付け忘れは必ず発生する**

- 「削除フラグだけはガチやめといたほうがいい」という実務者の声
- 全てのクエリで削除フラグを考慮する必要があり、見落としが頻発
- コメントアウトされたまま放置されるコードと同じ問題

> 参考: [Yahoo!リアルタイム検索での議論](https://search.yahoo.co.jp/realtime/search?p=%E8%AB%96%E7%90%86%E5%89%8A%E9%99%A4)

### 2. データベース設計の複雑化

**UNIQUE制約・外部キーが機能しなくなる**

- メールアドレスのユニーク制約が期待通りに動かない
- 削除済みユーザーと同じメールアドレスで再登録できない問題
- 外部キー制約の意味が失われ、参照整合性が保証されない

> 参考: [論理削除はアンチパターンなのか？ - Qiita](https://qiita.com/o-yyu-o/items/e46079dd8f93c75ca2b6)

### 3. パフォーマンスへの悪影響

**データ肥大化と検索性能の劣化**

- 削除されたレコードがDBに残り続け、容量を圧迫
- インデックスのカーディナリティが悪化
- 大規模テーブルでのクエリ性能が劣化

> 参考: [論理削除フラグという名の死亡フラグ - @ledsun blog](https://ledsun.hatenablog.com/entry/2015/03/27/015203)

### 4. 「論理削除」という概念自体の問題

**顧客の本当の要求ではない**

- 「論理削除という概念は世の中にはない概念で、システム屋特有の言葉」
- 顧客は「削除」を要求しているのではなく、「状態変更」を要求している
- 「退職」は「削除」ではなく「情報の追加」

> 参考: [fukabori.fm ep27 - 論理削除とは何か？](https://fukabori.fm/episode/27)

### 5. 削除復旧はほとんど使われない

**理論上のメリットは実践されない**

- 「10年以上、どの会社でも論理削除から実際にデータを復元したことは一度もない」
- 復元が必要なケースは極めて稀
- バックアップからの復旧で十分対応可能

> 参考: [Soft Deletion Probably Isn't Worth It - brandur.org](https://brandur.org/soft-deletion)

---

## 代替手段の提案

### 1. 状態（State）フィールド

```sql
-- 削除フラグではなく、状態として管理
ALTER TABLE users ADD COLUMN status ENUM('active', 'inactive', 'withdrawn');
```

- 「削除」ではなく「退職」「退会」「無効」という状態
- ビジネス要件に忠実なモデリング

### 2. アーカイブテーブル

```sql
-- 削除時にアーカイブテーブルに移動
INSERT INTO users_archive SELECT * FROM users WHERE id = ?;
DELETE FROM users WHERE id = ?;
```

- 本テーブルはクリーンに保たれる
- 履歴は別テーブルで管理

### 3. イベントソーシング

- 全ての操作を「事実」として記録
- INSERT と SELECT のみ、UPDATE/DELETE なし
- 改ざん不可能な監査証跡

> 参考: [[fukabori fm ep27より] 論理削除とその解法メモ - Qiita](https://qiita.com/smith-30/items/3b50b82be6eda95b07d0)

---

## 肯定派への反論準備

| 肯定派の主張 | 反論 |
|-------------|------|
| データ復旧が容易 | 実際にはほとんど使われない。バックアップで十分 |
| 監査要件対応 | 監査ログ・アーカイブテーブルの方が適切 |
| 参照整合性維持 | アーカイブ+カスケード設計で解決可能 |
| ビュー/ORMで隠蔽 | 隠蔽コストが高く、漏れが発生する |

---

## 参考資料

- [論理削除はアンチパターンなのか？ - Qiita](https://qiita.com/o-yyu-o/items/e46079dd8f93c75ca2b6)
- [fukabori.fm ep27 - 論理削除とは何か？](https://fukabori.fm/episode/27)
- [Why soft deletes are evil - James Halsall](https://jameshalsall.co.uk/posts/why-soft-deletes-are-evil-and-what-to-do-instead)
- [Soft Deletion Probably Isn't Worth It - brandur.org](https://brandur.org/soft-deletion)
- [削除フラグの落とし穴 - PHPerKaigi 2025](https://fortee.jp/phperkaigi-2025/proposal/1c6ca267-8832-48bf-90f7-6d6389a15fc5)
