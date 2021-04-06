---
title: Responsive Images in Eleventy
description: Quick overview of responsive images and how to configure them in Eleventy
date: 2021-04-05
readingTime: 5 minutes
tags: ["eleventy", "responsive", "images"]
---

{% image "./blog/2021/04/05/mathIsHard.jpeg", "math is hard", "(max-width: 640px) 100%, 100%", page.url %}

The above image is a live-action shot of me setting up responsive images in [Eleventy](https://www.11ty.dev/). Okay, it wasn't that hard, but I did have a couple of hiccups along the way. The [documentation](https://www.11ty.dev/docs/plugins/image/) is solid and should get you most of the way there. If you're stuck, hopefully some of the things I worked through below will help you out.

I hate to admit it but my biggest issue was ignorance of responsive images in general. This ended up being a great learning opportunity for me. This post will be a brief overview of what I've learned about responsive images and how to configure Eleventy to generate them using the default [eleventy-img](https://github.com/11ty/eleventy-img) plugin.

## Responsive Images

I've linked to a bunch of great resources at the bottom of this post that explains responsive images way better than I can. Below is a high-level summary of the situation.

As with all "responsive" web design, we need to build websites that look great on whatever device somebody may be accessing it on. A myriad of screen sizes, network connection situations, etc. need to be taken into account. The idea of responsive images is we create multiple versions of an image in different sizes and depending on the screen size of the user load the image that will best fit their screen.

The `<img>` tag has grown up a bit to allow this. Instead of just the `src` attribute that specifies a path to a single image we have the `srcset` and `sizes` attributes. The `srcset` attribute lets us provide multiple versions of an image and the `sizes` attribute lets us provide information to the browser to help it choose the best image to load for a particular user's viewport. It's always best to still provide an image in the `src` attribute as a fallback.

Here is an example using the image at the top of this page.

```html
<img
  alt="math is hard"
  loading="lazy"
  decoding="async"
  src="mathIsHard-640w.jpeg"
  width="768"
  height="325"
  srcset="mathIsHard-640w.jpeg 640w, mathIsHard-768w.jpeg 768w"
  sizes="(max-width: 640px) 100%, 100%"
/>
```

There is also now a `<picture>` element that contains `<source>` elements which can specify multiple image sources and also supports media queries to allow even more flexibility. This is probably overkill for most use cases and the srcset and sizes attributes on `<img>` should do just fine.

There are many packages available to automatically generate resized images based on your original image. You do not need to sit around manually resizing images all day. These packages are normally run as part of a build process so when you build your site all of the images get automatically generated. I'll be using the [eleventy-img](https://github.com/11ty/eleventy-img) plugin to do it for me here.

## Eleventy Configuration

You can copy the code from the [Eleventy Documentation](https://www.11ty.dev/docs/plugins/image/) like I did and you'll be 95% done. I just made a couple of tweaks to suit my needs.

- I want the generated responsive images to write directly to my \_site output directory and do not want them to also write to my input directory.
- I want to leave the original images in the input directory and do not want those copied to the output directory.
- I want images associated with a blog post to live in the same directory as that blog post file.

For now, my main concern is images in blog posts. You can see my folder structure for blog posts reflected in the URL of this page (/blog/year/month/day/postName). Eleventy will generate a directory with the same name as your input file (postName) at build time and the actual page will be a index.html file within that directory. I want my responsive images to also live in that same directory. To accomplish this I passed the [page.url](https://www.11ty.dev/docs/data-eleventy-supplied/) as an argument to my `imageShortcode` function via the template tag.

```js
{% raw %}
{% image "./blog/2021/04/05/mathIsHard.jpeg", "math is hard", "(min-width: 30em) 100vw, 100vw", page.url %}
{% endraw %}
```

I then used a simple ternary statement to set `imgPath` to the pageURL parameter if supplied or just the default "img" string if not. In my `outputDir` property I prepend "\_site/" to my `imgPath` so it writes directly to my generated \_site output directory. The `urlPath` property is just the current directory of the image.

The `widths` and `formats` to generate are completely up to you. I just want a couple of small jpegs right now for experimenting on this blog. I've linked to a good article at the bottom of this post which talks about how you can go about determining which sizes you may want. A large image on a production site would have many more widths to fit various screen resolutions (ie. 640, 768, 1024, 1366, 1600, 1920).

```js
async function imageShortcode(src, alt, sizes, pageURL) {
  const imgPath = pageURL ? pageURL : "img";
  const metadata = await Image(src, {
    widths: [640, 768],
    formats: ["jpeg"],
    urlPath: ".",
    outputDir: "_site/" + imgPath,
  });
  const imageAttributes = {
    alt,
    sizes,
    loading: "lazy",
    decoding: "async",
  };
  return Image.generateHTML(metadata, imageAttributes);
}
```

You'll also need to add references to the imageShortcode function to the eleventyConfig `module.exports` so you can use the "image" short-code in your template tags. You may not need all three of these but it doesn't hurt either.

```js
eleventyConfig.addNunjucksAsyncShortcode("image", imageShortcode);
eleventyConfig.addLiquidShortcode("image", imageShortcode);
eleventyConfig.addJavaScriptFunction("image", imageShortcode);
```

Side note on something I realized while setting this up. [Eleventy-base-blog](https://github.com/11ty/eleventy-base-blog) uses Liquid as the default template language for Markdown files and Nunjucks for HTML and data files. I've become used to seeing .njk files everywhere and assumed my Markdown files were using Nunjucks as well.

```js
markdownTemplateEngine: "liquid",
htmlTemplateEngine: "njk",
dataTemplateEngine: "njk",
```

This broader topic is out of the scope of this post but you'll need to tell Eleventy to process image files and write them to your output directory. There are a few options for this which you should read about [in the documentation](https://www.11ty.dev/docs/copy/)

One option is to add "jpeg", "png", etc. to the templateFormats array. This is like a global setting that tells Eleventy to copy any file in your input directory with one of these file extensions. This may work for you if you want every image file to copy to your site output. I do not want my original images to copy to the output directory, however. I only want the generated responsive images to write to the output directory and I want the original images to stay in my input/source directory.

```js
templateFormats: ["md", "njk", "html", "liquid", "css"//*, "jpeg" *//],
```

So I added a ridiculous-looking addPassthroughCopy that will only copy .jpeg images in an individual blog post folder. This works because my original images actually live one directory up in the "day" folder in my input directory and are therefore not copied to the output directory. It's also why I couldn't use the more straight forward `blog/**/*.jpeg` because then the original images would be copied too.

```js
eleventyConfig.addPassthroughCopy("blog/*/*/*/*/*.jpeg");
// AKA /blog/year/month/day/postname/*.jpeg
```

## Custom Hash Names

I also want to maintain the original file name in my generated image file names. This was a straight copy and paste out of the previously linked Eleventy image documentation. Although you could customize it if you want. Just add this function to your imageShortcode function.

```js
filenameFormat: function (id, src, width, format, options) {
  const extension = path.extname(src);
  const name = path.basename(src, extension);
  return `${name}-${width}w.${format}`;
},
```

## Final imageShortcode function

```js
async function imageShortcode(src, alt, sizes, pageURL) {
  const imgPath = pageURL ? pageURL : "img";
  const metadata = await Image(src, {
    widths: [640, 768],
    formats: ["jpeg"],
    urlPath: ".",
    outputDir: "_site/" + imgPath,
    filenameFormat: function (id, src, width, format, options) {
      const extension = path.extname(src);
      const name = path.basename(src, extension);
      return `${name}-${width}w.${format}`;
    },
  });
  const imageAttributes = {
    alt,
    sizes,
    loading: "lazy",
    decoding: "async",
  };
  return Image.generateHTML(metadata, imageAttributes);
}

// Don't forget the exports...
eleventyConfig.addNunjucksAsyncShortcode("image", imageShortcode);
eleventyConfig.addLiquidShortcode("image", imageShortcode);
eleventyConfig.addJavaScriptFunction("image", imageShortcode);
```

## Still Not Working ?

At this point everything with eleventy-img seemed to be working fine. I had multiple different sized images created and the generated HTML looked good. But it still wasn't working. If you look at the first HTML example at the top of this post you'll see eleventy-img adds the "width" and "height" attributes for the default "src" fallback image in the markup. These default to the largest srcset image dimensions. My image was always showing at that size regardless of the window size so these seemed a likely suspect. I hopped into DevTools and deleted the width and height attributes and boom, it started working.

This led me to [Issue #66](https://github.com/11ty/eleventy-img/issues/66) and [Issue #63](https://github.com/11ty/eleventy-img/issues/63) and finally to [this blog post](https://www.zachleat.com/web/fluid-images/) by Zach Leatherman (the creator of Eleventy).

The easy solution without writing more code or trying to alter the eleventy-img package to omit the width and height attributes is to sprinkle some CSS on your `<img>` tags. This will override the hardcoded attributes and give you the fluidly scaling images you're looking for. This CSS is from Zach's post linked above.

```css
img {
  max-width: 100%;
}
img[width] {
  width: auto; /* Defer to max-width */
}
img[width][height] {
  height: auto; /* Preserve aspect ratio */
}
```

## Helpful Reading

- [Google Developers Web Fundamentals - Responsive Images - Pete LePage](https://developers.google.com/web/fundamentals/design-and-ux/responsive/images)
- [Responsive Images - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)
- [Complete Guide to Responsive Images - Elad Shechter](https://elad.medium.com/a-complete-guide-for-responsive-images-b13db359c6c7)
- [Applying srcset: choosing the right sizes for responsive images at different breakpoints - Paolo Mioni](https://medium.com/hceverything/applying-srcset-choosing-the-right-sizes-for-responsive-images-at-different-breakpoints-a0433450a4a3)
- [How to optimize images on eleventy (11ty) - Mahmoud Ashraf](https://dev.to/22mahmoud/how-to-optimize-and-lazyload-images-on-eleventy-11ty-206h)
- [Barebones CSS for Fluid Images - Zach Leatherman](https://www.zachleat.com/web/fluid-images/)
