```
title: 【Discord.js】VCに参加/退出したユーザーを通知しよう
```

はろーわーるど

## 完成品
![](https://storage.googleapis.com/zenn-user-upload/a4749884f399-20230810.png)
↑こんな感じのやつが作れます。

## 作った意味
Discord.jsでVoice関係難しいらしい。
Voice関係じゃないけど

## テスト環境
- Discord.js v14
- Node.js v18

## 特定のユーザーがVCに参加/退出したら通知するコード
```javascript
const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMembers,
    GatewayIntentBits.GuildVoiceStates,
  ],
});

const CHANNEL_ID = '通知するチャンネルID';

client.on('voiceStateUpdate', async (oldState, newState) => {
  // 特定のユーザIDを設定
  const targetUserId = '特定のユーザID';

  if (newState.member.user.id === targetUserId) {
    const channel = client.channels.cache.get(CHANNEL_ID);

    // VCに参加すると表示するembed
    if (newState.channel) {
      const embed = new EmbedBuilder()
        .setTitle('VC参加ログ')
        .setDescription(`@${newState.member.user.username} (${targetUserId}) が、<#${newState.channel.id}>に参加しました。`)
        .setColor('#00ff00');

      await channel.send({ embeds: [embed] });

    // VCに退出すると表示するembed
    } else if (oldState.channel) {
      const embed = new EmbedBuilder()
        .setTitle('VC退出ログ')
        .setDescription(`@${newState.member.user.username} (${targetUserId}) が、<#${oldState.channel.id}>から退出しました。`)
        .setColor('#ff0000');

      await channel.send({ embeds: [embed] });
    }
  }
});
```
![](https://storage.googleapis.com/zenn-user-upload/d1a5ed9e7053-20230810.png)
この写真と同じものが作れます。

## サーバーユーザーがVCに参加/退出したら通知するコード
```javascript
const CHANNEL_ID = '通知するチャンネルID';

client.on('voiceStateUpdate', async (oldState, newState) => {
  const channel = client.channels.cache.get(CHANNEL_ID);

  // VCに参加すると表示するembed
  if (newState.channel) {
    const embed = new EmbedBuilder()
      .setTitle('VC参加ログ')
      .setDescription(`@${newState.member.user.username} (${newState.member.user.id}) が、<#${newState.channel.id}>に参加しました。`)
      .setColor('#00ff00');

    await channel.send({ embeds: [embed] });

  // VCに退出すると表示するembed
  } else if (oldState.channel) {
    const embed = new EmbedBuilder()
      .setTitle('VC退出ログ')
      .setDescription(`@${newState.member.user.username} (${newState.member.user.id}) が、<#${oldState.channel.id}>から退出しました。`)
      .setColor('#ff0000');

    await channel.send({ embeds: [embed] });
  }
});
```
![](https://storage.googleapis.com/zenn-user-upload/737607f98316-20230810.png)
この写真と同じものが作れます。

はーとください。
作:[@kinoko1216](https://discord.com/invite/VdzXx892Zq)
