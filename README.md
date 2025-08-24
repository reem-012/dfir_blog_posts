# Blog Content Repository

This repository contains blog posts that can be used across multiple sites via git submodules.

## Structure

- `posts/` - Markdown blog posts with frontmatter
- Each post should have frontmatter with at least: title, date, author

## Usage as Submodule

```bash
git submodule add <this-repo-url> blog
git submodule update --init --recursive
```

## Adding New Posts

1. Create a new `.md` file in the `posts/` directory
2. Add frontmatter at the top of the file
3. Commit and push changes
4. Update submodule in consuming repositories