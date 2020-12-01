---
layout: wiki
title: Committing Policy
---
## Committing Policy

Each commit MUST be associated with an issue. If there's no issue matching changes you are introducing then please open one.

- Commit message MUST start with `#N: ` where `N` is the ID of associated issue
- Commit message MUST start with capital letter
- Commit message MUST NOT have a trailing period

The automated check can be made against each commit by adding the file named `commit-msg` with the following content to `.git/hooks`:

```bash
#!/bin/sh

if ! head -1 "$1" | grep -q '^#[0-9]\+: '; then
    echo 'Aborting commit: the commit message must start with "#N: "'
    exit 1
fi

if head -1 "$1" | grep -q '^Issue #[0-9]\+: [a-z]'; then
    echo 'Aborting commit: the commit message must start with capital letter'
    exit 1
fi

if head -1 "$1" | grep -q '\.$'; then
    echo 'Aborting commit: the commit message must not have trailing period'
    exit 1
fi
```
