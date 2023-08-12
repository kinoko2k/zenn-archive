```
title: 【Discord.js】Botによくありそうな機能コード集
```

はろーわーるど

昔作ったコードを修正したり、作り直したり...っていうコードを貼っておきます。
勝手にパクっといてくれ。礼として入れたら[Discordサーバー](https://discord.com/invite/VdzXx892Zq)入ってほしい。

## サーバの絵文字リストを表示するコマンド
```javascript
client.on('messageCreate', async (message) => {
  if (!message.content.startsWith(prefix) || message.author.bot) {
    return;
  }

  const args = message.content.slice(prefix.length).trim().split(/ +/);
  const command = args.shift().toLowerCase();

  if (command === 'emojis') {
    const emojis = message.guild.emojis.cache;

    if (emojis.size === 0) {
      message.reply('このサーバーには絵文字がありません');
      return;
    }

    const emojiList = emojis.map((emoji) => `${emoji} — \`${emoji.name}\``).join('\n');
    const embed = new MessageEmbed()
      .setTitle(`サーバー絵文字リスト (${emojis.size})`)
      .setDescription(emojiList)
      .setColor('#0099ff');
      .setURL('https://zenn.dev/articles/0b3ce05e269d70/');

    message.channel.sendTyping(500)
    message.channel.send({ embeds: [embed] });
  }
});
```

## !omikujiでおみくじを表示するコマンド
```javascript
client.on('messageCreate', async (message) => {
  if (message.author.bot) return;
  if (!message.content.startsWith(PREFIX)) return;

  const args = message.content.slice(PREFIX.length).trim().split(/ +/);
  const command = args.shift().toLowerCase();

  if (command === 'omikuji') {
    const omikujiOptions = ['大吉', '中吉', '小吉', '末吉', '凶']; // おみくじの結果

    const randomIndex = Math.floor(Math.random() * omikujiOptions.length);
    const selectedResult = omikujiOptions[randomIndex];

    const embed = new EmbedBuilder()
      .setColor('#ADFF2F')
      .setTitle('おみくじ')
      .setDescription(`<@${message.author.id}>さんの今日の運勢は！`)
      .setFields({ name: '[運勢]', value: `${selectedResult}`, inline: true })
      .setThumbnail('https://3.bp.blogspot.com/-cPqdLavQBXA/UZNyKhdm8RI/AAAAAAAASiM/NQy6g-muUK0/s400/syougatsu2_omijikuji2.png')
      .setURL('https://zenn.dev/articles/0b3ce05e269d70/');

    message.channel.send({ embeds: [embed] });
  }
});
```

## サーバーメンバー数を細かく表示するコマンド
```javascript
client.on('messageCreate', async (message) => {
  if (message.author.bot) return;
  if (!message.content.startsWith(PREFIX)) return;

  const args = message.content.slice(PREFIX.length).trim().split(/ +/);
  const command = args.shift().toLowerCase();

  if (command === 'userlist') {
    const members = message.guild.members.cache.filter(member => !member.user.bot);
    const bots = message.guild.members.cache.filter(member => member.user.bot);

    const memberCount = members.size;
    const botCount = bots.size;

    const memberList = members.map(member => member.user.username).join('\n');
    const botList = bots.map(bot => bot.user.tag).join('\n');

    const embed = new EmbedBuilder()
      .setColor('#0099ff')
      .setTitle('サーバーの人数統計表')
      .addFields(
        { name: 'メンバー', value: `合計 ${memberCount} 人\n${memberList}`, inline: true },
        { name: 'Bot', value: `合計 ${botCount} 人\n${botList}`, inline: true },
        { name: '全体メンバー数', value: `合計 ${message.guild.memberCount} 人`, inline: false },
      )
      .setURL('https://zenn.dev/articles/0b3ce05e269d70/');

    message.channel.send({ embeds: [embed] });
  }
});
```

著:[@kinoko1216](https://twitter.com/kinoko1216)
