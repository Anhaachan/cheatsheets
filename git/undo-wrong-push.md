# Git: Undo Wrong Push (Safe Workflow Guide)

When you push the wrong commit to a branch, there are two main scenarios:

1. Nobody has pulled yet → rewrite history (`reset + force push`)
2. Someone already pulled → keep history consistent (`revert`)

---

## Case 1: Nobody has pulled yet (safe to rewrite history)

### Situation
A -- B -- C   (origin/main)

- A, B = good commits
- C = bad commit pushed by mistake
- Nobody else has pulled C yet

### Desired History
```
A -- B        (origin/main)
        \
         C   (correct-branch)
```
### Commands
```
git checkout -b backup-pushed
git push origin backup-pushed

git checkout main
git reset --hard HEAD~1
git push origin main --force

git checkout correct-branch
git merge backup-pushed     # OR: git cherry-pick <C>
git push origin correct-branch
```
### Result
main:     A -- B
correct-branch: A -- B -- C

---

## Case 2: Someone already pulled (must not rewrite history)

### Situation
A -- B -- C   (origin/main)

- C = bad commit
- Someone already pulled C

If you reset --hard and force push, their history diverges.
Use revert instead.

### Desired History
A -- B -- C -- D   (origin/main)
             \
              C   (correct-branch)

- D = new commit that undoes C
- C stays in history for consistency
- C is cherry-picked into the correct branch

### Commands
```
git checkout main
git revert <C>
git push origin main

git checkout correct-branch
git cherry-pick <C>
git push origin correct-branch
```
### Result
main:     A -- B -- C -- D
correct-branch: A -- B -- C

---

## Rule of Thumb
If nobody pulled → reset --hard + push --force
If someone pulled → revert + cherry-pick

---

## Quick Decision
Did anyone else pull the bad commit?
```
   ├─ No → Reset + force push
   │
   └─ Yes → Revert + cherry-pick
```
