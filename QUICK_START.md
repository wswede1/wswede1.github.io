# Quick Start Guide - Get Your Portfolio Live in 30 Minutes

Follow these steps to get your basic site online TODAY. You'll fill in the content over the next few weeks.

## Step 1: Personalize Configuration (5 minutes)

Open `_config.yml` and replace these placeholders:

```yaml
# Find and replace:
"Your Name" → Your actual name
your.email@example.com → Your actual email
yourusername → Your GitHub username
yourprofile → Your LinkedIn profile name
```

**Save the file.**

## Step 2: Create GitHub Repository (5 minutes)

1. Go to https://github.com (create account if needed)
2. Click "+" in top right → "New repository"
3. Name it: `yourusername.github.io` (use YOUR GitHub username)
4. Make it **Public**
5. Click "Create repository"
6. **Copy the repository URL** (should be like: `https://github.com/yourusername/yourusername.github.io.git`)

## Step 3: Push Your Files (5 minutes)

Open Terminal and run these commands:

```bash
# Navigate to your portfolio directory
cd /Users/wadeswede/Desktop/portfolio

# Initialize git (if not already done)
git init

# Add all files
git add .

# Commit
git commit -m "Initial portfolio setup"

# Add your remote (REPLACE with your actual URL from Step 2)
git remote add origin https://github.com/yourusername/yourusername.github.io.git

# Push to GitHub
git branch -M main
git push -u origin main
```

**If you get an error about authentication:**
- GitHub may require a Personal Access Token instead of password
- Go to GitHub Settings → Developer settings → Personal access tokens
- Generate new token with 'repo' permissions
- Use this token instead of your password

## Step 4: Enable GitHub Pages (3 minutes)

1. Go to your repository on GitHub
2. Click "Settings" tab
3. Click "Pages" in left sidebar
4. Under "Source", select:
   - Branch: **main**
   - Folder: **/ (root)**
5. Click "Save"
6. Wait 1-2 minutes

## Step 5: View Your Live Site! (2 minutes)

1. Go to: `https://yourusername.github.io` (replace with YOUR username)
2. It may take a minute or two to build the first time
3. Refresh if you see a 404 error

**Congratulations!** Your portfolio is now live. 🎉

---

## What You'll See

Right now, your site has:
- ✓ Basic structure and navigation
- ✓ Template pages with placeholder content
- ✓ Professional layout
- ⚠️ Placeholder text that says "Your Name" and "[Write content here]"

## Next Steps (Over the Next 3 Weeks)

### This Week
1. Fill in your professional introduction (300-1000 words)
2. Update your CV page
3. Create a resume PDF and add it to `assets/cv/resume.pdf`

### Week 2
4. Write your internship project description (1000-3000 words)
5. Add code snippets to the internship page
6. Clean up your LING 508 repository

### Week 3
7. Write your LING 508 project description (1000-3000 words)
8. Add code snippets to the LING 508 page
9. Proofread everything
10. Test all links and functionality

---

## Making Updates

Every time you want to update your site:

```bash
# 1. Edit your files
# 2. Save them
# 3. Run these commands:

cd /Users/wadeswede/Desktop/portfolio
git add .
git commit -m "Description of what you changed"
git push
```

Your site will automatically update in 1-2 minutes.

---

## Common Issues

**"Site not loading"**
- Wait 2-3 minutes after first push
- Check Settings → Pages to see build status
- Look for errors in the build log

**"Looks plain/no styling"**
- Make sure `remote_theme: mmistakes/minimal-mistakes` is in `_config.yml`
- Wait a few minutes for GitHub to apply the theme
- Clear your browser cache

**"Can't push to GitHub"**
- Make sure you created the repository as Public
- Check that you're using the correct repository URL
- May need a Personal Access Token for authentication

**"404 error"**
- Make sure repository is named `yourusername.github.io` exactly
- Check that GitHub Pages is enabled in Settings
- Wait a few minutes for initial deployment

---

## Files to Edit (in order of priority)

### Week 1 Priority
1. `_config.yml` - Your name, email, links
2. `index.md` - Home page with introduction
3. `_pages/about.md` - Detailed about page
4. `_pages/cv.md` - Resume/CV content
5. Add `assets/cv/resume.pdf` - PDF resume

### Week 2 Priority
6. `_pages/internship.md` - Complete internship description
7. Clean up LING 508 repo

### Week 3 Priority
8. `_pages/ling508.md` - Complete LING 508 description
9. Final proofreading and polish

---

## Need More Detail?

- **Full setup guide**: See `GETTING_STARTED.md`
- **Content writing help**: See `CONTENT_TEMPLATE.md`
- **Rubric checking**: See `RUBRIC_CHECKLIST.md`

---

## You're Ready!

You now have:
- ✅ A live portfolio website
- ✅ Professional structure and layout
- ✅ Clear templates for all required content
- ✅ A plan for the next 3 weeks

**Next action:** Start writing your professional introduction (300-1000 words). Use the template in `CONTENT_TEMPLATE.md` to guide you.

**Remember:** This portfolio will serve you beyond graduation. Take your time, be thorough, and showcase your skills with pride.

Good luck! 🚀

