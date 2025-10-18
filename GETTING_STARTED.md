# Getting Started with Your HLT Portfolio

This guide will help you set up and populate your portfolio website step by step.

## Step 1: Set Up GitHub Repository (TODAY)

### Create Your Repository
1. Go to [GitHub](https://github.com) and log in
2. Click the "+" icon in the top right and select "New repository"
3. Name it exactly: `yourusername.github.io` (replace `yourusername` with your actual GitHub username)
4. Make it **Public**
5. Do NOT initialize with README (we already have one)
6. Click "Create repository"

### Push Your Local Files to GitHub
```bash
cd /Users/wadeswede/Desktop/portfolio

# Initialize git if not already done
git init

# Add all files
git add .

# Commit
git commit -m "Initial portfolio site setup"

# Add your GitHub repository as remote (replace with your actual URL)
git remote add origin https://github.com/yourusername/yourusername.github.io.git

# Push to GitHub
git branch -M main
git push -u origin main
```

### Enable GitHub Pages
1. Go to your repository on GitHub
2. Click "Settings" tab
3. Scroll down to "Pages" in the left sidebar
4. Under "Source", select "Deploy from a branch"
5. Select branch "main" and folder "/ (root)"
6. Click "Save"
7. Wait 1-2 minutes, then visit `https://yourusername.github.io`

## Step 2: Set Up Local Development (Optional but Recommended)

This allows you to preview your site locally before pushing to GitHub.

### Install Ruby and Jekyll (macOS)
```bash
# Install Homebrew if you don't have it
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Ruby
brew install ruby

# Add Ruby to your PATH (add this to your ~/.zshrc)
echo 'export PATH="/opt/homebrew/opt/ruby/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Install Jekyll and Bundler
gem install jekyll bundler

# In your portfolio directory, install dependencies
cd /Users/wadeswede/Desktop/portfolio
bundle install
```

### Run Site Locally
```bash
# Start the Jekyll server
bundle exec jekyll serve

# Open http://localhost:4000 in your browser
```

Any changes you make will automatically update (you may need to refresh your browser).

## Step 3: Customize Your Site Configuration

### Edit `_config.yml`
Replace the placeholder information:

```yaml
title: "Your Actual Name - HLT Portfolio"
name: "Your Full Name"
email: your.actual.email@email.com
url: "https://yourusername.github.io"
repository: "yourusername/yourusername.github.io"

author:
  name: "Your Full Name"
  bio: "MS in Human Language Technology student specializing in [your focus areas]"
  location: "City, State" or "Remote"
  email: your.actual.email@email.com
  links:
    - label: "GitHub"
      icon: "fab fa-fw fa-github"
      url: "https://github.com/yourusername"
    - label: "LinkedIn"
      icon: "fab fa-fw fa-linkedin"
      url: "https://linkedin.com/in/yourprofile"
```

### Choose a Theme Skin
In `_config.yml`, change the `minimal_mistakes_skin` option:
- "default" - classic look
- "air" - clean and minimal
- "contrast" - high contrast dark
- "dark" - dark theme
- "mint" - fresh green
- "plum" - professional purple

## Step 4: Add Your Professional Photo (Optional)

1. Add a professional headshot to `/Users/wadeswede/Desktop/portfolio/assets/images/profile.jpg`
2. Recommended size: 400x400 pixels, square crop
3. If you don't have one, you can remove the `avatar:` line from `_config.yml`

## Step 5: Fill In Your Content (Week 1-2)

### Priority 1: Professional Introduction
Edit `index.md` and `_pages/about.md`

**What to include (300-1000 words):**
- Your educational background and path to HLT
- Technical skills (be specific: Python, spaCy, transformers, etc.)
- Professional experience summary
- Career goals and interests
- What excites you about NLP/HLT

**Checklist:**
- [ ] Written between 300-1000 words
- [ ] Focuses on professional background and skills
- [ ] Includes specific technical skills
- [ ] Free of grammatical errors
- [ ] Professional tone

### Priority 2: Update Your CV/Resume
Edit `_pages/cv.md`

**What to include:**
- Education with MS HLT coursework
- Internship experience with accomplishments
- Technical skills section (detailed)
- Academic projects
- Relevant coursework list
- Any publications or presentations

**Also create a PDF version:**
1. Format your resume in Word/Google Docs/LaTeX
2. Export as PDF
3. Save to `/Users/wadeswede/Desktop/portfolio/assets/cv/resume.pdf`

**Checklist:**
- [ ] Up to date with all HLT program content
- [ ] Includes internship and projects
- [ ] Detailed technical skills section
- [ ] PDF version created and placed in assets/cv/
- [ ] No grammatical or formatting errors

### Priority 3: Document Your Internship
Edit `_pages/internship.md`

**Required content (1000-3000 words, excluding code):**

Answer these questions:
- What did you do in this project?
- What were the goals and how did you plan to achieve them?
- What problems did you encounter and how did you solve them?
- What libraries or tools did you use?
- What did you apply from courses vs. what you learned new?
- What was the state at completion? Future plans?

**Must demonstrate all 4 learning outcomes:**
1. **Code development** - Show you can write, debug, document code
2. **Algorithm selection** - Show you applied appropriate HLT concepts
3. **Tool integration** - Show you used NLP libraries effectively  
4. **Professional skills** - Show teamwork, communication, critical thinking

**Code requirements:**
- Include 3-5 code snippets (don't count toward word count)
- Each snippet should be well-commented and explained
- OR link to a GitHub repository with your code
- If code can't be shared, provide documentation or pseudocode

**Checklist:**
- [ ] 1000-3000 words written (check word count)
- [ ] All required questions answered
- [ ] All 4 learning outcomes explicitly demonstrated
- [ ] 3-5 code snippets included OR repository linked
- [ ] Code is well-commented and explained
- [ ] No grammatical errors
- [ ] Professional tone maintained

### Priority 4: Document Your LING 508 Project
Edit `_pages/ling508.md`

**Same requirements as internship:**
- 1000-3000 words
- Answer all required questions
- Demonstrate all 4 learning outcomes
- Include code snippets or repository link

**Additional step: Clean up your LING 508 repository**

Before linking to it:
1. Create a backup: `cp -r ling508-project ling508-project-backup`
2. Remove experimental/failed code
3. Organize into clear directories
4. Add comprehensive README
5. Comment your code
6. Add toy data if needed
7. Push to GitHub

**Checklist:**
- [ ] Repository cleaned and organized
- [ ] Comprehensive README added to repo
- [ ] 1000-3000 words written
- [ ] All questions answered
- [ ] All 4 learning outcomes demonstrated
- [ ] Code snippets OR repository linked
- [ ] No grammatical errors

## Step 6: Test and Refine (Week 3)

### Content Checklist
- [ ] Professional introduction: 300-1000 words ✓
- [ ] CV/Resume: Up to date ✓
- [ ] Internship project: 1000-3000 words ✓
- [ ] LING 508 project: 1000-3000 words ✓
- [ ] All 4 learning outcomes demonstrated in EACH project
- [ ] Code present for both projects
- [ ] No more than 1-2 minor grammatical issues per section

### Technical Checklist
- [ ] Site loads at yourusername.github.io
- [ ] All internal links work
- [ ] All external links work (GitHub, LinkedIn, etc.)
- [ ] CV PDF downloads correctly
- [ ] Site looks good on mobile
- [ ] Code snippets have syntax highlighting
- [ ] Navigation menu works

### Quality Checklist
- [ ] Proofread everything with Grammarly or similar
- [ ] Word counts verified for each section
- [ ] All placeholder text replaced with your content
- [ ] Professional photo added (optional)
- [ ] Asked a peer or mentor to review
- [ ] Self-assessed against rubric (aim for 30/30)

## Step 7: Ongoing Maintenance

### After Initial Launch
- Keep your CV updated
- Add new projects as you complete them
- Consider adding blog posts about NLP topics
- Update your professional introduction as you progress

### Making Updates
```bash
# Make your changes to the files
# Then commit and push
git add .
git commit -m "Description of changes"
git push
```

GitHub Pages will automatically rebuild your site in 1-2 minutes.

## Tips for Success

### Writing Tips
- **Be specific**: Don't just say "used Python" - say "used Python with spaCy 3.4 and transformers 4.25"
- **Show impact**: Include metrics and outcomes where possible
- **Tell a story**: Describe challenges and how you solved them
- **Be professional**: Write for hiring managers, not just academics

### Code Tips
- **Choose meaningful snippets**: Pick code that demonstrates problem-solving
- **Add context**: Explain WHY you wrote the code this way
- **Comment well**: Make it easy for readers to understand
- **Link to repos**: Give readers access to full codebases when possible

### Time Management
- **Week 1**: Setup + content gathering (20 hours)
- **Week 2**: Writing project descriptions (25 hours)
- **Week 3**: Integration + polish (15 hours)
- **Total**: ~60 hours of focused work

### Getting Unstuck

**If Jekyll won't build:**
- Check `_config.yml` for syntax errors
- Make sure front matter (---) is correct in all .md files
- Check the build log on GitHub: Settings → Pages

**If styling looks wrong:**
- Clear your browser cache
- Check that `remote_theme` is set correctly in `_config.yml`
- Wait a few minutes for GitHub Pages to rebuild

**If you're stuck on content:**
- Look at the example portfolios in the rubric
- Review your actual project files and notes
- Talk through your work with a friend (helps clarify)
- Start with bullet points, then expand to prose

## Need Help?

- **Jekyll Documentation**: https://jekyllrb.com/docs/
- **Minimal Mistakes Guide**: https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/
- **GitHub Pages Docs**: https://docs.github.com/en/pages
- **Markdown Guide**: https://www.markdownguide.org/

## Resources

### Word Count Tools
- Most text editors show word count
- Online: https://wordcounter.net/
- Command line: `wc -w filename.md`

### Proofreading
- Grammarly (free version works)
- Hemingway Editor (readability)
- Ask a peer to review

### Example Portfolios (from your rubric)
- https://kelynnski.github.io/
- https://pbarrett520.github.io/
- https://maelfabien.github.io/portfolio/

---

**You've got this!** This portfolio will serve you well beyond graduation. Take your time, be thorough, and showcase your skills with confidence.

