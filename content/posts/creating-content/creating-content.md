---
title: "Creating content"
path: "/creating-content"
tags: ["Theme"]
featuredImage: "./cover.jpg"
excerpt: How to create pages, posts and manage tags.
created: 2019-07-21
updated: 2019-07-21
---

Content in Nehalem is served by the [gatsby-transformer-remark](https://www.gatsbyjs.org/packages/gatsby-transformer-remark/) plugin. But to 
create content it's not necessary to deal with GraphQL queries and instead create simple markdown files.

## The content directory

When starting Nehalem the first time it will create a `content` directory - and this is where the fun happens. If a markdown file is 
interpreted as post or static file depends on it's directory:

- put posts under `content/posts`
- put pages under `content/pages`

## Creating posts

Posts do have a slightly longer frontmatter than pages:

```markdown

---
title: "Post title"
path: "/path-to-the-post"
tags: ["A", "list", "of", "tags"]
featuredImage: "./cover.jpg"
excerpt: An excerpt what to expect from this post.
created: 2019-07-29
updated: 2019-07-29
---

```

## Creating pages

The pages frontmatter is slightly shorter:

```markdown

---
title: Page title
path: "/path-to-the-path"
excerpt: An excerpt what to expect from this page.
---

The `excerpt` is used for SEO!

```

## Tags

Tags are handled slightly different than posts and pages; they're stored in a `tags.yml` which must be located in the content directory.

> It's highly recommended to register all tags used within posts in this file!

The `tags.yml` file:

```yaml
- name: Tag1
  color: "#ff0000"
  icon: ./assets/images/tag1icon.svg
  featured: true
- name: Tag2
  color: #00ffee
  icon: ./assets/images/tag2icon.svg
  featured: false
```

`color` is used to change the appearance for the subheader on the tag archive for this specific tag. If `featured` is set to `true` the 
tag will be shown on the front page.

---

*Cover by [@anniespratt](https://unsplash.com/@anniespratt)*
