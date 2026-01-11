# Git Command

初期化
```sh
git init
```

カレントディレクトリ以下をインデックス（ステージング）
```sh
git add .
```

コミット
```sh
git commit -m "メッセージ"
```
`commit`には`記憶に留める・格納する`といった意味がある

## コミット前の確認

### 変更を表示
```sh
git status
```

### 差分表示

ステージングエリアとワーキングディレクトリの差分
```sh
git diff
```

ローカルリポジトリとステージングエリアの差分
```sh
git diff --staged
```

### ステージングエリアにあるファイルの一覧

```sh
git ls-files
```

## コミット前の取り消し

### ファイルの変更を取り消す（↔ diff）

「ステージングしていないファイル」を直前のコミット後まで戻す
```sh
git restore ファイル名
```

全ての「ステージングしていないファイル」を直前のコミット後まで戻す
```sh
git restore .
```

### addを取り消す（↔ diff --staged）

ステージング前の状態に戻す
```sh
git restore --staged ファイル名
```

## コミット管理

### コミット履歴を表示

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

#### 期間指定

過去２週間のコミット一覧
```sh
git log --since=2.weeks
```

```sh
git log --since="いつから" --until="いつまで"
```
特定の日を指定する ("2026-01-10") 、 相対日付を"2 years 1 day 3 minutes ago"のように指定することも可能

### コミット詳細を表示

HEADのあるコミット詳細
```sh
git show
```

HEADの2つ前のコミット
```sh
git show HEAD~2
```

そのIDのコミット
```sh
git show コミットID
```

そのタグがついたコミット
```sh
git show タグ名
```

### タグをつける（version情報など）

コミットを指定しないと最新のコミットにつく
```sh
git tag タグ名 （コミットID）
```

注釈付き
```sh
git tag -a タグ名 （コミットID） -m "注釈"
```

タグ一覧
```sh
git tag
```

ローカルのタグを削除
```sh
git tag -d タグ名 （コミットID）
```

## ブランチ

ブランチは「別ルートのセーブデータ」

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

リモート追跡ブランチの一覧
```sh
git branch -r
```

コミットIDとメッセージと追跡リモート付き一覧
```sh
git branch -vv
```

リモートも含めた全てのブランチ一覧
```sh
git branch -a
```

ブランチ名変更
```sh
git branch -m <old> <new>
```

### ブランチ削除

merge済のみ削除
```sh
git branch -d ブランチ名
```

merge済でなくても削除
```sh
git branch -D ブランチ名
```

### 親ブランチ（main等）との変更確認

コミット表示
```sh
git log 親ブランチ..ブランチ
```

差分表示
```sh
git diff 親ブランチ..ブランチ
```

### ブランチの統合

#### merge - 合流 - (fast-forward,no-ff)

親ブランチへ移動した後
```sh
git merge トピックブランチ
```

fast-forwardできる状態でもマージコミットを残す時は
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

トピックブランチブランチにいる状態で
```sh
git rebase main
```

mainへ移動した後、merge
```sh
git merge トピックブランチ
```

## リモートリポジトリ

### push

上流（upstream）ブランチ（リモート追跡ブランチ）を設定しながらpush
```sh
git push -u origin ブランチ名
```

上流ブランチを設定しておくとorigin mainは省略できる
```sh
git push (origin main)
```

ローカルのタグをリモートに同期
```sh
git push origin タグ名
```

リモートからタグを削除
```sh
git push origin --delete タグ名
```

### ローカルにクローン

リポジトリ名のフォルダが作成され、クローンされる
```sh
git clone リモートリポジトリのクローンURL
```

### リモートリポジトリ

リモートリポジトリの一覧
```sh
git remote
```

fetchやpushする時のURLも表示
```sh
git remote -v
```

ローカルからリモートリポジトリへ接続
```sh
git remote add origin リモートリポジトリのURL
```

既にリモートで削除されているブランチを消す
```sh
git remote pune origin
```

### リモートリポジトリのコミットを取得

#### fetch

リモート追跡ブランチ（origin/main）が更新される
```sh
git fetch
```

fetchでエラーがあった等で戻したい時
```sh
git reset --hard HEAD
```

#### pull

fetch + merge
```sh
git pull
```

fetch + rebase
ローカルの変更がリモートの後になるので履歴が綺麗
```sh
git pull --rebase
```

## コミットの取り消し

### reset（なかった事にする）

HEADとmainの位置を1つ前のコミットに戻す
```sh
git reset --soft HEAD~1
```

ステージングエリアも1つ前に戻す
```sh
git reset （--mixed） HEAD
```

ワーキングディレクトリも戻す
```sh
git reset --hard HEAD
```

全てを1つ前に戻す
```sh
git reset --hard HEAD~1
```

### revert（取り消しコミットを作る）

1つ前に戻したコミットを新たに作成する
```sh
git revert HEAD
```

### 取り消したコミットを復元

HEADの移動履歴を表示
```sh
git reflog
```

履歴から戻りたいHEAD位置をコピー・ペースト
```sh
git reset --hard HEAD@{1}
```

ただしPowerShellではエラーになるため''で囲む
```sh
git reset --hard 'HEAD@{1}'
```

## コミットを修正

### 最新コミットを修正

コミットメッセージを修正
```sh
git commit --amend -m "修正したコミットメッセージ"
```

ファイルを追加し忘れた
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
continueせず途中で抜けたい場合は
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










