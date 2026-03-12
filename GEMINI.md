# Gemini Context: The Magic of Programming (Technical Blog)

## Project Overview
This is a Jekyll-based technical blog (GitHub Pages) owned by Wenliang ZHANG (张文亮). It focuses on computer science, database systems, and software engineering.

## Key Directories
- `_posts/`: Markdown files for blog posts.
- `_layouts/`: Liquid templates for site structure (`default`, `page`, `post`, `tagpage`).
- `_includes/`: Reusable HTML fragments (analytics, comments, tags, etc.).
- `tag/`: Automatically generated tag pages.
- `images/`: Static images used in posts.
- `files/`: PDF documents and other downloadable assets.

## Post Creation Guidelines
- **File Naming:** `_posts/YYYY-MM-DD-TITLE.md` (e.g., `2026-03-12-HELLO-WORLD.md`).
- **Front Matter:** Must include `layout: post`, `title`, and `tags`.
  - Example:
    ```yaml
    ---
    layout: post
    title: "My New Post"
    tags: [database, sql]
    ---
    ```
- **Tag Generation:** After adding or updating a post with new tags, run `./tag_generator.py` to update the `tag/` directory.

## Technical Standards
- **Markdown:** Kramdown (GitHub Flavored Markdown).
- **Syntax Highlighting:** Rouge (configured in `_config.yml`).
- **Styling:** Sass (partials in `_sass/`, main entry in `style.scss`).
- **Icons:** SVG icons managed in `_includes/svg-icons.html` and `_sass/_svg-icons.scss`.

## Workflows
- **Adding a Post:**
  1. Create a new `.md` file in `_posts/` following the naming convention.
  2. Add required front matter and content.
  3. Run `./tag_generator.py` to refresh the tag pages.
  4. (Optional) Preview locally using `bundle exec jekyll serve` or `jekyll serve`.
  5. Commit and push to GitHub.
