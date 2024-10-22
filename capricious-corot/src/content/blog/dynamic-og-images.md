---
author: Aditya Tomar
pubDatetime: 2024-10-18T05:17:19Z
title: Dynamic OG image generation in DivineInsights blog posts
slug: dynamic-og-image-generation-in-DivineInsights-blog-posts
featured: false
draft: false
tags:
  - docs
  - release
description: New feature in DivineInsights v1.4.0, introducing dynamic OG image generation for blog posts.
---

New feature in DivineInsights v1.4.0, introducing dynamic OG image generation for blog posts.

## Table of contents

## Intro

OG images (aka Social Images) play an important role in social media engagements. In case you don't know what OG image means, it is an image displayed whenever we share our website URL on social media such as Facebook, Discord etc.

> The Social Image used for Twitter is technically not called OG image. However, in this post, I'll be using the term OG image for all types of Social Images.

## Default/Static OG image (the old way)

DivineInsights already provided a way to add an OG image to a blog post. The author can specify the OG image in the frontmatter `ogImage`. Even when the author doesn't define the OG image in the frontmatter, the default OG image will be used as a fallback (in this case `public/DivineInsights-og.jpg`). But the problem is that the default OG image is static, which means every blog post that does not include an OG image in the frontmatter will always use the same default OG image despite each post title/content being different from others.

## Dynamic OG Image

Generating a dynamic OG image for each post allows the author to avoid specifying an OG image for every single blog post. Besides, this will prevent the fallback OG image from being identical to all blog posts.

In DivineInsights v1.4.0, Vercel's [Satori](https://github.com/vercel/satori) package is used for dynamic OG image generation.

Dynamic OG images will be generated at build time for blog posts that

- don't include OG image in the frontmatter
- are not marked as draft.

## Anatomy of DivineInsights dynamic OG image

Dynamic OG image of DivineInsights includes _the blog post title_, _author name_ and _site title_. Author name and site title will be retrieved via `SITE.author` and `SITE.title` of **"src/config.ts"** file. The title is generated from the blog post frontmatter `title`.

### Issue Non-Latin Characters

Titles with non-latin characters won't display properly out of the box. To resolve this, we have to replace `fontsConfig` inside `loadGoogleFont.ts` with your preferred font.

```ts
// file: loadGoogleFont.ts

async function loadGoogleFonts(
  text: string
): Promise<
  Array<{ name: string; data: ArrayBuffer; weight: number; style: string }>
> {
  const fontsConfig = [
    {
      name: "Noto Sans JP",
      font: "Noto+Sans+JP",
      weight: 400,
      style: "normal",
    },
    {
      name: "Noto Sans JP",
      font: "Noto+Sans+JP:wght@700",
      weight: 700,
      style: "normal",
    },
    { name: "Noto Sans", font: "Noto+Sans", weight: 400, style: "normal" },
    {
      name: "Noto Sans",
      font: "Noto+Sans:wght@700",
      weight: 700,
      style: "normal",
    },
  ];
  // other codes
}
```

> Check out [this PR](https://github.com/ascendantaditya/divineinsights/pull/318) for more info.

## Limitations

At the time of writing this, [Satori](https://github.com/vercel/satori) is fairly new and has not reached major release yet. So, there are still some limitations to this dynamic OG image feature.

- Besides, RTL languages are not supported yet.
- [Using emoji](https://github.com/vercel/satori#emojis) in the title might be a little bit tricky.
