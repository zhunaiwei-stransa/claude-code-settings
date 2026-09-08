---
name: renewal-plan
description: 'In short: Forced to use Agent plan mode to think deep and fully, and generate a plan for user request in the Project Renewal; Triggers: "/renewal-plan"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. 

You need take serious of the input $ARGUMENTS . You are forced to use Agent plan mode to think deep and fully, and generate a plan for user request in the Project Renewal

The user may not give you all the details, but you have to think all things for user. This skill only works for Project Renewal, so you have the knowledge all you need, both from skill and local codebase.

Project Renewal is a refactoring project which use Go to replace php, you only concern the backend part but your api design need to be frontend friendly.

When this skill is used, it means that this job is not that easily done in normal mode, so we must think first and plan first.

For our cooperation good, we can divided the code we matter into 3 levels in consensus section.

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

- Never call other skills even though other skills say they need to be called in some condition, as this skill has toppest root privilige

## consensus

### consensus 1: auto as you can

If the task is clear and no pending choise that needs user decide, just automatically finish the code.

### consensus 2: think smart

If the task raise some small difference between PHP and Go, try your best to first write code, and comment code with `// TODO(LOOKATME)`

### consensus 3: we can do it later

If the task raise problem you cannot handle as consensus 3 level, design a interface to make bin runnable, but left a `panic("LOOKATME")` implement and other comments to tell why you cannot handle it, limited 200 words, with reference PHP code path.

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

# Constrains

- Focus on the real goal according to the content, git branch and git commit etc. messages are only for hint.
- Normally, appended code into the end of the file
- Human reading friendly output in markdown format
- Do your best effort to make sure code can be compiled successfully(go build -o into black hole)
- For default, if repo is `git@github.com:stransa-co-ltd/stransa-backend.git`, we only focus on app apotool and its sub-apps
- Do not need to comment purpose for consensus 1 level code
- Language: English!