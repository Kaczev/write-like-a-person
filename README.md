# write-like-a-person

给 AI agent 用的写作技能：让生成的文字读起来像人写的，而不是机器写的。

技能本体是 [SKILL.md](SKILL.md)，用英文写成六个阶段的操作步骤：动笔前、起草、改段落、改句子、演讲稿专用检查、交付前。不放改写例文；每条指出处理对象、修改动作和执行条件。

## 它管什么

- 动笔前列出读者、用途、必须回答的问题和材料，先标出用户改定或指定保留的字句及结尾。
- 成稿后补漏答、调整段落次序、合并重复内容，删除没有实质信息的导语和自评。
- 逐句处理指代、缺失的执行者、动宾搭配、因果依据、否定范围、比喻和修饰。材料不能确认的指代不猜；没有依据的因果不能靠加“可能”留下。
- 不强制统一开头、结尾、句长或并列次数；用户需要的摘要、技术词和具体措辞不能机械删除。
- 演讲稿另查翻页提示、实际字数、语速、时长和可选删减。普通对话与语气管线不执行演讲稿步骤。
- 交付前对照原材料恢复误删的事实、条件、否定和承诺；不恢复已经撤去的无依据断言。

## 怎么用

**Kaz 模式（dsh）**：已经内置，不用单独装。

**其他 dsh 预设或 profile**：把仓库克隆成一个技能目录，再把它的父目录加进 `skill-filesystem` 的 `customSkillDirs`。技能目录名就是 agent 看到的技能名。

```powershell
git clone https://github.com/Kaczev/write-like-a-person.git "$env:USERPROFILE\skills\write-like-a-person"
```

```yaml
- id: skill-filesystem
  name: '@deepseek-ai/dsh-skill-filesystem'
  config:
    customSkillDirs:
      - C:\Users\你\skills
```

目录里没有 `SKILL.md` 的会被跳过，不会报错。路径写错时，技能是静默消失的，所以装完要去技能列表里确认它在。

**别的 agent**：把 `SKILL.md` 复制进那个 agent 的技能目录。它是纯 markdown，frontmatter 只有 `name` 和 `description`。

## 维护

技能还在改，改动落在 `SKILL.md` 上。

### 同步：push 就会自动跑

日常只有一个方法：把 `SKILL.md` 的改动 push 到 `main`。`.github/workflows/sync-skill.yml` 会自动：

1. checkout 本仓库和 `Kaczev/dsh-KazMode`；
2. 跑 `node scripts/sync-skill.mjs --from "$GITHUB_WORKSPACE"`；
3. 有变化就 commit + push 到 Kaz 的 `main`。

不用去 Actions 页面点 `Run workflow`；那个按钮只用于测试或补跑。触发条件是 push 到 `main` 且改到 `SKILL.md`；只改 README 不会触发。

第一次要配 `KAZ_SYNC_TOKEN`（fine-grained PAT，只给 `dsh-KazMode` 的 `Contents: Read and write`）：

1. GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token。
2. Repository access 选 Only select repositories → `dsh-KazMode`；Repository permissions 只给 `Contents: Read and write`。
3. 生成后复制，去 `write-like-a-person` 仓库的 Settings → Secrets and variables → Actions → New repository secret，名字填 `KAZ_SYNC_TOKEN`，值贴进去。不要把 token 贴进聊天或提交进仓库。
4. 配好后在 Actions 页面点一次 `Run workflow` 做首次验收；之后就靠 push，不用再点。

PAT 会过期；过期后 workflow 会红，续一个、更新 secret 即可。

### 本机 Kaz

workflow 只改 GitHub 上的 Kaz 仓库。本机 Kaz 仓库 `git pull`，桌面端开新会话；已经开着的会话保留启动时的那份。

`dsh-KazMode` 仓库持有这个文件的一份副本，随预设一起发版。那份副本由 `scripts/sync-skill.mjs` 同步过来（只搬 `SKILL.md`），不要直接在 Kaz 那边编辑，否则下一次同步会把它覆盖掉。

有些工作区会在 `.agents\skills\` 下另放一份手工副本。那份不在同步链里，而且可能遮住 Kaz 内置的那份。要让“一个方法”成立，最好删掉它，让工作区直接用 Kaz 的副本；至少要知道技能调用返回的 base directory 是哪一份。

### 备用：workflow 坏了才手动跑

```powershell
cd C:\Users\你\Documents\GitHub\dsh-KazMode
node scripts/sync-skill.mjs
```

手动跑完先 commit，别和 workflow 同时跑；两边都会写 `SOURCE.json`，虽然现在的脚本会把 commit 记成同一个正文提交，但没必要制造一次竞态。

发版时打 tag，Kaz 那边的同步记录会写下对应的 commit：

```powershell
git tag 0.1.3
git push origin main --tags
```

## 授权

MIT，见 `LICENSE.md`。
