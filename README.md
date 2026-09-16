# nk-novel

An agent skill for [Claude Code](https://code.claude.com) and [OpenAI Codex](https://developers.openai.com/codex). Turn one story idea into a staged novel plan that holds together, plus a one-page visual story bible.

Part of [nickkk-skills](https://github.com/NickkkLian/nickkk-skills) — agent skills whose scripts were broken on purpose
before release to prove their checks react.

![nk-novel demo: one idea in, a finished page out](https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/nk-novel.gif)

## What it does

- Eight stages into one `story.json`: premise and one-sentence retelling, world rules and what each costs, characters (want, fear, secret), an event spine where each event says what changed, acts and beats, clues and open questions, chapters.
- `story_check.py` finds timeline contradictions, confusable names, clues never paid off, missing motivation, too many open threads at once, missing caps, and a plot that hangs entirely on one rule.
- `bible.py` renders the plan as one HTML page: cast, relationship map, event spine, clue and question tables, chapters and the check results, in three themes with light and dark.
- Standard library only. Each check has a sample only it catches, and each was broken on purpose to prove the self-test goes red.

The full procedure, the boundaries and where the rules came from are in [SKILL.md](SKILL.md).

## How it works

1. Premise and retelling
2. World and its costs
3. Characters
4. Event spine
5. Structure
6. Consequences
7. Chapters
8. Check and render

## Install

Pick one of four ways: three for Claude Code, one for OpenAI Codex. Skills load when a session starts, so open a **new** session after installing.

### 1 · Terminal, one command

```bash
git clone https://github.com/NickkkLian/nk-novel ~/.claude/skills/nk-novel
```

1. Run the command above (for one project only, clone into `.claude/skills/nk-novel` inside that project).
2. Start a new Claude Code session.
3. Check it loaded: type `/nk-novel` — it appears in the slash-command menu. Or just ask for the task; the skill triggers on its own.

### 2 · Claude Code in a terminal session (plugin)

The plugin route goes through the [nickkk-skills](https://github.com/NickkkLian/nickkk-skills) marketplace. Add it once; after that each skill is one command.

```
/plugin marketplace add NickkkLian/nickkk-skills
/plugin install nk-novel@nickkk-skills
```

1. In a Claude Code session, run the first line (once per machine).
2. Run the second line.
3. Start a new session (or run `/reload-plugins`). The skill shows up as `nk-novel:nk-novel`.

Without opening a session, the same two steps work from a shell: `claude plugin marketplace add NickkkLian/nickkk-skills` then `claude plugin install nk-novel@nickkk-skills`.

### 3 · Claude desktop app (Code tab)

**Add the marketplace first — Discover only searches marketplaces you have already added.**

<img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/panel-route.gif" alt="Adding the marketplace and installing a skill in the desktop app" width="640">

<sub>The repository list in this recording shows the recorder's own repositories because a GitHub account is connected; yours will show yours. Type the full name as in step 4.</sub>

1. In the chat box, type `/plugin marketplace` and press Enter (or open **Settings → Customize → Plugins**). The **Plugins** panel opens.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step1-type-plugin-marketplace.png" alt="/plugin marketplace typed in the chat box" width="480">
2. Top right, open **Add ▾** and choose **Add marketplace**.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step2-add-menu.png" alt="The Add menu with Add marketplace" width="480">
3. Choose **Add from a repository**.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step3-add-from-repository.png" alt="Add marketplace dialog: Add from a repository" width="480">
4. In **URL**, type the full `NickkkLian/nickkk-skills`. At the bottom of the list choose the row **Use "NickkkLian/nickkk-skills"**, then press **Sync**.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step4-url-then-sync.png" alt="URL filled in, Sync button" width="480">
5. You land on **Discover**, filtered to the new marketplace (**Filter · 1**). Find **Nk novel** and press **Add**. Installed ones show **✓ Added**.
   <br><img src="https://raw.githubusercontent.com/NickkkLian/nickkk-skills/main/gallery/panel-route/step5-discover-add.png" alt="Discover list with Added and Add buttons" width="480">
6. Close the panel and start a new session.

To try it for one session without installing anything: `claude --plugin-dir ./nk-novel` from a clone.

### 4 · OpenAI Codex CLI

```bash
git clone https://github.com/NickkkLian/nk-novel.git ~/.agents/skills/nk-novel
```

1. Run the command above (for one project only, clone into `.agents/skills/nk-novel` inside that project).
2. Start a new Codex session.
3. Check it loaded, without spending a model call: `codex debug prompt-input | grep -o -- '- nk-novel[a-z0-9:-]*' | sort -u` prints `- nk-novel:nk-novel:`. Codex adds the `nk-novel:` prefix because this repository also carries a Claude Code plugin manifest. Ask for the task and the skill triggers on its own, or type `$` and pick it from the list.

## Compatibility

| Agent | Tested | What was checked |
|---|---|---|
| Claude Code (CLI 2.1.173, macOS) | yes | In a fresh project with an isolated Claude config, inside a macOS sandbox that blocked reading the tester's ~/.claude folder (settings, session history, memory), Desktop, Documents and Downloads, SSH keys and git identity, a plain request that never names the skill triggered it and it ran its bundled script. |
| OpenAI Codex CLI (0.154.0-alpha.6.2, gpt-5.6-sol, low reasoning, macOS) | yes | Copied into `~/.agents/skills` of a temporary home (the folder route 4 clones into), in a fresh project, without the user's Codex config. From a plain request that never names the skill, Codex read SKILL.md, wrote story.json through the stages, ran `scripts/story_check.py --strict` (0 errors, 0 warnings) and rendered the page with `scripts/bible.py`. The copy was the skill folder without this repository's Claude Code plugin manifest (`.claude-plugin/`), so Codex listed it as `nk-novel:`, not the `nk-novel:nk-novel:` that route 4 installs. |
| Cursor, Gemini CLI | no | Not tested. Their documentation says both read `~/.agents/skills`, the folder route 4 clones into; Gemini CLI asks before it activates a skill. |

In this skill's Codex run, every call into the skill folder's scripts/ used that folder's absolute path. This skill's frontmatter uses only name, description, license and metadata.

## Verify

```bash
python3 scripts/bible.py --selftest
python3 scripts/story_check.py --selftest
```

Standard library only, Python 3.9+. Before publishing, the guarded lines of each script were
mutated one at a time in a sandbox copy and the self-test was confirmed to go red on the named
assertion, without a traceback; the unmutated control stayed green.

## Limits

- The checks read structure, not quality: a plan with zero findings can still be dull, and a strange plan can be the right one.
- Reader load is approximated by counting open questions and unpaid clues; it is a prompt to reread, not a measurement of a reader.
- The retelling test catches meta-layers only if the retelling is honest; an agent can always write a sentence that sounds simple.
- The plan is the author's. The stages ask questions; they do not decide what the story is about.

## License

MIT. Read a script before letting it run in your environment.
