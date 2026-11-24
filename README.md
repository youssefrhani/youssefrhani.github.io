# Youssef Rhani — Portfolio & Technical Blog

Personal portfolio and technical blog about Linux troubleshooting, Ansible
automation, and infrastructure projects. Built with AstroPaper and deployed to
GitHub Pages at <https://youssefrhani.github.io/>.

## Local development

```bash
pnpm install
pnpm dev
```

Create technical articles in `src/content/posts/` using the collection schema
and category values `linux`, `ansible`, or `projects`. Build the static site
with `pnpm build`.

The GitHub Pages deployment workflow runs on pushes to `main`. Enable GitHub
Pages in the repository settings and select **GitHub Actions** as the build
and deployment source.
