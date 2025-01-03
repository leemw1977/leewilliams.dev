+++
authors = ["Lee Williams"]
title = "Building My Hugo Site: From Local to Live with GitHub Actions 🚀"
date = 2025-01-03T14:00:00Z
draft = false
description = "Learn how I set up my Hugo site, fixed deployment issues, and automated it all with GitHub Actions. A guide packed with tips, code snippets, and lessons learned."
tags = ["Hugo", "GitHub Actions", "Static Site", "Web Development"]
+++
Hey there! 👋 Today, I want to share the journey of setting up my Hugo site, tackling some tricky challenges, and automating deployment with GitHub Actions. If you’ve ever dived into static site generation or struggled with deployment woes, you’ll find some valuable nuggets here. Let’s dive in! 🌊

---

## Why Hugo? 🤔

Hugo is **blazing fast**, super flexible, and perfect for creating static sites. Plus, its support for themes, powerful configuration options, and ease of use won me over quickly. Whether you’re a developer looking for control or just someone who loves tinkering, Hugo has something for everyone.

---

## The Hiccups Along the Way 😅

Like any good project, things didn’t go perfectly at first. 

None of these though are the fault of the wonderful template [Coder](https://github.com/luizdepra/hugo-coder/) or [Hugo](https://gohugo.io/) in any way - **I want to make that very clear**.

Here’s what I faced:

### 1. **Themes as Submodules**
I used a Git submodule to manage my theme (great for updates! 🛠️), but it didn’t fetch properly during the build process. Cue broken layouts and missing templates. 😭


### 2. **URLs That Didn’t Play Nice**
Locally, the site worked like a dream, but on deployment, paths to assets like CSS and JavaScript went rogue. Instead of `/css/style.css`, I’d get `./css/style.css`—not ideal.

### 3. **Automation Mysteries**
Setting up GitHub Actions for automatic deployment wasn’t as straightforward as I hoped. Missing files, old content hanging around, and general “why isn’t this working?!” moments kept me busy.

---

## How I Fixed Everything 🛠️

Here’s how I tackled each problem and got my site live, smooth, and looking great.

### **1. Setting Up Hugo Properly**
The `config.toml` file is the backbone of your Hugo site. Here’s a snippet of the final version I ended up with:

```
baseURL = "https://leewilliams.dev/"
canonifyURLs = true
relativeURLs = false
```

- **`baseURL`**: Defines the root URL for the site.
- **`canonifyURLs`**: Converts all paths to absolute URLs for consistency.
- **`relativeURLs`**: Disabled to prevent doubling paths like `/posts/posts/`.

### **2. Automating with GitHub Actions**
Automation saves time ⏳ and ensures everything is consistent. After a bit of trial and error, here’s the GitHub Actions workflow I landed on:

```yaml
name: Build and Deploy Hugo Site

on:
  push:
    branches:
      - main

jobs:
  build-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2
        with:
          submodules: recursive

      - name: Install Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'

      - name: Build Hugo site
        run: hugo --cleanDestinationDir --minify

      - name: Deploy via FTP
        uses: SamKirkland/FTP-Deploy-Action@4.3.0
        with:
          server: your-ftp-server.com
          username: ${{ secrets.FTP_USERNAME }}
          password: ${{ secrets.FTP_PASSWORD }}
          local-dir: public
```

💡 **Pro Tip**: Always initialise submodules during the checkout step, or you’ll end up scratching your head over missing theme files.

### **3. Debugging Links and Assets**
To find the issue with the rogue paths, I used my browsers developer tools, to identify what was going on.

Eventually I generated the site myself locally, cleared my hosting folder, and uploaded manually and everything started working fine.

So I knew then it was definitely a problem in the automation rather than with the technical set up.

The key was this part:

```yaml
        with:
          submodules: recursive
```

Before this I had a separate job step that was using `git` directly to get the submodule, I haven't had time to investigate it, but my thinking is there was something wrong with that `git` command.
---

## Lessons Learned ✨

1. **Plan Your Config**: Your `config.toml` is the foundation of your site. Get it right early, and things will flow smoothly.
2. **Automate Everything**: Once you’ve got a reliable GitHub Actions workflow, you’ll wonder how you ever managed without it.
3. **Debug Locally**: Always test builds locally with:
   ```bash
   hugo --cleanDestinationDir --minify
   ```

---

## What’s Next? 🔮

Now that my site’s live, here are a few things I’m planning to explore:

- **SEO Tweaks**: Adding metadata, alt tags, and a sitemap.
- **Experimenting with Features**: Custom shortcodes, taxonomies, and more.
- **Content Creation**: Sharing tutorials, thoughts, and updates.

---

## Final Thoughts 💭

Setting up a Hugo site was an adventure - which I'm really glad I undertook. There were a few hiccups due to me not having used the tech before, but each one taught me something new.

If you’re diving into Hugo or static site generation, don’t be afraid to experiment, break things, and debug. That’s half the fun! 🎉

Have any questions or tips? Drop them in the comments—I’d love to hear from you! 😊
