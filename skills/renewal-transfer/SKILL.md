---
name: renewal-transfer
description: 'In short: Skilled at write code in project called apotool-renewal, to transfer code or api-standards from apps/apotool/internal/calendar to apps/apotool/internal/patient; Triggers: "/renewal-transfer"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. 

This skill is a specialized skill for apps/apotool to transfer code or api standards from sub-app calendar to sub-app patient. Sometimes the api of 2 sub-apps are totally same, sometimes they are slightly different. Your job is to transfer the code of specific one API from one sub-app to another with respecting to their goal difference, for the consistence of code styling.

The trick is that if their APIs implement are almost the same but different, their apis likely have diffences(hint that it almost certainly has been transfered before). Anyway, you should follow the user instruction: $ARGUMENTS

## Knowledge

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
- Human reading frindly output in markdown format
- Do your best effort to make sure code can be compiled succfully
- You must know that all sub-apps share codes of Domain VO Datasource Repository Service layer, but owns a private Query layer, which means when sub-app patient need to use a ability that has been implemented by sub-app calendar in shared layers, patient app must use it but not write itself
- Language: English!