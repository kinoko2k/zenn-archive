===
title: 【Discord.py】Botによくありそうな機能コード集
===

はろーわーるど

[【Discord.js】Botによくありそうな機能コード集](https://zenn.dev/kinoko1216/articles/0b3ce05e269d70)の記事をDiscord.pyに直したコードを貼っておきます。
コードが初心者すぎておかしくなってますが、ご了承ください。(2年前ぐらいにかじっただけ)
勝手にパクっといてくれ。礼として入れたら[Discordサーバー](https://discord.com/invite/VdzXx892Zq)入ってほしい。

## サーバの絵文字リストを表示するコマンド
```python
@client.event
async def on_message(message):
    # Prefixを設定する。
    prefix = 's!'

    if not message.content.startswith(prefix) or message.author.bot:
        return

    args = message.content[len(prefix):].strip().split()
    command = args.pop(0).lower()

    if command == 'emojis':
        emojis = message.guild.emojis

	# 絵文字が存在しない場合、このメッセージを送信する。
        if len(emojis) == 0:
            await message.reply('このサーバーには絵文字がありません')
            return

        emoji_list = '\n'.join([f'{emoji} — `{emoji.name}`' for emoji in emojis])
	# embedを作成する。
        embed = discord.Embed(
            title=f'サーバー絵文字リスト ({len(emojis)})',
            description=emoji_list,
            color=0x0099ff
        )
        embed.url = 'https://zenn.dev/kinoko1216/articles/b647b4db656e70'

        await message.channel.send(embed=embed)

```

## !omikujiでおみくじを表示するコマンド
```javascript
@client.event
async def on_message(message):
    prefix = 's!'  # 任意のプレフィックスを設定してください

    if message.author.bot:
        return

    if not message.content.startswith(prefix):
        return

    args = message.content[len(prefix):].strip().split()
    command = args.pop(0).lower()

    if command == 'omikuji':
	# おみくじの結果
        omikuji_options = ['大吉', '中吉', '小吉', '末吉', '凶']

        random_index = random.randint(0, len(omikuji_options) - 1)
        selected_result = omikuji_options[random_index]
	
	# embedを作成する。
        embed = discord.Embed(
            title='おみくじ',
            description=f'<@{message.author.id}>さんの今日の運勢は！',
            color=0xADFF2F
        )
        embed.add_field(name='[運勢]', value=selected_result, inline=True)
        embed.set_thumbnail(url='https://3.bp.blogspot.com/-cPqdLavQBXA/UZNyKhdm8RI/AAAAAAAASiM/NQy6g-muUK0/s400/syougatsu2_omijikuji2.png')
        embed.url = 'https://zenn.dev/kinoko1216/articles/b647b4db656e70'

        await message.channel.send(embed=embed)
```

## サーバーメンバー数を細かく表示するコマンド
```javascript
@client.event
async def on_message(message):
    prefix = 's!'  # 任意のプレフィックスを設定してください

    if message.author.bot:
        return

    if not message.content.startswith(prefix):
        return

    args = message.content[len(prefix):].strip().split()
    command = args.pop(0).lower()

    if command == 'userlist':
        members = [member for member in message.guild.members if not member.bot]
        bots = [member for member in message.guild.members if member.bot]

        member_count = len(members)
        bot_count = len(bots)

        member_list = '\n'.join([member.name for member in members])
        bot_list = '\n'.join([bot.name for bot in bots])

        embed = discord.Embed(
            title='サーバーの人数統計表',
            color=0x0099ff
        )
        embed.add_field(name='メンバー', value=f'合計 {member_count} 人\n{member_list}', inline=True)
        embed.add_field(name='Bot', value=f'合計 {bot_count} 人\n{bot_list}', inline=True)
        embed.add_field(name='全体メンバー数', value=f'合計 {len(message.guild.members)} 人', inline=False)
        embed.url = 'https://zenn.dev/kinoko1216/articles/b647b4db656e70'

        await message.channel.send(embed=embed)
```

著:[@kinoko1216](https://twitter.com/kinoko1216)
