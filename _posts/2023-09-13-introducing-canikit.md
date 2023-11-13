---
layout: post
title: Introducing CanIKit - Don't forget to authorize!
date: 2023-09-13 22:25:18
description: The missing authorization library for SvelteKit!
tags: authorization, sveltekit
categories: open-source
featured: true
---

It's conventional wisdom as old as time itself.

Do. not. roll. your. own. auth(__enitcation__).

And as a developer community, we've done a pretty good job of following that advice. We've got a plethora of authentication libraries, and frameworks that make it easy to add authentication to our applications.

But what about _authorization?_ Some frameworks have community endorsed authorization libraries that do a great job of adding authorization to our applications. Rails has __[Pundit](https://github.com/varvet/pundit)__, Go has __[casbin](https://github.com/casbin/casbin)__, __ASP.NET__ has it built directly into the framework.

But what about __SvelteKit?__ Currently, SvelteKit developers are left to roll their own authorization solutions as part of their routing logic. Authorization logic is typically mixed in with the server-side route logic.

This has a few major drawbacks:

* The Authorization logic is much harder to reason about.

* The Authorization logic is much harder to test.

* It is much, much easier to forget to add authorization logic all together.

Given this, what should we look for in an authorization library?

* Authorization logic should be isolated from everything else. This will make it easy to find, easy to reason about, and easy to test.

* Authorization logic should be enforced. If authorization logic is not called, an error should be thrown. Force the developer to think about authorization at all times. Don't rely solely on code reviews to catch missing authorization logic!


## Introducing CanIKit

CanIKit is a SvelteKit library that provides a simple way to add authorization to your SvelteKit application. It is heavily inspired by __[Pundit](https://github.com/varvet/pundit)__ 

Every single +page.server, +layout.server, or +server file in your SvelteKit app will now require a corresponding +policy file. The policy file is responsible for answering the question

> Can I (the current user) do this (action) on this (resource)?

A simple policy file might look like this (you can just export a `view` function directly if you prefer):

```typescript
// routes/dashboard/page.policy.ts
import { CanIKitPolicy } from "canikit";

export default class Policy extends CanIKitPolicy {
  public view() {
    return this.user?.role === "admin";
  }
}
```

This policy file will be used by the corresponding page.server file:

```typescript
// routes/dashboard/+page.server.ts

export async function get({ session, locals: { canI } }) {
    const user = ... // get the user from the session
    await canI({ user }) // Check to see if the user can view the dashboard
}
```

If the developer forgets to call `canI` in the page.server file, an error will be thrown:

You can read more about CanIKit on the __[GitHub repo](https://github.com/tombroomfield/CanIKit)__.


Happy Authorizing!
