# Mahjong Ledger — Firebase 实时共享版

## 1. 创建 Firebase 项目

1. 打开 https://console.firebase.google.com/ 创建项目。
2. 在 **Build → Authentication → Sign-in method** 启用 **Anonymous（匿名）** 登录。
3. 在 **Build → Realtime Database** 创建数据库，记下数据库 URL（例如 `https://your-project-default-rtdb.europe-west1.firebasedatabase.app`）。注意是 **Realtime Database**，不是 Firestore。
4. 在 Realtime Database → **Rules** 中，复制 `database.rules.json` 的内容并发布。
5. 在 Project settings → Your apps 添加 **Web app**，复制 Web 配置。

## 2. 配置 index.html

找到 `const firebaseConfig = {...}`，把其中占位值替换为 Firebase 控制台提供的真实 `apiKey`, `authDomain`, `databaseURL`, `projectId`, `appId`。

注意 `databaseURL` 应当是完整地址，如 `https://your-project-default-rtdb.europe-west1.firebasedatabase.app`，不能再额外加前缀。

如果需要多个独立积分房间，可修改 `ROOM_ID`，但必须同步修改 `database.rules.json` 的房间限制。

## 3. GitHub Pages 发布

1. 在 GitHub 新建一个 **Public** 仓库，例如 `mahjong-ledger`。
2. 上传已配置的 `index.html` 到仓库根目录（可以一起上传本 README）。
3. 打开仓库 **Settings → Pages**，选择 **Deploy from a branch**，选择 `main` 和 `/(root)`，保存。
4. 等待部署，访问 `https://你的GitHub用户名.github.io/mahjong-ledger/`。
5. 把这个地址发给朋友。所有访问者使用同一个 Firebase 房间。

## 4. 测试

- 用两个不同浏览器或手机打开网址。
- 在一端修改一个玩家名字，另一端应实时变化。
- 选择四人、填写本轮游戏局数及四人分数，提交后检查另一端排行榜与历史记录。
- 两端同时提交不同轮次，检查两轮是否都存在。

## 注意

- **任何访问者都能修改/删除成绩和清空数据**。匿名登录不是管理员权限，链接泄露后也无法阻止别人篡改。不要用作高价值或正式比赛的安全记分系统。
- 目前没有云端备份和恢复功能，建议定期从 Firebase Console 导出 JSON 备份。
- Firebase 免费套餐有额度限制，通常适合小型朋友圈使用，但不保证永远免费。
- 这是在线版，不能离线提交。Firebase 配置不能凭空生成，必须由项目所有者创建。
- 本程序保留原版的 `Games this round` 逻辑：每轮四名参与者的游戏次数都增加相同的填写值。
- 旧版 `window.storage` 的历史数据**不会自动迁移**到 Firebase。
- Firebase 配置中的 `apiKey` 是公开客户端标识，不是保密的管理员密码；数据库安全取决于规则与身份验证。

## 5. 修改日志与共享记事本（新增）

- 底部“记事本”标签包含：操作人昵称、共享笔记、修改日志。
- 修改成绩、新增成绩、删除成绩、修改玩家姓名、清空积分以及发布笔记时，保存修改人、客户端时间、修改前后快照（适用时）。
- 昵称保存在当前浏览器的 localStorage；匿名登录 UID 可辅助区分设备/登录实例，但**不能证明现实身份**。换设备或清理浏览器数据会更换匿名身份。
- **重要安全限制**：由于此版本允许所有匿名登录者写入整个房间，具有技术能力的访问者仍可绕过网页直接修改或删除日志，客户端时间也可能被伪造。因此这是便于追溯的操作日志，而不是不可篡改审计。若要求“保证谁在何时改了什么”，必须采用实名/受控账户登录、服务器端时间、不可由普通用户写入或删除的审计记录（例如 Cloud Functions + 独立受保护的日志路径）。
- 所有旧积分继续可读取；旧操作无法追溯。请定期导出数据库备份。
