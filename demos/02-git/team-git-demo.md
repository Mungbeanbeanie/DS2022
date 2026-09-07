# Collaboration with Git / GitHub

This demo is about **decentralized collaboration**. Each person works on their own copy of a shared project, then Git combines those copies. When two people change the same file, Git cannot guess the intended result, so it cannot merge automatically. That is a **merge conflict**. We will show you how to resolve one.

We will first share work that does *not* collide (unique filenames, automatic merge), then deliberately create a collision and resolve it as a team.

Instructor demo repo: [https://github.com/ksiller/ds2022-git-demo](https://github.com/ksiller/ds2022-git-demo)

## Step 1: Repository Setup

- One person (the "creator") sets up a new repository on GitHub. Initialize it with a README so GitHub creates a `main` branch. For this live demo, that repo is [https://github.com/ksiller/ds2022-git-demo](https://github.com/ksiller/ds2022-git-demo).
- The creator adds all group members as collaborators:
  - Go to **Settings** → **Collaborators** (under **Access** in the left sidebar) → **Add people**.
  - Search for each teammate by GitHub username and click **Add [username] to [repository]**. Each invited teammate must accept the invitation (email, GitHub notification, or the banner on the repository page) before they can clone and push.

## Step 2: Clone the Repository

- Open a terminal window.
- Using the command line, **all** group members clone the new repository to their own environment. Make sure you are **not** inside an existing Git repository (you don't want nested Git repositories).
- Change to `~/ds2022-fall-26` first (we created that directory last week; `mkdir -p` is safe to re-run if it is missing):

```bash
mkdir -p ~/ds2022-fall-26
cd ~/ds2022-fall-26
git clone https://github.com/ksiller/ds2022-git-demo.git
cd ds2022-git-demo
ls -la
```

`ls -la` should show the README plus the hidden `.git` directory.

## Step 3: Open the Cloned Repository as a Project in Cursor

- In Cursor, switch to the **IDE Window** if you are not already there (**File → Open IDE**, or confirm the File menu shows **Switch to Agent Windows**).
- Go to **File → New Window**. Then **File → Open Folder...** and navigate to `ds2022-fall-26` → `ds2022-git-demo`.
- Go to **View → Explorer**. This will open the folder/file explorer in the Cursor sidebar.

## Step 4: Create Unique Files

Each group member should create a new text file in their local repository. Use unique filenames so Git can merge everyone's work automatically in the next step (for example, `alice.txt`, `bob.txt`).

Since we learned about the CLI, let's practice our skills. If your Cursor window doesn't show a terminal, go to **Terminal → New Terminal**. By default, you should be in the top-level directory of the cloned repository. Confirm with `pwd` and then use `echo` with redirect to create the new file (replace `alice.txt` and the message with your own name):

```bash
pwd
echo "Hello from Alice" > alice.txt
```

The new file should show up in your project's **Explorer** sidebar. As an alternative to the command line, you can also create new files in the Cursor GUI: in the **Explorer** sidebar, right-click (on Mac: Control-click, or a two-finger tap) and select **New File**. Then use the editor to update the file's content.

## Step 5: Add, Commit, and Push Local Changes

Unique filenames avoid *file* collisions, but two people still cannot push new commits to `main` at the same time. Go **one person at a time**, clockwise from the creator. Each person (using their own filename):

```bash
git add alice.txt
git commit -m "Add alice.txt"
git push origin main
```

If `git push` is rejected because a teammate pushed first, you may see an error message like this (**Hint:** use the Cursor **Agents Window** if you'd like a deeper explanation of the error):

```text
! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/ksiller/ds2022-git-demo.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
```

Pull from `origin` (the GitHub copy) to integrate their work, then push again:

```bash
git pull origin main --merge
git push origin main
```

(The `--merge` flag is explicit and avoids warnings in newer Git versions.) Because the filenames are unique, Git can merge automatically (no conflict to resolve).

Repeat until everyone has pushed their file.

## Step 6: Verify on GitHub

Everyone: check the presence of the new files on GitHub by visiting the repository page.

## Step 7: Pull Latest Changes

Everyone: run the following command so every local copy has every teammate's unique file:

```bash
git pull origin main --merge
```

**So far, so good. Let's take it to the next level!**

## Step 8: Create Collision File

When collaborating, team members may be working in parallel on local copies of the *same* file. This leads to divergence, and file-version conflicts need to be resolved. Let's simulate that scenario.

Everyone: create a new file `collision.txt` in your local repository. The file should contain a single line with your first name and favorite animal. Add, commit, and push it to the remote repository on GitHub:

```bash
echo "Alice, cat" > collision.txt
git add collision.txt
git commit -m "Add collision.txt"
git push origin main
```

## Step 9: Resolve Merge Conflicts

**The early bird gets the worm:** If you are the first person to push the `collision.txt` file, you're in luck: the push should go through without a hitch. The others will see the same rejected-push error as in Step 5:

```text
! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/ksiller/ds2022-git-demo.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
```

This time the rejection is only the start. After you pull, Git will also report a **merge conflict**, because you and your teammate both edited `collision.txt`.

**To resolve the conflict:**

Starting with the group member next to the first person who successfully pushed, go clockwise and perform the following steps *one person at a time*:

1. Pull with merge to reconcile the differences:

```bash
git pull origin main --merge
```

(The `--merge` flag is explicit and avoids warnings in newer Git versions.)

Git will start a merge and pause because of conflicts. It will not finish the merge commit until you resolve them.

2. Cursor will highlight the conflicting lines in `collision.txt`. Git writes both versions into the file with conflict markers. If Alice pushed first (`Alice, cat`) and Bob then pulled, Bob's file looks like this:

```text
<<<<<<< HEAD
Bob, dog
=======
Alice, cat
>>>>>>> origin/main
```

- The block between `<<<<<<< HEAD` and `=======` is **your local version** (what you committed before the pull). Cursor labels this **Current Change**.
- The block between `=======` and `>>>>>>> origin/main` is the **remote version** (what you just pulled from GitHub). Cursor labels this **Incoming Change**.

3. **Resolve the conflict:** You want to **append** (not replace) the content so that everyone's entry is included. Delete the marker lines (`<<<<<<<`, `=======`, `>>>>>>>`); they are only annotations. The file should contain all group members' entries, one per line:

```text
Alice, cat
Bob, dog
Carol, bird
```

4. After resolving the conflict, stage the resolved file:

```bash
git add collision.txt
```

5. Complete the merge. Git will open a commit message in the editor (a default merge message is fine). Save and close that file to finish:

```bash
git commit
```

This creates the merge commit.

6. Push your changes:

```bash
git push origin main
```

7. The next person in the group should repeat steps 1-6 until everyone has successfully pushed their entry to the consolidated `collision.txt` file on GitHub.

**That's it.** All changes are now merged, and everyone has access to the merged results.
