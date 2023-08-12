```
title: GitHub Actionsを使って今日のコミット数をDiscordに送信しよう
```

初のまともな記事です。多分。そうだろう。

## 参考にしたサイト
https://docs.github.com/ja/actions/learn-github-actions/understanding-github-actions

## Actionsの作り方
1. `.github/workflows`というディレクトリを作成します。
2. `commit.yml`というファイルを作成します。
![](https://storage.googleapis.com/zenn-user-upload/c4317cc370f2-20230614.png)
3. `commit.ymlの中身`
```yml:commit.yml
name: Commit Counter

on:
  push:
  schedule:
    - cron: "59 23 * * *"

jobs:
  commit_counter:
    runs-on: ubuntu-latest

    steps:
      - name: Check out repository
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: 3.x

      - name: Install dependencies
        run: pip install requests

      - name: Count commits
        id: count_commits
        run: |
          COMMIT_COUNT=$(git rev-list --count HEAD)
          echo "::set-output name=count::$COMMIT_COUNT"

      - name: Send to Discord
        env:
          DISCORD_WEBHOOK: ${{ secrets.DISCORD_WEBHOOK }}
          ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
          GITHUB_REPOSITORY: ${{ github.repository }}
          GITHUB_SHA: ${{ github.sha }}
          GITHUB_REF: ${{ github.ref }}
        run: |
          python send_user_to_discord.py ${{ steps.count_commits.outputs.count }}
```
23時59分に送るのは、今日のコミット数を24時に送信すると明日のコミット数が含まれて0になるからです。

4. Pythonで`send_user_to_discord.py`というファイルを作成します。
5. `send_user_to_discord.py`の中身
```python:send_user_to_discord.py
import requests
import datetime
import os

# {username}ユーザ名を設定する
username = '自分のユーザ名(@はいらないよ)'

today = datetime.date.today().isoformat()

# GitHub REST APIのエンドポイントURLを構築する
url = f'https://api.github.com/users/{username}/events'

access_token = os.environ['ACCESS_TOKEN']
webhook_url = os.environ['DISCORD_WEBHOOK']

# APIリクエストを送信する
headers = {'Authorization': f'token {access_token}'}
response = requests.get(url, headers=headers)

events = response.json()
commit_count = sum(1 for event in events if event['type'] == 'PushEvent' and event['created_at'].startswith(today))

data = {
    "embeds": [
        {
            "title": "今日のこみっと",
            "description": f"今日のこみっと数：{commit_count}",
            "color": 16711680
        }
    ]
}

# Discord WebhookにPOSTリクエストを送信
response = requests.post(webhook_url, json=data)
if response.status_code == 204:
    print('コミット数が、Discordに正常に送信されました！')
else:
    print(f'コミット数を、Discordに送信できませんでした。{response.status_code}')
```
書き方は汚いですが、多分あってると思います。

6. `ACCESS_TOKEN`を設定しないといけませんので、https://github.com/settings/tokens?type=beta からTOKENを取得してください
7. レポジトリに行って、Settings > https://github.com/kinoko2k/DailyCommits/settings/secrets/actions > に行きます。
8. `New repository secret`に行き、`Name`に`ACCESS_TOKEN`と保存し、`Value`に`TOKEN`をしまいます。
9. また、`DiscordWebhook`も設定しないといけないので、同じように、`New repository secret`を押して、`Name`に`DISCORD_WEBHOOK`と入れて、`Value`に`Webhook`を保存します。
10. そしてレポジトリをプッシュしてください。(なんでもいいので多分)
11. うまくいくと画像のような感じで送信されます。
![](https://storage.googleapis.com/zenn-user-upload/c72f9bee729a-20230614.png)

## 最後に
急ぎ足ですが、暇であればこの記事をもっとわかりやすく改善します。

というか初心者ですので、効率化できるのであれば教えてください ;; 