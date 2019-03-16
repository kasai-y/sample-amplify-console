## Memo

いろいろと触ってみたメモ

### create

```
$ aws amplify create-app --profile ryusei --region us-east-1 \
  --name sample \
  --repository github.com/kasai-y/sample-amplify-console \
  --platform WEB \
  --oauth-token ${GITHUB_TOKEN}

$ aws amplify create-branch --profile ryusei --region us-east-1 \
  --app-id ${AMPLIFY_APP_ID} \
  --branch-name master
```

東京リージョンには来てないので `us-east-1` を指定。

こんな感じでいけると思ったけど、ちょっと違うっぽい。
コマンドは成功してて、listコマンドを叩く限りは登録されているのだけれど、AWSコンソールで一覧を開くとずーっとロード中になっている。
おそらくGitHubのOauthTokenの設定の仕方が誤ってる気がする。
画面ポチポチで作成。

`https://master.***.amplifyapp.com/` こんな感じのURLが作られてindex.htmlが表示された。

masterブランチにコミットすると、即座にビルドが走る。

### list

```
 aws amplify list-apps --profile ryusei --region us-east-1
```

### build

ビルドの設定をリポジトリのルートに置いておくと、それを利用してくれる。

[amplify.yml](amplify.yml)

```yaml
version: 0.1
frontend:
  phases:
    build:
      commands:
        - echo "building" > building.html
  artifacts:
    baseDirectory: /
    files:
      - '**/*'
  cache:
    paths: []
```

試しに `echo "building" > building.html` というコマンドをビルドに設定。
ビルド後に `https://master.xxxxx.amplifyapp.com/building.html` というURLを叩くと `building` と出力するページが表示された。

### Delete

```
$ aws amplify delete-app --profile ryusei --region us-east-1 \
  --app-id ${AMPLIFY_APP_ID}
```

ほったらかしでも料金的には大丈夫そうだけどいちおう。

### Other

- 料金はビルド時間とストレージ使用量に依存
- 「フロントエンドでバックエンドをデプロイする」的なこともユースケースとして書かれてる
  - アプリのプッシュ通知とか？

### 所感

- CIとセットになってるあたりに安心感
  - CIとしても柔軟そう
- 静的なコンテンツを展開するのには便利そう
  - Amplifyフレームワークがそもそも分かってないけど
  - 社内へのGOツール配布とかにも使えるかな
- ブランチ毎に開発環境を作りやすい
  - 設定でBASIC認証をかけられる
- デプロイされた環境はどの程度の負荷まで耐えられるのだろうか。
