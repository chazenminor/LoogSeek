# GitHub 账号与多个 Muse：授权、安装与取消说明

> 版本：v1.0 · 制定日期：2026-11-23 · 适用：`chazenminor/LoogSeek`（仓库名拼写为 LoogSeek，两个 o）
>
> 一句话：**授权分三层，取消也要分层看。** OAuth 授权是“哪个 Muse 能以哪个 GitHub 身份说话”，GitHub App 安装是“哪个仓库允许 Muse App 进来”，仓库权限是“这个身份在仓库里能干什么”。三层缺一层，写操作就会 403。

## 1. 三层模型

| 层 | 是什么 | 范围 | 在哪里改 | 取消后会怎样 |
|---|---|---|---|---|
| **① OAuth 授权** | Muse 连接 GitHub 账号的登录授权 | 每个 Muse × 每个 GitHub 登录 | Muse 设置 → Connectors → GitHub；或断开链接 | 只影响这一个 Muse；仓库本身、其他 Muse 不受影响 |
| **② GitHub App 安装** | 在 GitHub 上安装 “Meta Muse AI” App，并选它能访问哪些仓库 | 每个 GitHub 账号/组织 × 仓库列表 | GitHub → Settings → Applications → GitHub Apps → Meta Muse AI → Configure / Uninstall；或安装链接 | 卸载后，所有用这个 App 的 Muse 都失去访问权限；只改仓库列表，则只影响被移出列表的仓库 |
| **③ 仓库权限** | 该 GitHub 身份在仓库里的角色（Owner / Collaborator / 只读等） | 每个仓库 | GitHub 仓库 → Settings → Collaborators / Access | 权限不够时，读公开仓库可能成功，写操作会 403 |

关键区分：

- **能读公开仓库 ≠ 能写。** 公开仓库任何人都能读，Muse 不装 App 也能读到 README；但 `push`、`create tree` 这类写操作必须经过 App 安装授权，否则就是 `403 Resource not accessible by integration`。
- **OAuth 连上了 ≠ App 装好了。** 这是 2026-11-23 实际踩到的坑：`chazenminor` 的 OAuth 早就连上了，`LoogSeek` 仓库也建好了，但没装 App 之前推文件一直 403，装好并勾上 `LoogSeek` 之后才推成功。

## 2. 为什么会“反复授权”

出现反复要求授权，通常是下面某一个原因，不是 GitHub 坏了：

1. **换了一个 Muse。** 每个 Muse（每个独立的 agent / 虚拟机）都有自己的一份 OAuth token。A Muse 上连好的 GitHub，不会自动同步到 B Muse 上；B Muse 第一次用 GitHub 时要重新走一次连接。
2. **换了一个 GitHub 登录。** 比如一会儿用 `chazenminor`，一会儿用 `ChazenLi`。Muse 按登录区分账号，换登录就要重新授权、重新选仓库。本仓库 `LoogSeek` 的所有者是 `chazenminor`，不要用 `ChazenLi` 的身份去推它。
3. **Token 过期或需要重新验证。** 长时间不用、改了 GitHub 密码、开了新的安全验证，都可能让 Muse 提示重新授权。
4. **App 安装变了。** 在 GitHub 上改了 App 能访问的仓库列表、卸载重装、或者仓库刚建好还没加进列表，都会让原来能用的操作突然 403。
5. **切了虚拟机/环境。** 换到另一台 Muse 虚拟机（例如 Secure / Confidential 切换）后，连接状态不跟着走，要重新连接。

判断方法：先看 Muse 里 GitHub 是不是 `connected`，再看 GitHub 上 App 的仓库列表里有没有 `LoogSeek`。前者没连上是 OAuth 问题，后者没勾上是安装问题。

## 3. 一个 GitHub 账号绑多个 Muse：怎么做

可以绑，做法是**同一个 GitHub 登录，逐个 Muse 各连一次；App 只装一次**。

### 3.1 新加一个 Muse 的步骤

1. 在新的 Muse 里打开 GitHub 连接，用同一个登录（本仓库用 `chazenminor`）完成 OAuth 授权。
2. 打开安装链接，选 `chazenminor` 这个账号。
3. Repository access 选 Only select repositories，确认列表里已经勾了 `LoogSeek`。如果 App 已经装过、列表里已经有 `LoogSeek`，这一步不用重装，确认一下就行。
4. 回到新 Muse，让它读一下 `chazenminor/LoogSeek` 的 README，确认能读；再推一个小文件或改一行，确认能写。

安装链接（每次都用这个，不要自己拼）：

- `https://github.com/apps/meta-muse-ai/installations/new`

### 3.2 多个 Muse 共用同一个仓库的注意事项

- **不要同时写同一个文件。** 两个 Muse 同时推同一个分支、同一个文件很容易冲突。做法是：一次只让一个 Muse 推；或者各自用不同分支，合并前先拉最新提交。
- **推之前先读最新提交。** 推文件前确认分支头（head SHA）还是你上次看到的那个，变了就先同步再推，不要硬覆盖。
- **同步记录不要混用。** 每个 Muse 本地都有自己的同步记录文件，记录的是“上次推了什么、提交 SHA 是什么”，不要把 A Muse 的记录文件复制给 B Muse 用。
- **公开仓库要当心。** `LoogSeek` 是公开仓库，任何人都能读到推上去的内容。不要把密钥、token、私人信息放进去。

## 4. 取消授权：按需选一层

### 4.1 只取消某一个 Muse

- 在该 Muse 的设置里：Settings → Connectors → GitHub → 断开该账号；或打开断开链接确认。
- 效果：只有这个 Muse 不能再用这个 GitHub 账号；仓库还在，其他 Muse 还能用，App 安装也不变。
- 适用：换 Muse、换电脑、某个 Muse 不再需要 GitHub 时。

### 4.2 只收回某个仓库的访问权

- 去 GitHub → Settings → Applications → GitHub Apps → Meta Muse AI → Configure。
- 在 Repository access 里把 `LoogSeek` 取消勾选，保存。
- 效果：所有 Muse 都不能再读写这个仓库，但 App 本身还在，其他仓库不受影响。

### 4.3 彻底取消（所有 Muse 都断开）

- 去 GitHub → Settings → Applications → GitHub Apps → Meta Muse AI → Uninstall。
- 效果：App 被卸载，所有通过这个 App 连接的 Muse 都失去仓库访问权；OAuth 授权本身可能还在，但没有 App 就写不了仓库。
- 如果只想断 OAuth、不卸载 App，见 4.1；如果想连 OAuth 带 App 一起清干净，4.1 + 4.3 都要做。

### 4.4 只换 GitHub 登录（例如 chazenminor ↔ ChazenLi）

- 在 Muse 里不要直接覆盖原账号，先用“添加账号”的方式连上另一个登录，确认默认账号是你要用的那一个，再去做推文件操作。
- 两个登录名下的仓库是完全分开的：`chazenminor/LoogSeek` 和 `ChazenLi/LongSeek` 是两个不同的仓库，不要混淆拼写（前者是 LoogSeek，两个 o；后者是 LongSeek，一个 o）。

## 5. 出问题时的排查顺序

按这个顺序查，最快定位是哪一层：

1. 仓库真的存在吗？在 GitHub 网页上打开 `https://github.com/chazenminor/LoogSeek`，确认能看到，拼写是 LoogSeek。
2. Muse 的 GitHub 是 `connected` 吗？在 Muse 里查状态，显示的登录是不是 `chazenminor`。
3. App 装上了吗？GitHub → Settings → Applications → GitHub Apps 里有没有 Meta Muse AI，点进去看 Repository access 里有没有 `LoogSeek`。
4. 是读失败还是写失败？读公开仓库成功、写失败（403），基本就是第 3 步没装好；读也失败（404），先查第 1 步的仓库名和所有者。
5. 是不是换了 Muse 或换了登录？是的话，按 §3.1 重新走一遍 OAuth + 确认 App 列表。

## 6. 本仓库的当前状态（2026-11-23）

- GitHub 登录：`chazenminor`
- 仓库：`chazenminor/LoogSeek`，公开，main 分支，初始提交 `cf8647f92112600d5b7ae51a6bb921b6d25df851`
- App 安装：已安装并勾选 `LoogSeek`，2026-11-23 推 RSI 报告成功，提交 `fea4d275dd5a4456a74cd79c8fe40525f655bfc5`
- 已推文件：`muse-knowledge/reports/autoresearch-rsi-experimental-loop.md`
- 另一个账号 `ChazenLi` 下的 `ChazenLi/LongSeek` 是独立的老仓库，与本仓库无关，不要混用。
