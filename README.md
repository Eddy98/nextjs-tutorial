## Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

# Notes

## Chapter 1 - Getting Started

- `/app` Contains the routes, components and the logic of the application
- `/app/lib` Contains functions used in your application, such as reusable utility functions and data fetching functions.
- `/app/ui` Contains all the UI components for your application
- `/public` Contains all the static assets

## Chapter 2 - Styling

If you look inside the `/app/ui` folder, you'll see a file called `global.css`. You can use this file to add CSS rules to all the routes in your application - such as CSS reset rules, site-wide styles for HTML elements like links, and more.

Import `global.css` to the root layout to apply styles to entire application

We are using `Tailwind` to add styling

Tailwind is a CSS framework that speeds up the development process by allowing you to quickly write utility classes directly in your TSX markup.

In Tailwind, you style elements by adding class names

An alternative to styling is to use CSS Modules, by creating respective CSS modules like `app/ui/home.module.css` and importing these into respective components

`clsx` - librabry to toggle classnames

## Chapter 3 - Optimizing Fonts and Images

Fonts can affect performace of an application as they can be loaded in after an app has already loaded, shifting elements around

Next.js optimizes fonts by using the `next/font` module. Which downloads font files at build time and hosts these with the other static assets.
No additional network requests for fonts.

We created `app/ui/fonts.ts` where we create the fonts object that we use in the applcation.

We should also optimize images, for that we can use `next/image`

The `<Image>` Component is an extension of the HTML `<img>` tag, and comes with automatic image optimization, such as:

- Preventing layout shift automatically when images are loading.
- Resizing images to avoid shipping large images to devices with a smaller viewport.
- Lazy loading images by default (images load as they enter the viewport).
- Serving images in modern formats, like WebP and AVIF, when the browser supports it.

It is good practice to define the width and teh heigh of your images to avoid layout shifting. Images without dimensions and web fonts are common causes of layout shift.

Use the next js `Image` component to add any new image.
For example:

```
<Image
    src="/hero-desktop.png"
    width={1000}
    height={760}
    className="hidden md:block"
    alt="Screenshots of the dashboard project showing desktop version"
/>
```

## Chapter 4 - Creating Layouts and Pages

Next.js uses file-system routing where folders are used to create nested routes. Each folder represents a route segment that maps to a URL segment.

`layout.tsx` and `page.tsx` are special. You can create separate UIs for each route using these filenames.

`page.tsx` is a special Next.js file that exports a React component, and it's required for the route to be accessible. In your application, you already have a page file: `/app/page.tsx` - this is the home page associated with the route `/`.

`layout.tsx` is also speccial. We can use this file to create UI that is shared among multiple pages.

The `<Layout />` component receives a children prop. This child can either be a page or another layout. In your case, the pages inside `/dashboard` will automatically be nested inside a `<Layout />`.

One benefit of using layouts in Next.js is that on navigation, only the page components update while the layout won't re-render.

`/app/layout.tsx` is the root layout, and it is requried.

Any UI you add to the root layout will be shared across all pages in your application. You can use the root layout to modify your <html> and <body> tags, and add metadata (you'll learn more about metadata in a later chapter).
