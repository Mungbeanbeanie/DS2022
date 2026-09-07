# Getting Started with Git / GitHub

Git keeps **history on your laptop**. GitHub is a **shared copy** of that history. Today's loop is: clone → change → add → commit → push / pull. (The next demo is collaboration and conflicts.)

Instructor demo repo: [https://github.com/ksiller/ds2022-demo](https://github.com/ksiller/ds2022-demo)

Work in `~/ds2022-fall-26`. Do **not** clone inside another Git repository.

If Git asks for a password, paste your **Personal Access Token**, not your GitHub password. The first `git commit` on a new laptop may also ask you to set your name and email (`git config --global user.name` and `user.email`).

## 1. Clone

Copy the repo from GitHub onto your machine:

```bash
mkdir -p ~/ds2022-fall-26
cd ~/ds2022-fall-26
git clone https://github.com/ksiller/ds2022-demo.git
cd ds2022-demo
ls -la
```

`ls -la` shows the project files plus a hidden `.git` folder (that is Git's local history).

## 2. Where is the shared copy?

```bash
git remote -v
```

`origin` is a nickname for the GitHub URL you cloned from.

## 3. Is anything waiting to be saved?

```bash
git status
```

On a fresh clone you should be on `main` and "working tree clean."

## 4. Make a change, then save a snapshot

Create two files. We will commit only one of them.

```bash
echo "Hello Git" > hello.txt
echo "do not commit me" > scratch.txt
git status
```

Both files are **untracked**. Stage them (tell Git what to include in the next snapshot):

```bash
git add hello.txt scratch.txt
git status
```

Now they are **staged**.

## 5. Unstage the file you do not want

`git add` is not permanent. `git rm --cached` takes a file out of the snapshot but **leaves it on disk**:

```bash
git rm --cached scratch.txt
git status
```

`hello.txt` is still staged. `scratch.txt` is untracked again.

```bash
git commit -m "Add hello.txt"
git status
```

A **commit** is a saved snapshot. `scratch.txt` is still in your folder; it is just not in Git's history.

## 6. Push (instructor)

`git push` publishes commits to GitHub. You do not have write access to this demo repo, so **watch the instructor** run:

```bash
git push origin main
```

Then refresh [https://github.com/ksiller/ds2022-demo](https://github.com/ksiller/ds2022-demo) and confirm the new file is there.

## 7. Pull (everyone)

When GitHub has commits you do not have yet, update your laptop:

```bash
git pull origin main
git status
ls
```

You should see whatever the instructor just pushed.

## Same loop in Cursor

- Switch to the **IDE Window** if needed (**File → Open IDE**).
- **File → Open Folder...** and open `ds2022-fall-26/ds2022-demo`.
- **View → Explorer** to see files. `scratch.txt` is on disk; Git is not tracking it.
- Open **Source Control** (branch icon in the left activity bar). This is `git status` as a list: changes to stage, a box for the commit message, and a commit button (same as `git add` then `git commit`).

You will **push from a repo you own** in the next exercise.
