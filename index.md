# Git Command

## 基本

### 初期化
```sh
git init
```

### ファイルをステージング（インデックス）する
`git add`することでバージョン管理の対象になる

#### カレントディレクトリ以下をステージングする
```sh
git add .
```

#### バージョン管理されているファイルの変更（削除も）のみをステージングする
新しく作成した（追跡していない）ファイルはステージングされない
```sh
git add --update
# または
git add -u
```

### コミット
```sh
git commit -m "メッセージ"
```
`commit`は`記憶に留める・格納する`といった意味

---
## コミット前の確認

### 変更を表示
```sh
git status
```
> `git status`コマンドの主な機能
> 
> - **変更されたファイルの表示**:
>     - `Changes not staged for commit`（ステージングされていない変更）: `git add`する前の変更ファイル
>     - `Changes to be committed`（コミットされる変更）: `git add`されてステージングエリアにあるファイル
> - **未追跡ファイルの表示**: Gitがまだ管理していない（`git add`していない）新規ファイル（`Untracked files`）を表示
> - **現在のブランチ表示**: 現在いるブランチ名を表示
> - **リモートブランチとの差分**: リモートリポジトリとローカルブランチの差分（`ahead`/`behind`）を表示


### gitが追跡しているファイルを表示

#### 変更（modified）されて、まだ git add されていないファイルを表示
```sh
git ls-files -m
```

#### 削除（deleted）されたファイルを表示
```sh
git ls-files -d
```

### 差分表示

#### ワーキングディレクトリとステージングエリア（インデックス）の差分
まだ`git add`していない変更
```sh
git diff
```

#### ステージングエリアと直前のコミット（HEAD）の差分
`git add`した内容を確認
```sh
git diff --staged
```
`--staged`は`--cached`でもよい

#### ワーキングディレクトリと直前のコミット（HEAD）の差分
`git add`したかどうかにかかわらず、前回のコミットからの変更すべて
```sh
git diff HEAD
```

#### どのファイルが何行追加/削除されたか
```sh
git diff --stat # 現在の変更の統計
git diff --stat <commit1> <commit2> # 2つのコミット間の差分の統計
```
`stat` は `statistics` (統計) の略で、変更の詳細（具体的な内容）ではなく、変更の規模（ファイル数、行数）を示す

---
## コミット前の取り消し

### ファイルの変更（diff の内容）を取り消す

#### 「ステージングしていないファイル」を直前のコミット後まで戻す
```sh
git restore ファイル名
```

#### 全ての「ステージングしていないファイル」を直前のコミット後まで戻す
```sh
git restore .
```

### add（diff --staged の内容）を取り消す

#### ステージング前の状態に戻す
```sh
git restore --staged ファイル名
```

#### さらにGitの管理からも完全に外す（手元には残す）
```sh
git rm --cached ファイル名
```

#### フォルダごとGit管理から外したい（手元には残す）
node_modulesフォルダを外したい場合
```bash
git rm -r --cached node_modules/
```

|コマンド|ファイルの追跡（Track）状態|目的|
|---|---|---|
|**`git rm --cached`**|**追跡を完全にやめる**|今後Gitで管理したくない（後でIgnoreしたい）時|
|**`git restore --staged`**|**追跡は継続する**|「今した `git add`」だけを取り消したい（Unstage）時|

## 「git rm」コマンド
remove（取り除く）
### 1. ファイルも消すし、Gitの追跡もやめる
```sh
git rm ファイル名
```
- ローカルのファイルが物理的に削除され、その削除がステージング（`git add` された状態）される
- `git commit` をすれば、リポジトリから完全に消えたことになる

### 2. ディレクトリ（フォルダ）を中身ごと消す
```sh
git rm  -r ディレクトリ名/
```
「recursive（再帰的）」にフォルダを削除する

### 3. ファイルは手元に残すが、Gitの管理対象からは外す
```sh
git rm --cached ファイル名
```

### 4. 編集中のファイルや、ステージング済みの変更があるファイルを削除
```sh
git rm -f ファイル名
```
- 「force（強制）」の略
- まだコミットしていない変更があるファイルを `git rm` で消そうとするとエラーになる。それを無視して消したい時に使います

### 例：特定の拡張子を一括で削除したい（追跡も解除）

```bash
git rm *.tmp
```

## 「git mv」コマンド

### ファイル名の変更
```bash
git mv old.txt new.txt
```

### ファイルの移動
```bash
git mv ファイル名 ディレクトリ名/
```

### 「git mv」コマンドの動作
例えばファイル名の変更の場合「git mv」は以下の3つのステップを1回で実行します
```bash
mv old.txt new.txt
git rm old.txt
git add new.txt
```

## gitignoreの書き方
.gitignoreファイルに記述するパターンは、行ごとに指定します
- ファイル名: config.yml（config.ymlという名前のファイルを無視）
- ディレクトリ: build/（buildディレクトリとその中身を無視）
- ワイルドカード: temp*（tempで始まるファイルを無視）
- 拡張子: *.log（.logで終わる全てのファイルを無視）
- 否定（例外）: !important.log（*.logで無視するが、important.logは除く）
- コメント: #で始まる行はコメントとして扱われます

---
## コミット管理

### コミット履歴を一覧表示

```sh
git log --oneline --graph --all
```

オプション |              説明
---------- | ------------------------------
--oneline  | １行で表示
--graph    | ブランチ構造を視覚化
--all      | ブランチも含めた履歴
--grep     | "メッセージの文字列"で絞り込み
-p         | diff情報も表示
-数字      | 最新から数字分の履歴
ファイル名      | 特定ファイルの変更履歴

#### 各コミットで変更されたファイルと追加/削除行数の統計を表示

```sh
git log --stat # 履歴の各コミットの統計
git log --stat -2 # 最新2つのコミットの統計
```

#### 期間指定

```sh
git log --since="いつから" --until="いつまで"

git log --since=2.weeks # 過去２週間のコミット一覧
```
特定の日を指定する ("2026-01-10") 、 相対日付を"2 years 1 day 3 minutes ago"のように指定することも可能

### コミット詳細を表示

#### HEAD（現在の作業中ブランチの最新コミット）のあるコミット詳細
```sh
git show
```

#### HEADの2つ前のコミット
```sh
git show HEAD~2
```

#### そのIDのコミット
```sh
git show コミットID
```

#### そのタグがついたコミット
```sh
git show タグ名
```

### タグをつける（version情報など）

#### コミットを指定しないと最新のコミットにつく
```sh
git tag タグ名 （コミットID）
```

#### 注釈付き
```sh
git tag -a タグ名 （コミットID） -m "注釈"
```

#### タグ一覧
```sh
git tag
```

#### ローカルのタグを削除
```sh
git tag -d タグ名 （コミットID）
```

---
## ブランチ

- branch（木の枝）
- ブランチは「別ルートのセーブデータ」
- 実体は、特定のコミットを指し示す移動可能なポインタ

### 主なブランチの種類

#### 1. 恒久的なブランチ（長期間維持するもの）
- メインブランチ (Main/Master): プロジェクトの「幹（trunk）」。常に安定し、リリース可能なコードを管理
- 開発ブランチ (Develop): 次のリリースに向けた開発コードを集約する場所。各トピックブランチはここから分岐し、ここへ合流。 
#### 2. 一時的なブランチ（役割が終われば削除するもの） 
- トピック / フィーチャーブランチ (Topic/Feature): 特定の新機能開発やタスクを行うための枝。
- リリースブランチ (Release): 新バージョンのリリース準備（最終調整、バグ修正、ドキュメント作成）専用のブランチ。準備完了後にメインと開発ブランチへ統合。
- ホットフィックスブランチ (Hotfix): 本番環境で発生した緊急のバグを直ちに修正するために、メインブランチから直接分岐させて作成。 
#### 3. その他の特殊なブランチ
- サポートブランチ (Support): 古いバージョンのメンテナンス（セキュリティパッチの適用など）を継続する場合に利用。
- 環境用ブランチ (Environment): staging や production など、特定のデプロイ先環境の状態を管理するために使われることがあります。 

### ブランチを作成して切り替え

```sh
git switch -C ブランチ名
```

```sh
git switch //切り替えのみ
```

### ブランチ一覧

```sh
git branch
```

#### リモート追跡ブランチの一覧
```sh
git branch -r
```

#### コミットIDとメッセージと追跡リモート付き一覧
```sh
git branch -vv
```

#### リモートも含めた全てのブランチ一覧
```sh
git branch -a
```

#### ブランチ名変更
```sh
git branch -m <old> <new>
```

### ブランチ削除

#### merge済のみ削除
```sh
git branch -d ブランチ名
```

#### merge済でなくても削除
```sh
git branch -D ブランチ名
```

### 親ブランチ（main等）との変更確認

#### コミット表示
```sh
git log 親ブランチ..ブランチ
```

#### 差分表示
```sh
git diff 親ブランチ..ブランチ
```

### ブランチの統合

#### merge - 合流 - (fast-forward,no-ff)

##### 親ブランチへ移動した後
```sh
git merge トピックブランチ
```

##### fast-forwardできる状態でもマージコミットを残す時は
```sh
git merge --no-ff トピックブランチ
```

#### merge (squash)

ブランチを分けた時点から最新commitまでの差分が1つにまとまり、親ブランチにaddされる（commit前）
mergeはされない

```sh
git merge --squash トピックブランチ
```

#### rebase - 付け替え -

変更の起点をmainの最新コミットで置き換える
ローカルでのみ使用推奨

##### トピックブランチブランチにいる状態で
```sh
git rebase main
```

##### mainへ移動した後、merge
```sh
git merge トピックブランチ
```

---
## リモートリポジトリ

### push

#### 上流（upstream）ブランチ（リモート追跡ブランチ）を設定しながらpush
```sh
git push -u origin ブランチ名
```

#### 上流ブランチを設定しておくとorigin mainは省略できる
```sh
git push (origin main)
```

#### ローカルのタグをリモートに同期
```sh
git push origin タグ名
```

#### リモートからタグを削除
```sh
git push origin --delete タグ名
```

### ローカルにクローン

#### リポジトリ名のフォルダが作成され、クローンされる
```sh
git clone リモートリポジトリのクローンURL
```

### リモートリポジトリ

#### リモートリポジトリの一覧
```sh
git remote
```

#### fetchやpushする時のURLも表示
```sh
git remote -v
```

#### ローカルからリモートリポジトリへ接続
```sh
git remote add origin リモートリポジトリのURL
```

#### 既にリモートで削除されているブランチを消す
```sh
git remote pune origin
```

### リモートリポジトリのコミットを取得

#### fetch

##### リモート追跡ブランチ（origin/main）が更新される
```sh
git fetch
```

##### fetchでエラーがあった等で戻したい時
```sh
git reset --hard HEAD
```

#### pull

##### fetch + merge
```sh
git pull
```

##### fetch + rebase
ローカルの変更がリモートの後になるので履歴が綺麗
```sh
git pull --rebase
```

---
## コミットの取り消し

### reset（なかった事にする）

#### HEADとmainの位置を1つ前のコミットに戻す
```sh
git reset --soft HEAD~1
```

#### ステージングエリアも1つ前に戻す
```sh
git reset （--mixed） HEAD
```

#### ワーキングディレクトリも戻す
```sh
git reset --hard HEAD
```

#### 全てを1つ前に戻す
```sh
git reset --hard HEAD~1
```

### revert（取り消しコミットを作る）

#### 1つ前に戻したコミットを新たに作成する
```sh
git revert HEAD
```

### 取り消したコミットを復元

#### HEADの移動履歴を表示
```sh
git reflog
```

#### 履歴から戻りたいHEAD位置をコピー・ペースト
```sh
git reset --hard HEAD@{1}
```

#### ただしPowerShellではエラーになるため''で囲む
```sh
git reset --hard 'HEAD@{1}'
```

---
## コミットを修正

### 最新コミットを修正

#### コミットメッセージを修正
```sh
git commit --amend -m "修正したコミットメッセージ"
```

#### ファイルを追加し忘れた
```sh
git add 追加したいファイル
```
→
```sh
git commit --amend
```

### 過去のコミットを修正

```sh
git rebase -i 修正したいコミットの1つ前のコミットID
```
→
修正したいコミットの「pick」を「edit」に変更してSTART
→
修正したいファイルを修正
→
```sh
git add .
```
→
```sh
git commit --amend
```
→
```sh
git rebase --continue
```

#### continueせず途中で抜けたい場合は
```sh
git rebase --abort
```

### コミットを並べ替える

```sh
git rebase -i 並び替えたいコミット達の1つ前のコミットID
```
→
指定コミットにカーソルを置き「alt+矢印」で移動（VSCode）

### 過去のコミットを統合

```sh
git rebase -i 統合したいコミット達の1つ前のコミットID
```
→
統合したいコミット（後の方）の「pick」を「squash」に変更
→
1つ前のコミットと統合される












































