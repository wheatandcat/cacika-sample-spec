# cacika-sample-spec

[cacika](https://cacika.net) の「リリース進行（β版機能）」の集計を試すためのサンプルです。
Todo List の webサービスを作る、という想定で、仕様 issue・開発 issue・PR・テスト issue を 3 つのリポジトリと 1 つの GitHub Project に分けて置いています。

> 1 日でまとめて作ったため、各フェーズの所要日数は 0 日前後になります。

## 構成

| 種別 | 場所 | config.yaml の項目 |
| --- | --- | --- |
| GitHub Project | [cacika sample: Todo List](https://github.com/users/wheatandcat/projects/3) | `release_progress.github_project_number` |
| 仕様 issue | [wheatandcat/cacika-sample-spec](https://github.com/wheatandcat/cacika-sample-spec/issues)（このリポジトリ） | `release_progress.spec_repositories` |
| 開発 issue・PR | [wheatandcat/cacika-sample-todo](https://github.com/wheatandcat/cacika-sample-todo) | `release_progress.dev_repositories` / `github_repositories` |
| テスト issue | [wheatandcat/cacika-sample-qa](https://github.com/wheatandcat/cacika-sample-qa/issues) | `release_progress.test_repositories` |

## 集計に乗せるためのルール

cacika は、次の順に issue と PR のつながりをたどって集計します。

```
GitHub Project に追加した仕様 issue
  └ 開発 issue   … 本文に仕様 issue の URL を書く（sub-issue も含めて集計される）
      └ 開発 PR  … 本文に「Closes #開発 issue 番号」を書いてマージする
          └ テスト issue … 本文に開発 PR の URL を書く
```

1. 仕様 issue を作り、GitHub Project に追加する
2. 開発 issue の本文に、仕様 issue の URL を書く（例: [cacika-sample-todo#6](https://github.com/wheatandcat/cacika-sample-todo/issues/6)）
   - 作業を分けたいときは、開発 issue に sub-issue を付ける（例: [cacika-sample-todo#1](https://github.com/wheatandcat/cacika-sample-todo/issues/1)）。sub-issue には仕様 issue の URL を書かなくてよい
3. 開発 PR の本文に `Closes #<開発 issue 番号>` を書く（例: [cacika-sample-todo#8](https://github.com/wheatandcat/cacika-sample-todo/pull/8)）
4. テスト issue の本文に、開発 PR の URL を書く（例: [cacika-sample-qa#3](https://github.com/wheatandcat/cacika-sample-qa/issues/3)）
5. リリースしたら仕様 issue をクローズする。open のままの仕様 issue は「進行中」として表示される

## 仕様 issue の一覧

| 仕様 issue | 状態 | 見どころ |
| --- | --- | --- |
| [#1 Todo の追加と一覧表示](https://github.com/wheatandcat/cacika-sample-spec/issues/1) | 完了 | 開発 issue を sub-issue に分けた例 |
| [#2 Todo の完了と削除](https://github.com/wheatandcat/cacika-sample-spec/issues/2) | 完了 | リリーステストで不具合が見つかり、`qafix` ラベルの issue で修正した例。`[dev]` で始まるテスト issue は集計から除外される |
| [#3 Todo をブラウザに保存する](https://github.com/wheatandcat/cacika-sample-spec/issues/3) | 完了 | 開発 issue・PR・テスト issue が 1 つずつの最小構成 |
| [#4 Todo の絞り込みとタグ](https://github.com/wheatandcat/cacika-sample-spec/issues/4) | 進行中 | 開発 issue の 1 つがまだ open（PR レビュー中） |

## config.yaml の例

```yaml
github_owner: "wheatandcat"

github_repositories:
  - name: "cacika-sample-todo"
    base_branch: "main"
    qafix_labels:
      - "qafix"

release_progress:
  github_project_number: 3
  github_project_owner: "wheatandcat"
  project_owner_type: "user"
  spec_repositories:
    - "cacika-sample-spec"
  dev_repositories:
    - "cacika-sample-todo"
  test_repositories:
    - name: "cacika-sample-qa"
      exclude_prefix_issue_titles:
        - "[dev]"
```

GitHub トークンには、公開リポジトリの読み取り権限と、Project の読み取り権限（`read:project`）が必要です。
