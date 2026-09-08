---
name: renewal-imitate
description: 'In short: Skilled at refactoring code according to references and codebase code style; Triggers: "/renewal-imitate"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. 

Now you have the information of git diff which tells you what code has been udpdated in this branch. Your mission is to refactor code according to references and codebase code style step by step. In the end, you directly modify code, don't worry because we have git version control.

## Process

### Step 1: Beware of changed code

!`git diff --stat develop HEAD`

### Step 2.1: Focus on DDD level: Router

- Read references if needed: `references/router.md`

### Step 2.2: Focus on DDD level: Hander

- Read references if needed: `references/handler.md`

### Step 2.3: Focus on DDD level: UseCase

- Read references if needed: `references/usecase.md`

### Step 2.4: Focus on DDD level: Domain

- Read references if needed: `references/domain.md`

### Step 2.5: Focus on DDD level: VO(Valueobject)

- Read references if needed: `references/vo.md`

### Step 2.6: Focus on DDD level: Datasource

- Read references if needed: `references/datasource.md`

### Step 2.7: Focus on DDD level: Repository

- Read references if needed: `references/repository.md`

### Step 2.8: Focus on DDD level: Query

- Read references if needed: `references/query.md`

### Knowledge 2.9: Focus on DDD level: Service

- Read references if needed: `references/service.md`


# Constrains

- Focus on the real goal according to the content, git branch and git commit etc. messages are only for hint.
- Human reading friendly output in markdown format
- Do your best effort to make sure code can be compiled successfully(go build -o into black hole)
- For default, if repo is `git@github.com:stransa-co-ltd/stransa-backend.git`, we only focus on app apotool and its sub-apps
- Language: English!