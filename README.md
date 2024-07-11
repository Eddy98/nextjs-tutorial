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

## Chapter 5 - Navigating Between Pages

### Why optimize navigation?

To link between pages, you'd traditionally use the `<a>` HTML element. At the moment, the sidebar links use `<a>` elements, but notice what happens when you navigate between the home, invoices, and customers pages on your browser.

Did you see it?

There's a full page refresh on each page navigation!

In next.js we can use the `Link` component, which gives us:

- Automatic code-splitting and prefetching

To improve the navigation experience, Next.js automatically code splits your application by route segments. This is different from a traditional React SPA, where the browser loads all your application code on initial load.

Splitting code by routes means that pages become isolated. If a certain page throws an error, the rest of the application will still work.

Furthermore, in production, whenever `<Link>` components appear in the browser's viewport, Next.js automatically prefetches the code for the linked route in the background. This makes the re-direct instant.

## Chapter 6 - Setting up your database

### Vercel

Provides fast deployment of application, and integreates with Github. Comes with CICD out of the box.
By connecting your GitHub repository, whenever you push changes to your main branch, Vercel will automatically redeploy your application with no configuration needed. When opening pull requests, you'll also have instant previews which allow you to catch deployment errors early and share a preview of your project with team members for feedback.

### Postgres Database

Postgres set up is also integrated into Vercel. Through the portal i was able to create a DB instance, specify the environments where it would be available.
I then copied the env secrets into my codebase, and ran script to seed the DB.

Through Vercel i was abel to interact with the DB as well, running SQL queries.

## Chapet 7 - Fetching Data

### API layer

APIs are an intermediary layer between your application code and database. There are a few cases where you might use an API:

- If you're using 3rd party services that provide an API.
- If you're fetching data from the client, you want to have an API layer that runs on the server to avoid exposing your database secrets to the client.
  In Next.js, you can create API endpoints using Route Handlers.

There are a few cases where you have to write database queries:

When creating your API endpoints, you need to write logic to interact with your database.
If you are using React Server Components (fetching data on the server), you can skip the API layer, and query your database directly without risking exposing your database secrets to the client.

### Server components

By default, Next.js uses React Server Components

- Server Components support promises, providing a simpler solution for asynchronous tasks like data fetching. You can use async/await syntax without reaching out for useEffect, useState or data fetching libraries.
- Server Components execute on the server, so you can keep expensive data fetches and logic on the server and only send the result to the client.
- As mentioned before, since Server Components execute on the server, you can query the database directly without an additional API layer.

You can call sql inside any Server Component.

However... there are two things you need to be aware of:

- The data requests are unintentionally blocking each other, creating a request waterfall. (This is from the request we have added in `app/dashboard/page.tsx`, where we have sequentially added multiple fetches)
- By default, Next.js prerenders routes to improve performance, this is called Static Rendering. So if your data changes, it won't be reflected in your dashboard.
  Let's discuss number 1 in this chapter, then look into detail at number 2 in the next chapter.

### What are request waterfalls?

A "waterfall" refers to a sequence of network requests that depend on the completion of previous requests. In the case of data fetching, each request can only begin once the previous request has returned data.

### Parallel data fetching

In JavaScript, you can use the `Promise.all()` or `Promise.allSettled()` functions to initiate all promises at the same time. For example, in `data.ts`, we're using Promise.all() in the fetchCardData() function:

By using this pattern, you can:

- Start executing all data fetches at the same time, which can lead to performance gains.
- Use a native JavaScript pattern that can be applied to any library or framework.

There is the disadvantage that one request might slow down the rest

## Chapter 8 - Static and Dynamic Rendering

### What is static rendering

With static rendering, data fetching and rendering happens on the server at build time (when you deploy) or when revalidating data.

Whenever a user visits your application, the cached result is served. There are a couple of benefits of static rendering:

- Faster Websites - Prerendered content can be cached and globally distributed. This ensures that users around the world can access your website's content more quickly and reliably.
- Reduced Server Load - Because the content is cached, your server does not have to dynamically generate content for each user request.
- SEO - Prerendered content is easier for search engine crawlers to index, as the content is already available when the page loads. This can lead to improved search engine rankings.

Static rendering is useful for UI with no data or data that is shared across users, such as a static blog post or a product page. It might not be a good fit for a dashboard that has personalized data which is regularly updated.

The opposite of static rendering is dynamic rendering.

### What is Dynamic Rendering

With dynamic rendering, content is rendered on the server for each user at request time (when the user visits the page). There are a couple of benefits of dynamic rendering:

- Real-Time Data - Dynamic rendering allows your application to display real-time or frequently updated data. This is ideal for applications where data changes often.
- User-Specific Content - It's easier to serve personalized content, such as dashboards or user profiles, and update the data based on user interaction.
- Request Time Information - Dynamic rendering allows you to access information that can only be known at request time, such as cookies or the URL search parameters.

With dynamic rendering, your application is only as fast as your slowest data fetch.
