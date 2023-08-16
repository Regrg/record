---
layout: post
---

### angularJS comment contains three parts: Header, Body, Footer

#### Header
- `one line`
- contains three blocks: `type`(must), `scope`(optional) and `subject`(must)

type can be:
|type|description|
|-|-|
|feat(feature)|new feature|
|fix|bug fix|
|docs|documetal change|
|style|style change(do not affect source code)|
|test|add test code|
|chore|build process, auxiliary tools, libraries, or documentation generation|

	- scope is the affection place of region of the changes
		ex: $location, $browser, etc
	- subject is short description of the change. `Limit is 50 characters`
		- use present tense(change, not changes or changed)
		- no capital letter at first
		- no dot at the end

#### Body
- detailed description of the commit. Can separate to multiple lines.
- use present tense(change, not changes or changed)
- no capital letter at first
- no dot at the end

#### Footer
- used under two conditions: 1. incompatible commit 2. issue fixed

#### Full Example:

``` bash
_type_(_scope_): _subject_
#BLANK LINE#
_body_
#BLANK LINE#
_footer_
```

#### Revert
- if the commit reverts a previous commit, its header should begin with `revert`, followed by the header of the reverted commit. The body should also say: `This reverts commit <has>.`, where the hash is the SHA of the commit being reverted.
Ex:
``` bash
revert: feat(apple): remove `pineapple` syntax

This revert commit 326d91765264f40a48b373dyjl35725fb3cda111
```

#### CHANGELOG.md
- if commit message obeys above angular rules, you can easily generate CHANGELOG.md from "git log"
	- `git log <last_tag> HEAD --pretty=format:%s` > CHANGELOG.md
