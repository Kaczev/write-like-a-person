# write-like-a-person

给 AI agent 用的写作技能：让生成的文字读起来像人写的，而不是机器写的。

技能本体是 `SKILL.md`：先问两条（读者能不能一遍读懂、懂行的人会不会这么说），再问这篇东西该有哪些段落，然后是十二条具体的坏习惯，每条配一组通用场景的改前/改后例子，最后是写完之后的自检。`examples/corrections.md` 另放真实修改案例（物理论文、量子科普、口语稿），用来看规则怎么落地，不是模板。

## 它管什么

1. 句子要写完，别让读者自己补主语和宾语。
2. 叫出名字，别用「这一层」「那条线」这种指代。
3. 否定要放在读者能接住的位置：别反驳读者没提过的东西，也别把否定挂在句尾。
4. 别让抽象名词做人才能做的动作。
5. 动词和宾语要真的能搭在一起，别把英文短语逐词翻成中文。
6. 每个连接词都要有前提；没有前提的「所以」是装饰。
7. 别给自己的写作配旁白。
8. 别替读者下结论，也别表演严谨。
9. 比喻要么是题材自带的，要么是语言里现成的；别为一句句子现造一个。
10. 句子长度要有变化。
11. 口语稿里标出翻页的位置。
12. 结尾要往前推一步，不要复述正文。

另外两节：写之前的「这篇东西是什么」，以及写完之后的自检。口语稿另有字数与时长约束。

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

**别的 agent**：把 `SKILL.md` 复制进那个 agent 的技能目录；`examples/` 可选，复制过去能让它看到真实案例。它是纯 markdown，frontmatter 只有 `name` 和 `description`。

## 维护

技能还在改，改动落在 `SKILL.md` 和 `examples/corrections.md` 上。

### 同步：push 就会自动跑

日常只有一个方法：把 `SKILL.md` 或 `examples/**` 的改动 push 到 `main`。`.github/workflows/sync-skill.yml` 会自动：

1. checkout 本仓库和 `Kaczev/dsh-KazMode`；
2. 跑 `node scripts/sync-skill.mjs --from "$GITHUB_WORKSPACE"`；
3. 有变化就 commit + push 到 Kaz 的 `main`。

不用去 Actions 页面点 `Run workflow`；那个按钮只用于测试或补跑。触发条件是 push 到 `main` 且改到 `SKILL.md` 或 `examples/**`；只改 README 不会触发。

第一次要配 `KAZ_SYNC_TOKEN`（fine-grained PAT，只给 `dsh-KazMode` 的 `Contents: Read and write`）：

1. GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token。
2. Repository access 选 Only select repositories → `dsh-KazMode`；Repository permissions 只给 `Contents: Read and write`。
3. 生成后复制，去 `write-like-a-person` 仓库的 Settings → Secrets and variables → Actions → New repository secret，名字填 `KAZ_SYNC_TOKEN`，值贴进去。不要把 token 贴进聊天或提交进仓库。
4. 配好后在 Actions 页面点一次 `Run workflow` 做首次验收；之后就靠 push，不用再点。

PAT 会过期；过期后 workflow 会红，续一个、更新 secret 即可。

### 本机 Kaz

workflow 只改 GitHub 上的 Kaz 仓库。本机 Kaz 仓库 `git pull`，桌面端开新会话；已经开着的会话保留启动时的那份。

`dsh-KazMode` 仓库持有这个文件的一份副本，随预设一起发版。那份副本由 `scripts/sync-skill.mjs` 同步过来（`SKILL.md` 和 `examples/` 都搬），不要直接在 Kaz 那边编辑，否则下一次同步会把它覆盖掉。

有些工作区会在 `.agents\skills\` 下另放一份手工副本。那份不在同步链里，而且可能遮住 Kaz 内置的那份。要让“一个方法”成立，最好删掉它，让工作区直接用 Kaz 的副本；至少要知道技能调用返回的 base directory 是哪一份。

### 备用：workflow 坏了才手动跑

```powershell
cd C:\Users\你\Documents\GitHub\dsh-KazMode
node scripts/sync-skill.mjs
```

手动跑完先 commit，别和 workflow 同时跑；两边都会写 `SOURCE.json`，虽然现在的脚本会把 commit 记成同一个正文提交，但没必要制造一次竞态。

发版时打 tag，Kaz 那边的同步记录会写下对应的 commit：

```powershell
git tag 0.1.0
git push origin main --tags
```

## 授权

MIT，见 `LICENSE.md`。
