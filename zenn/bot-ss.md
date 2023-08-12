```
title: 【Discord】Botからサイトのスクリーンショットを撮ろう！
```

// あんまり投稿しないので初メッセージの仕方を忘れました。

## この記事を読んだ人が求めてそうなスクリーンショット
![](https://storage.googleapis.com/zenn-user-upload/126ab209e356-20230806.png)

これが作れるコードです。

### 作ったきっかけ
なんか認証Bot開発してた時から、意見応募に多かった機能だから。

### メリット
- サイトが分かりやすい
- 遊べる
- 自分のドメインがどう動いてるか確かめれる

### デメリット
- なんか危ない気がする
- セキュリティ対策０
- 悪用されたら危なそう

なんか傾きが大きいですが、めっちゃコード書いてる人なら改善する余地があるかもしれません...汗

## 動作環境と必要なパッケージ
- [Windows 10](https://www.microsoft.com/) / [Ubuntu 22.04 LTS](https://ubuntu.com/)
- [Node.js v18](https://nodejs.org/)
- [Discord.js v14](https://discord.js.org/)
- [puppeteer](https://www.npmjs.com/package/puppeteer) `npm install puppeteer`
- [chromium](https://www.chromium.org/chromium-projects/)
## 全体コード
```javascript
const { Client, GatewayIntentBits, EmbedBuilder, MessageAttachment } = require('discord.js');


const client = new Client({
	intents: [
	GatewayIntentBits.Guilds,
	GatewayIntentBits.GuildMessages,
	GatewayIntentBits.MessageContent,
	GatewayIntentBits.GuildMembers,
	GatewayIntentBits.GuildMessageTyping,
	GatewayIntentBits.DirectMessages,
	GatewayIntentBits.DirectMessageReactions,
	GatewayIntentBits.DirectMessageTyping,
	],
});


client.on("ready", () => {
  console.log(`Logged in as ${client.user.tag}`);
});

const puppeteer = require('puppeteer');
const { chromium } = require('playwright');

async function takeScreenshot(url) {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  await page.goto(url);
  const screenshot = await page.screenshot();
  await browser.close();
  return screenshot;
}
// discordに送信します。
async function sendScreenshot(message, url) {
  try {
    const screenshot = await takeScreenshot(url);

    const embed = new EmbedBuilder()
      .setTitle('サイトのスクリーンショットを表示します。')
      .setDescription('`' + url + '`' + `のスクリーンショットはこちら:`)
      .setColor('#00ff00');

    message.channel.send({ embeds: [embed] });
    message.channel.send({ files: [{ attachment: screenshot, name: 'screenshot.png' }] });
  } catch (error) {
    console.error('スクリーンショットの取得に失敗しました:', error);
    message.channel.send('スクリーンショットの取得に失敗しました。');
  }
}

// メッセージに反応してコマンドを実行する
client.on('messageCreate', (message) => {
  if (message.content.startsWith('!screenshot')) {
    const args = message.content.split(' ');
    if (args.length === 2) {
      const url = args[1];
      sendScreenshot(message, url);
    } else {
      message.channel.send('正しい使用法: !screenshot <URL>');
    }
  }
});

client.login("TOKEN");
```

## 最後に
ちなみにGitHubにも投稿されているのでよかったらstarとかつけてほしいなって...
https://github.com/kinoko2k/ScreenShotBot/

あとTwitterもやってるからフォローしてほしいなとかって(ごうよくかよ)
https://twitter.com/kinoko1216

