## 概要

- bashスクリプト内で（スクリプト自身を） `git pull`する
- `git pull` の後に続く処理内容に変更があった場合、その変更は直後の実行時にも反映されるのか？（スクリプト自身が更新された内容で処理されるのか？）を確認してみる
- 結論: __直後の実行時は古い内容のまま処理された__

## 検証環境

- OS X Catalina 10.15.7
- bash 3.2.57
- git 2.34.1

```sh
% /bin/sh --version
GNU bash, version 3.2.57(1)-release (x86_64-apple-darwin19)
Copyright (C) 2007 Free Software Foundation, Inc.

% git --version
git version 2.34.1
```

## 調査手順

下記内容で test.sh をcommit→pushしておく

```sh
#!/bin/sh

git pull origin main

sleep 30
echo foo
```

次に、このリポジトリをcloneしたディレクトリを2つ用意しておき、どちらか一方で `foo` を `bar` に修正してcommit→pushする

```sh
#!/bin/sh

git pull origin main

sleep 30
echo bar
```

修正を行わなかった方のディレクトリにcdして実行し、`foo`が表示されるか`bar`が表示されるか確認する

__→ `foo` が表示された（= 古いcommitの内容）__

```sh
bash-3.2$ sh test.sh
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 271 bytes | 271.00 KiB/s, done.
From github.com:practice-goldeneggg/bash-overwrite
 * branch            main       -> FETCH_HEAD
   3a72b6a..5232b90  main       -> origin/main
Updating 3a72b6a..5232b90
Fast-forward
 test.sh | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
foo
```

念の為、上記と同じディレクトリで同じコマンドを再実行してみて、`foo`が表示されるか`bar`が表示されるか再確認

__→ `bar` が表示された（= 新しいcommitの内容）__

```sh
bash-3.2$ sh test.sh
From github.com:practice-goldeneggg/bash-overwrite
 * branch            main       -> FETCH_HEAD
Already up to date.
bar
```
