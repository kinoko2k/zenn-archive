```
title: 【Discord.js】メッセージURLからメッセージを取得しよう
```

はろーわーるど

## テスト環境
- Discord.js v14
- Node.js v18

## メッセージURLからメッセージを取得する方法
```javascript
  const MESSAGE_URL_REGEX = /https?:\/\/discord\.com\/channels\/(\d+)\/(\d+)\/(\d+)/g;
  const matches = MESSAGE_URL_REGEX.exec(message.content);
  if (matches) {
    const [_, guildId, channelId, messageId] = matches;
    const guild = await client.guilds.fetch(guildId);
    const channel = await client.channels.fetch(channelId);
    const fetchedMessage = await channel.messages.fetch(messageId);
```
```js
  const MESSAGE_URL_REGEX = /https?:\/\/discord\.com\/channels\/(\d+)\/(\d+)\/(\d+)/g;
```
上記のコードでメッセージURLと判別します。

あとは、メッセージURLにある、サーバIDやチャンネルIDやメッセージIDからメッセージを取得します。

*guildが役に立ってない気がしますが、スルーします。

## メッセージを取得して、Botから表示するコード
```javascript
client.on('messageCreate', async message => {
  if (message.author.id === client.user.id) return;

  if (message.author.bot) return;
  const MESSAGE_URL_REGEX = /https?:\/\/discord\.com\/channels\/(\d+)\/(\d+)\/(\d+)/g;
  const matches = MESSAGE_URL_REGEX.exec(message.content);
  if (matches) {
    const [_, guildId, channelId, messageId] = matches;
    const guild = await client.guilds.fetch(guildId);
    const channel = await client.channels.fetch(channelId);
    const fetchedMessage = await channel.messages.fetch(messageId);

    const Embed = new EmbedBuilder()
      .setColor('#4d4df7')
      .setAuthor({ name: fetchedMessage.author.tag, iconURL: fetchedMessage.author.displayAvatarURL() })
      .setDescription(fetchedMessage.content)
      .addFields(
        { name: 'メッセージに飛ぶ', value: `[クリックで飛べます。](${fetchedMessage.url})` }
      )
      .setTimestamp(fetchedMessage.createdTimestamp);

    message.channel.send({ embeds: [Embed] });
  }
});
```
ちなみに、URLをそのまま使うので、処理中にメッセージ変更するとバグります。
どんだけ早い速度で編集したらそうなるかわかりませんが...

著:[@kinoko1216](https://x.com/kinoko1216)
