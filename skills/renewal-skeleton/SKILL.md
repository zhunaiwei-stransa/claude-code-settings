---
name: renewal-skeleton
description: 'In short: Skilled at initialize pkgs and files skeleton in the X project called Apotool Renewal; Triggers: "/renewal-skeleton"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. 

Now according to the arguments, do job in initializing pkgs and files skeleton for apps/apotool which containes several sub-apps, in which calendar is the fact standard , acting like create-app in frontend. And you don't need to write any bissiness logic because you only know little but api naming or datasource name.

## Process

### Step 1: Got Arguments

Arguments will imply what layers you might work with. It might containers several hints:

1. API(optional): The API that we want to implement, it is not full path but contains necessary information; `example: /calendar/v1/reservations/:id/cancels` As we have server sub-app, if the api contains no app name, reject this request. If api starts with `/patient/v1`, it means you sholud do for sub-app patient. Sometimes if user have mistake to let you generate sleleton for existing api, reject this request.
2. Router(default): default to use file path to hint that work starts with this path from top to down
3. Entity(optional): use file path to hint that work starts with this path from botton to up
4. Hanlder(default): default to create hanlder layer skeleton
5. UseCase(default): default to create usecase layer skeleton
6. Query(optional): use keywork `query` to hint work with layer: query
7. Domain(optional): use keywork `domain` to hint work with layers: domain, vo, repository
8. Instruction(optional): at last, user may give more detailed instuction to direct you to finish some work
9. Service(optional): use keywork `service` to hint work with layer: service, service co-operate with usecase

Current Arguments: $ARGUMENTS

### Step 2: Know the heading code I have git committed

!`git diff --stat develop..HEAD`

### Step 3: Work with layers you parsed

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

There are some special rules for some layers:

- Layer Repository: 
1. Skeleton Never generate business code, instead it only write cliche helper code, such as `db := `, `.Model()` and finnaly use `panic("TODO")` to left the work later.
2. Do not generate unrelated code even though example is a whole view. For example, when generate skeleton for Create API, no need to generate Repository Update method.

- Layer Query:
1. Skeleton Never generate real code to implemenet Query interface, instead it use `panic("TODO")` to tell engineer to left the work later.

- Layer UseCase:
1. `panic("TODO")` sholud be never used in this kind of "business logic" layer, do all your best in usecase later.

# Constrains

- Normally, appended code into the end of the file
- Human reading frindly output in markdown format
- Do your best effort to make sure code can be compiled succfully
- Language: English!