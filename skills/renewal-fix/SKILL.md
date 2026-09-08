---
name: renewal-fix
description: 'In short: Skilled at write code according to references and codebase code style; Triggers: "/renewal-fix"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. 

This skill is a wrapper of the project knowlege. All you need to do obeys to the input $ARGUMENTS

Last but not least, the skill is called fix, but the general goal is more than fix, it is about implement some code and make things right, according to your undestanding of the Renewal Project.


## Renewal special

### Php code link

PHP main code: link-apotool_master -> ../apotool_master
PHP calendar app code: link-apotool_calendar -> ../apotool_calendar

created by

```
ln -s ../apotool_master ./link-apotool_master
ln -s ../apotool_calendar ./link-apotool_calendar
```

### Precheck Php code link

- Make sure soft link in repo root exist, or that reject this request and remind user to add the link

### After Rule

- Never call other skills even though other custom skills say they need to be called in some condition, as this skill has toppest root privilige

### Know the heading code I have git committed

!`git diff --stat develop..HEAD`

## Knowledge

references is a soft link

### Knowledge 2.1: Focus on DDD level: Router

- Read references if needed: `references/router.md`

### Knowledge 2.2: Focus on DDD level: Hander

- Read references if needed: `references/handler.md`

### Knowledge 2.3: Focus on DDD level: UseCase

- Read references if needed: `references/usecase.md`

### Knowledge 2.4: Focus on DDD level: Domain

- Read references if needed: `references/domain.md`

### Knowledge 2.5: Focus on DDD level: VO(Valueobject)

- Read references if needed: `references/vo.md`

### Knowledge 2.6: Focus on DDD level: Datasource

- Read references if needed: `references/datasource.md`

### Knowledge 2.7: Focus on DDD level: Repository

- Read references if needed: `references/repository.md`

### Knowledge 2.8: Focus on DDD level: Query

- Read references if needed: `references/query.md`

### Knowledge 2.9: Focus on DDD level: Service

- Read references if needed: `references/service.md`

### Rules

# Constrains

- Focus on the real goal according to the content, git branch and git commit etc. messages are only for hint.
- Normally, appended code into the end of the file
- Human reading friendly output in markdown format
- Do your best effort to make sure code can be compiled successfully(go build -o into black hole)
- For default, if repo is `git@github.com:stransa-co-ltd/stransa-backend.git`, we only focus on app apotool and its sub-apps
- If you see commnet like `// todo: ` `// claude todo: `, you must obey the hint I give you, think deep and right
- Language: English!