---
name: json-tag
description: 'In short: Skilled at add json tag for Go struct; Triggers: "/json-tag"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. 

Now add json tag for Go struct, obeying the constrains.

## Process

### Step 1: Beware of file path

struct location of file path: $ARGUMENTS

### Step 2: Knowledge in need

### Knowledge 2.2: Focus on DDD level: Hander

- Read references if needed: `references/handler.md`

### Step 3: Do tagging

In the file, the root struct usally names endding with `Output`. Tag all the structs rooting from this struct. The json tag name is exactly inferenced from the field name, thanks to the Go snake naming convention.

There are some other things you can help when doing:

1. If the filed name have possible problem like typo, points them all out in the end, but never change it on yourself.
2. If some json tag exists, it might be tagged before, you must make sure the json tag name consistent with the field name.

# Constrains

- No matter whether type is pointer, do not use omitempty tag 
- Must make sure json tag is lowerCamelCase like `psProductID`.
- Human reading frindly output in markdown format
- Do your best effort to make sure code can be compiled succfully
- Language: English!