---
title: "Typing the routes: TanStack Router"
description: "A Quick Introduction to TanStack ROuter"
dateString: Feb 2023
draft: false
tags: ["React", "Tanstack router", "TypeScript"]
weight: 101
cover:
    image: "/blog/tanstack-router/tanstack.png"
---

## Introduction

Routing is used in most of the web apps or websites today. In front-end side, Routing is basically way to handle requests from URL and navigate the user to the required section of the website.

ReactJS **does not come** with an in-built solution for routing. It recommends using **react-router-dom** library to do the routing. It serves the purpose, but it does not have a great TypeScript support for routes.

For example, when we declare a route in react-router, we do it:

```javascript
<Route path="/signup" element={<Signup />} />
```

To navigate to this page we use a &lt;Link&gt; component like follows:

```javascript
<Link to={'signup'}>
{'Sign Up'}
</Link>
```

But the problem is that even when we change it to:

```javascript
<Link to={'not-signup'}>
{'Sign Up'}
</Link>
```

It won't show any error until we navigate to that specific route in the app i.e. to `/signup`. This way it can end up in production code as well.

[TanStack Router](https://tanstack.com/) has come to our help to solve this problem.

There are many great features of Tanstack router but important ones are:

* <mark>Typesafe Routes</mark>
    
* Devtools
    
* Typesafe Path Params
    
* Devtools
    
* Custom Search Param
    
* Auto Route Prefetching
    
* Nested / Layout Routes
    

## Installations

TanStack React Router is compatible with React v16.8+

```bash
npm install @tanstack/router@beta
```

## Usage

To start off, we will create an index route that will serve as a base route as following:

#### Root Route

```javascript
const rootRoute = new RootRoute({
  component: Root,
});
```

I will be using TailwindCSS for styling. We will split the code for the actual component to keep it clean.

```typescript
function Root() {
  return (
    <div className="flex h-screen">
      <div className="flex flex-col gap-6 items-center h-screen w-1/4 bg-slate-600">
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link
          to="/projects/$projectId"
          params={{
            projectId: "my-first-blog-post",
          }}
        >
          Projects
        </Link>
      </div>
      <hr />
      <Outlet />
    </div>
  );
}
```

In the above code, there are 2 things

1. Link
    
2. Outlet
    

`Link` is used to navigate to the specified URL. This is similar to react-router with a few differences.

`Outlet` is where our child routes will be rendered.

> ***Note***: Route paths are <mark>not case-sensetive</mark>. For example, /signup and /SignUp are the same.

#### Child Routes

Let's start creating routes that we will render in the `Outlet` of the root route.

```typescript
const homeRoute = new Route({
  getParentRoute: () => rootRoute,
  path: "/",
  component: Home,
});
```

We can pass a lot more properties while creating new Route, but the essential ones are **getParentRoute**, **path**, **component**.

Just like this, we will create 2 more routes.

```typescript
const aboutRoute = new Route({
  getParentRoute: () => rootRoute,
  path: "/about",
  component: About,
});

const projectsRoute = new Route({
  getParentRoute: () => rootRoute,
  path: "/projects",
});

// Route with a dynamic parameter
const projectRoute = new Route({
  getParentRoute: () => projectsRoute,
  path: "$projectId",
  component: Projects,
});
```

For demonstration purposes, I have made a `projectsRoute` to showcase

After creating routes we will create a Route Tree and pass it to `ReactRouter` while creating a new router.

When it creates our router it will **automatically sort all the routers**. So we don't have to worry about how we order our routes!

> You can read about router matching in the [offical docs](https://tanstack.com/router/v1/docs/guide/route-matching).

#### Creating the Router

```typescript
// Router Tree
const routeTree = rootRoute.addChildren([
  homeRoute,
  aboutRoute,
  projectsRoute.addChildren([projectRoute]),
]);
```

```typescript
const router = new ReactRouter({ routeTree });
```

We have to explicitly include all the children routes for `projectsRoute`.

#### Type Safety

Then to get the Type Safety all over our project we declare module as follows:

```typescript
declare module "@tanstack/react-router" {
  interface Register {
    router: typeof router;
  }
}
```

Finally, you can include the `RouterProvider` component in your render function.

You have to pass the `router` to the `ReactProvider`.

We have covered the basic functions of the TanStack router. Let's look at some more cool features.

#### Search Params

You can pass search parameters to the `Link` component. We create a Link for About page as follows:

```typescript
<Link
    to="/about"
    className="bg-gray-400 p-4"
    search={{
    title: "TanStack",
            sortBy: "name",
            desc: true,
    }}>
        About
</Link>
```

#### DevTools

You can also pass `<TanStackRouterDevtools router={router} />` to your render function to show DevTools on your site. More information can be found [here](https://tanstack.com/router/v1/docs/devtools).

GitHub repository for this can be found [here](https://github.com/mak2002/TanStack-Router-Tutorial).

Thank you for reading this blog. Feel free to provide any feedback you have.