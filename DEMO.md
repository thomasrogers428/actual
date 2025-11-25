# Claude Code Demo Script

## 🎯 Demo Overview

This repository is configured to showcase Claude Code's key features in a real-world codebase.

## ⏱️ Quick Setup (30 seconds)

```bash
cd /Users/tommyrogers/Desktop/ActualBudget/actual
yarn start:browser
# App runs on http://localhost:3001
```

---

## 📋 Demo Flow (5-7 minutes)

### 1️⃣ **Company Standards** (1 min)

**What to show:**

- Open `.claude.md` in VS Code
- Highlight the coding standards section

**What to say:**

> "Claude Code automatically reads project guidelines from .claude.md files. Here we've defined our TypeScript standards, security requirements, and testing policies. Claude will follow these throughout the entire session."

**Interactive moment:**
Ask Claude: _"What are the coding standards for this project?"_
→ Claude references the .claude.md file

### 2️⃣ **Custom Commands** (30 sec)

**What to show:**
Type `/test` in Claude Code

**What to say:**

> "Teams can create custom slash commands. Type `/test` and Claude runs your full test suite. `/test-budget` runs just the budget calculation tests."

### 3️⃣ **Permission Model** (1 min)

**What to show:**

- Open `.claude/settings.json`
- Point out the commented sections

**What to say:**

> "Security is built-in. This config pre-approves safe operations like reading files and running tests, but prompts for sensitive actions like writing code or git commits. Perfect for demos - but you'd lock this down in production."

### 4️⃣ **Context Awareness** (2 min)

**What to show:**
Navigate to `packages/loot-core/.claude.md`

**What to say:**

> "Each package has its own guidelines. Loot-core is the budget calculation engine - notice it warns that amounts are stored as integers in cents, not floats."

**Interactive moment:**
Ask Claude: _"Show me how to work with budget amounts in loot-core"_
→ Claude references the package-specific guidelines

### 5️⃣ **The Bug Hunt** (2-3 min) ⭐ **MAIN DEMO**

**What to show:**

1. Open browser at http://localhost:3001
2. Show the demo budget with transactions
3. Point out budget totals seem wrong

**What to say:**

> "I loaded some test data, but the budget totals are off. Let's have Claude investigate."

**Interactive script:**

```
You: "The budget category totals are showing incorrect amounts.
      Can you investigate what might be wrong?"

→ Claude searches the codebase
→ Finds util.ts with sumAmounts function
→ Identifies the bug (starts at 100 instead of 0)

You: "Fix the bug in util.ts"

→ Permission dialog appears! (Shows security model)
→ Click "Accept"
→ Claude fixes: changes line 11 from 100 to 0
→ Explains the fix

You: "Commit this fix"

→ Another permission prompt for git operations
→ Claude creates descriptive commit message
→ Shows git workflow integration
```

### 6️⃣ **Closing Points** (30 sec)

**Key Takeaways to mention:**

- ✅ **Context Aware**: Reads .claude.md files automatically
- ✅ **Secure by Default**: Permission model for sensitive operations
- ✅ **Team Workflows**: Custom commands, coding standards
- ✅ **Real Codebase**: Works with actual TypeScript/React project
- ✅ **Git Integration**: Commits, PRs, reviews built-in

---

## 🎬 Backup Demo Ideas

### If Extra Time:

**A) Code Generation:**

```
"Create a new React component for displaying transaction history,
 following our component standards"
```

→ Shows Claude using desktop-client/.claude.md guidelines

**B) Testing:**

```
"Write a test for the sumAmounts function we just fixed"
```

→ Shows AI test generation with proper assertions

**C) Documentation:**

```
"Add JSDoc comments to the sumAmounts function"
```

→ Quick edit, shows code improvement

**D) Code Review:**

```
"Review the changes in util.ts and explain if there are any edge cases"
```

→ Shows Claude as code review assistant

---

## 📊 Key Files for Demo

| File                                           | Purpose            | When to Show         |
| ---------------------------------------------- | ------------------ | -------------------- |
| `.claude.md`                                   | Root standards     | Start of demo        |
| `.claude/settings.json`                        | Permissions        | Security discussion  |
| `packages/loot-core/.claude.md`                | Package guidelines | Context awareness    |
| `packages/loot-core/src/server/budget/util.ts` | The bug!           | Main demo            |
| `data/Demo-Budget/`                            | Test data          | Shows real budget    |
| `.claude/commands/`                            | Custom shortcuts   | Productivity feature |

---

## 💡 Pro Tips

1. **Keep the browser visible** - Shows real impact of code changes
2. **Highlight permission prompts** - Key differentiator
3. **Show .claude.md files** - Unique Claude Code feature
4. **Let Claude search** - Don't tell it where the bug is
5. **Emphasize team benefits** - Standards enforcement, onboarding

---

## 🐛 The Intentional Bug

**Location:** `packages/loot-core/src/server/budget/util.ts:11`

**Bug:**

```typescript
amounts.reduce((total, amount) => {
  return total + number(amount);
}, 100); // ← Should be 0, not 100!
```

**Impact:** Every budget category total is $1.00 too high

**Fix:** Change `100` to `0`

This bug is **intentional** for the demo. It's:

- Easy to explain
- Has visible impact in the UI
- Demonstrates Claude's code analysis
- Shows git workflow

---

## 🎤 Talking Points During Loading

While waiting for commands to run:

1. **"This is a real production codebase"** - 10+ packages, TypeScript, React
2. **"The .claude.md files cost nothing"** - Just markdown, automatic context
3. **"Permission model scales"** - From solo devs to enterprise teams
4. **"Works with existing tools"** - Git, tests, builds - not a new workflow

---

## 📝 Notes

- Demo data is pre-loaded in `data/Demo-Budget/`
- Settings optimized for demos (see comments in settings.json)
- Bug is intentional and easy to fix
- All .claude.md files have real, useful content

Good luck with your demo! 🚀
