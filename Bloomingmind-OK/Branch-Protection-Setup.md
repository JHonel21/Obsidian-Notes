# Branch Protection Setup — Blooming Mind Repo

**Purpose:** Prevent accidental direct pushes to `main` (production). Forces changes through a pull request first, giving a review checkpoint before anything hits the live site.

**Repo:** `JHonel21/bloomingmind-ok` **Branches:** `main` (production) → `preview` (staging, deploys to Netlify branch deploy)

---

## Step 1: Open branch protection settings

1. Go to the repo on GitHub: `https://github.com/JHonel21/bloomingmind-ok`
2. Click **Settings** (top nav, requires admin/owner access on the repo)
3. In the left sidebar, click **Branches**

## Step 2: Add a protection rule

1. Under **Branch protection rules**, click **Add branch ruleset** (or **Add rule**, depending on GitHub's current UI)
2. In **Branch name pattern**, enter:
    
    ```
    main
    ```
    
3. This targets `main` specifically. Do not use a wildcard here, `preview` should stay unprotected so you can push directly to it for testing.

## Step 3: Configure the core protections

Enable these settings:

- [x] **Require a pull request before merging**
    - [x] Require approvals: set to `1` (even as a solo dev, this stops a direct force-push and makes you consciously approve your own PR before merge)
    - Optional: **Dismiss stale pull request approvals when new commits are pushed**, useful if you tend to push follow-up fixes to an open PR
- [x] **Require status checks to pass before merging** (optional for now, revisit if you add CI/lint checks later)
- [x] **Do not allow bypassing the above settings**
    - Uncheck this if you want an "admin override" escape hatch for emergencies. Leaving it checked means even you, as repo owner, must go through the PR process.
- [ ] **Restrict who can push to matching branches** (skip for now, not needed for a solo/small team repo)

## Step 4: Save

1. Click **Create** (or **Save changes**)
2. Confirm the rule appears in the Branches settings list, showing `main` as protected

---

## New workflow after this change

Direct pushes to `main` will now be **rejected**. The flow becomes:

```powershell
# Work happens on preview (or a feature branch off preview)
git checkout preview
git pull origin preview

# ... make changes, commit ...
git add .
git commit -m "Update team page content"
git push origin preview
```

Then in GitHub:

1. Open a **Pull Request**: base = `main`, compare = `preview`
2. Review the diff (this is your last check before prod)
3. Click **Merge pull request**
4. Netlify automatically builds and deploys `main` to production

---

## Notes

- This does **not** affect the `preview` branch. Pushes straight to `preview` still work and will still trigger a Netlify branch deploy for testing.
- If a PR merge ever gets blocked unexpectedly, check whether **Require approvals** is set higher than the number of collaborators who can approve. As a solo dev, keep this at `1`.
- Revisit **Require status checks** if a CI pipeline (linting, build validation) gets added to the repo later.