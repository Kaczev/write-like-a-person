# write-like-a-person

给 AI agent 用的写作技能：让生成的文字读起来像人写的，而不是机器写的。

技能本体只有一个文件 `SKILL.md`。里面是十二条具体的坏习惯，每条配一组改前和改后的例子，末尾是一遍自检清单。例子来自真实的修改记录，不是编出来的示范。

## 它管什么

1. 句子要写完，别让读者自己补主语和宾语。
2. 别反驳一个读者根本没提过的说法。
3. 叫出名字，别用「这一层」「那条线」这种指代。
4. 别让抽象名词做人才能做的动作。
5. 否定词别挂在句尾。
6. 别给自己的写作配旁白。
7. 别替读者下结论。
8. 比喻只在题材自带的时候保留，别生造。
9. 不用套话连接词（首先、其次、综上所述、值得注意的是……）。
10. 句子长度要有变化。
11. 口语稿里标出翻页的位置。
12. 结尾别总结，落在动作或结论上。

另外两节：写完之后的自检清单，以及口语稿的字数与时长约束。

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

技能还在改，改动直接落在 `SKILL.md` 上。

dsh-KazMode 仓库持有这个文件的一份副本，随预设一起发版。那份副本由那个仓库里的 `scripts/sync-skill.mjs` 同步过来，不要直接在 Kaz 那边编辑，否则下一次同步会把它覆盖掉。

发版时打 tag，Kaz 那边的同步记录会写下对应的 commit：

```powershell
git tag 0.2.0
git push origin main --tags
```

## 授权

MIT，见 `LICENSE.md`。
