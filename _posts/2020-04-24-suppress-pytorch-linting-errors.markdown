---
layout: post
title:  "Ways to suppress 'torch' has no member 'xxx' errors in VS Code"
date:   2020-04-25 10:08:00 +0800
categories: pytorch pylint vscode flake8
---

reference: https://github.com/pytorch/pytorch/issues/701

# Solution 1: edit `.pylintrc`

 - step 1

Check your project directory and find `.pylintrc`. If not found, generate one with

```bash
$ pylint --generate-rcfile > .pylintrc
```

or just create an with

```bash
$ touch .pylintrc
```

Both ways work for me.

 - step 2

Edit `.pylintrc`. If you generated one, add the line starts with `generated-members` into the `[TYPECHECK]` section. If not, copy all the contents in the code block into your rc file.

```ini
[TYPECHECK]
# List of members which are set dynamically and missed by pylint inference
# system, and so shouldn't trigger E1101 when accessed. Python regular
# expressions are accepted.
generated-members=numpy.*,torch.*
```


# Solution 2:

*NOT VERIFIED*

According to [cardoso-neto's comment](https://github.com/pytorch/pytorch/issues/701#issuecomment-438215838), you can just add

```json
"python.linting.pylintArgs": [
    "--generated-members=numpy.* ,torch.* ,cv2.* , cv.*"
]
```

to VS Code user settings.


# Solution 3:

*NOT VERIFIED*

According to [SelvamArul's comment](https://github.com/pytorch/pytorch/issues/701#issuecomment-596440868), selecting `flake8` instead of `pylint` in VS Code settings by `Ctrl+Shift+P` -> `Select linter` -> `flake8` works too.