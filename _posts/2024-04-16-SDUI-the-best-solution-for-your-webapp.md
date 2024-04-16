---
layout: post
title:  "SDUI: The Best Solution For Your Web Apps"
date:   2024-04-16
tags: [ development, SDUI ] 
---
Hi! Do you know what SDUI is? Maybe you have heard this abbreviation before? If not, don't worry, I'll try to explain it.

In traditional web development, we have a backend (server-side) and a frontend (UI side). A backend engineer creates an API for a specific feature. Usually, this API is based on Figma prototypes. It's cool when you're making an MVP. As your app grows, you need to create many APIs with different logic, conduct A/B testing on customers, and plan releases, etc.

> SDUI - Server Driven User Interface

This is an architectural principle that dictates which elements should be shown in the UI and how they interact with each other. SDUI is more about the frontend and less about the backend. It's a kind of middleware layer like BFF (backend for frontend), but SDUI provides you the ability to construct the UI on the backend side. There are some benefits that SDUI can offer.

### UI looks the same throughout the application
The main element in SDUI is the design component. Each component should be described in a Figma project. It contains design and behavior. This is a common building block of your app.

Created once - reused everywhere with the same design and logic. You don't need to code this piece anymore, just place them in a SDUI JSON structure.

### Less time to market
When you release an app on app store or Android distribution platforms, you have to wait for approval from moderators. It can take an unpredictable amount of time. I did some research and it turns out that most releases contain only UI features without any logic.

Of course, UI features make customers happier, but we don't necessarily need to release each time when the product owner decides to change the button's color.

Also, we can easily modify the UI on the backend side by editing json in the SDUI layer. You can add more screens or custom logic for specific customers or groups of customers.

If you want to conduct A/B testing with product metrics, it is also modified on the SDUI backend side.


### The frontend does not know about business logic.
The SDUI pattern encapsulates business logic in the backend layer. The frontend doesn't contain extensive logic; it only draws elements and interacts with customers. And these interactions are the same on all platforms.

### Decrease costs for the engineering team.
With SDUI, you can separate your team by grades from junior to senior developers. You can assign tasks based on their difficulty.

Some engineers make the SDUI layer, which is easier than creating business logic.

SDUI is a logical evolution of web development where the frontend should draw the UI and the backend should process the business logic. These layers should not be mixed.