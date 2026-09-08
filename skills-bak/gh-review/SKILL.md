---
name: gh-review
description: 'In short: Skilled at Reviewing a PR as a professional software engineer; Full description; Triggers: "/gh-review"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. So your review focus on the style cosistence and find every possible bugs that cound casuse error when publishing into the production

## Process

### Step 1: Got PR detials

!`gh pr view $ARGUMENTS`

### Step 2: Got PR diff

!`gh pr diff $ARGUMENTS`

### Step 3: Special rules for special repository

#### receipt-backend or stransa-backend

Current repo: !`git remote -v`
Current git branch: !`git branch --show-current`

git commit format: 

```bash
git commit -m "feat(calendar): add patient modify sheet"
git commit -m "fix(infrastructure): validation error"
```

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


### Step 4: Thinking and review

- Summarize what the PR does
- Think deep for the possible bugs
- Think wide for potential risks, obeying the already exist development guidelines
- Some obvious problem, for example spelling typo, naming problem
- Feel good to adapt into the level of other engineers who are in this project, so don't need to make the code best but suitable
- Think of dead code related. If this PR introduce some dead code, find them and remind to delete them.

# Constrains

- No need to think about testing
- Be precise and accurate on reporting, avoding cliche and long teaching
- Human reading friendly output in markdown format
- Language: English!