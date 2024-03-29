---
title: "Triumph of the Blogman"
date: 2024-03-02
tags: [11ty, font-size, css, github-actions, github-pages]
---
I spent my free time this week converting my github pages site from jekyll to [11ty](https://11ty.dev). I'm fairly new to the whole static-site-generator scene. My blog only had 2 or 3 posts. The main problem I had with jekyll is the build times. My site, with only a few posts and a fairly simple theme took around **5 minutes** to build. Could it be any slower? It is also written in *perl* 🤢. (There is *probably* nothing wrong with the perl language, but it is *sooo* 1990, and I know **nothing** about it.) For the same content, *11ty* builds only take about ***25 seconds***. Less than a minute after doing a `git push`, I can see the update on my site! 11ty is written in JavaScript too, so it's a bit more familiar for me.

## Issues
Throughout this process I ran into a few issues. The first problem was that I didn't know how to stop using jekyll. Github pages default to building with jekyll, and from searching and reading I had gathered that it was possible. However, the forums and tutorials that I found were not very clear about the process. Well—disabling jekyll is pretty easy. Just add an empty file called `.nojekyll` to the repository. What I had a hard time with was *replacing* jekyll with something else. I ended up finding [this tutorial](https://www.linkedin.com/pulse/eleventy-github-pages-lea-tortay/) from 4 years ago that worked.

### GitHub actions
1. First create a `.nojekyll` file.
2. Create a `.github/workflows/` folder and run `git add .github`
3. Create a custom github action, like the following:

```yaml
# .github/workflows/build.yml
name: Build Eleventy

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-22.04

    strategy:
      matrix:
        node-version: [16.x]

    steps:
      - uses: actions/checkout@v2

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install dependencies & build
        run: |
          npm ci
          npm run build

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          publish_dir: ./_site #this should match the output folder from eleventy.config.js
          github_token: ${{ secrets.GITHUB_TOKEN }}
```

4. In the repo settings, **change the pages Build Source** to the `gh-pages` branch. (This took me several hours to figure out.) The custom action runs everytime the main branch is updated. The custom action updates the gh-pages branch with the built output of the site. Failing to change the GitHub Pages Build Source will result in a 404 error and tell you that there is no index.html file when you attempt to navigate to your site.

### Look and Feel
I decided to use [eleventy-base-blog](https://github.com/11ty/eleventy-base-blog) as a starting point for my theme. As an 11ty beginner, it offers a good structure and includes some default plugins already activated. (I think after the last few days of playing around, I am a bit more comfortable with the eleventy.config.js file.) Now, me being me, I can't just leave the default options in place.

#### Theme Colors
Thankfully eleventy-base-blog includes support for automatic light and dark modes. The light theme is pretty much perfect, but the dark theme had some weird link colors. Light-purplish on a dark blue background 😠, really?! I changed it to shade of Vivid Blue. Found in `public/css/index.css`.

```css
@media (prefers-color-scheme: dark) {
	:root {
		--color-gray-20: #e0e0e0;
		--color-gray-50: #C0C0C0;
		--color-gray-90: #dad8d8;

		/* --text-color is assigned to --color-gray-_ above */
		--text-color-link: #1493fb;
		--text-color-link-active: #2d9ffb;
		--text-color-link-visited: #5fb6fc;

		--background-color: #15202b;
	}
}
```

#### Font Family

The default settings are set up with a very basic *sans* font family. It looks small compared to the text in the code blocks as well. I found a great font family on Google Fonts called [Amiri](https://fonts.google.com/specimen/Amiri?query=Amiri). I made some more changes in the css. 

```css
:root{
  --font-family: "Amiri", serif;
}

body{
	font-family: var(--font-family);
	font-size: 1.12em;
}
```

The font used in the code blocks seemed too large for my taste. This turned into a learning moment. I thought I could simply change the `font-size` for the `pre` blocks to something like `.9em`. Turns out that didn't change anything.  Apparently the way css inherits variable font sizes inside child elements means that `em` doesn't  reference anything. I found that inside of the `pre` block you must use `rem` instead. `rem` references the root of the document. 

```css
:not(pre) > code[class*="language-"],
pre[class*="language-"] {
  font-size: .9rem;
}
```

**Much better!**

Time for a nightcap. Goodnight! 🥱