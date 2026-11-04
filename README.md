# wswede1.github.io

Source for [wswede1.github.io](https://wswede1.github.io/), the personal website of Wade Swede: AI and NLP projects, educational technology, resources for teachers, and writing.

Built with [Jekyll](https://jekyllrb.com/) and the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) theme, and hosted on GitHub Pages. Pushing to `main` publishes the site.

## Site structure

```
_config.yml            Site settings, collections, and defaults
_data/
  navigation.yml       Top menu: Home, Projects, Resources, Writing, About, CV
  taxonomy.yml         Project areas and resource types (sections on each page)
_pages/                Standalone pages: home, projects, resources, writing, about, CV
_projects/             One Markdown file per project case study  -> /projects/<name>/
_resources/            One Markdown file per teacher resource, agent skill, or workshop -> /resources/<name>/
_posts/                Writing, named YYYY-MM-DD-title.md         -> /writing/<title>/
_templates/            Starter files to copy (not published)
_layouts/, _includes/  The project and resource page layouts and summary boxes
assets/
  images/              Images (use images/projects/ for project screenshots)
  downloads/           Files for teacher resources
  skills/              Agent skill folders and .zip downloads
  cv/                  Resume PDF
```

## Adding content

| To add a... | Copy this template | To |
|---|---|---|
| Project case study | `_templates/project.md` | `_projects/<short-name>.md` |
| Teacher resource or workshop | `_templates/resource.md` | `_resources/<short-name>.md` |
| AI agent skill | `_templates/agent-skill.md` | `_resources/<skill-name>.md` |
| Article | (any Markdown file) | `_posts/YYYY-MM-DD-title.md` |

Fill in the front matter at the top of the file. It drives the summary box, the cards on the Projects and Resources pages, and the homepage. Set `featured: true` on a project to show it on the homepage. Sections and categories are defined in `_data/taxonomy.yml`.

## Previewing locally

```bash
bundle install
bundle exec jekyll serve
```

Then open http://localhost:4000. Work on a branch and merge to `main` when ready to publish.
