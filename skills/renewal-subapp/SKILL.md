---
name: renewal-subapp
description: 'In short: Skilled at initialize a new sub-app in the Renewal project; Triggers: "/renewal-subapp"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. 

Now according to the arguments, do job in initializing a new sub-app for apps/apotool which containes several sub-apps, in which calendar is the product standard, acting like create-app in frontend.

As create-app is an expensive operation. you are fored to use claude plan mode to think deep and comprehensively.

## Process

### Step 1: Parse Arguments

Arguments will imply the sub-app name and other things. It containes several parts:

1. Name(required): 
1. Added APIs after initialed(optional): The apis you receive are totally exactly-same api between sub-apps, which is dulplicated implemented just for api division. You need to implement these apis with reference to the standard sub-app implementation.

Current Arguments: $ARGUMENTS

### Step 2: Build your plan roughly

Your plan should be perfect as possible becuase a new sub-app will be deployed into Cloud, that means any mistake will make the first deployment failed.

I give you some must-to-be and then you think the remain work based on codebase:

- apps/apotool/cmd/
- apps/apotool/Makefile
- apps/apotool/Dockerfile
- use the same authorization as strandard use
- error
- env
- .env.{subapp}
- .github/labeler.yml

### Step 3: Fine turn with knowledge

In this step, you should work with layers that hinted step by step. In each layer, you may need to read the references in need as the follwing index.

code references index:

- `references/router.md`
- `references/handler.md`
- `references/usecase.md`
- `references/domain.md`
- `references/vo.md`
- `references/datasource.md`
- `references/repository.md`
- `references/query.md`
- `references/service.md`

# Constrains

- Normally, appended code into the end of the file
- Human reading frindly output in markdown format
- Do your best effort to make sure code can be compiled succfully
- Language: English!