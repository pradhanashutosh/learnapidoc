---
title: "Example of DevX Usability"
permalink: /devx-usability-example.html
course: "Documenting REST APIs"
weight: 7.91
sidebar: docapis
section: docapiscode
path1: /doccode.html
---

In [API design and usability](evaluating-api-design.html), I covered different aspects of usability related to API design. In this topic, I'll explore usability related to developer processes and technologies in general, independent of API design.

* TOC
{:toc}


## Three types of usability

Usability can be roughly divided into at least three different areas:

* [Usability with physical products](#physical_products)
* [Usability with code products](#code_products)
* [Usability with documentation](#documentation_products)

(Many other divisions could likely be made, for but the discussion here, this division is enough.)

## Usability with physical products {#physical_products}

Usability with physical products or graphical user interfaces (GUIs) is what people traditionally think of with usability. When you have a physical product to use, assessing the usability is more straightforward.

For example, suppose you're testing the usability of a new headphone design. A usability researcher might bring in a group beta testers to try the headphones on, ask them to adjust the headphone controls, use the headphones in their daily workflows, and so on. Users might evaluate the headphones based on comfort, familiarity of buttons, how easy they might be to pair to bluetooth, the placement of the microphone, and so on.

Usually, technical writers are too far down the development line to influence product design. By the time the product arrives at your door, most product teams just ask technical writers to provide documentation for the product, not to provide input on design itself. Usually, it would not be expected (or desired) for you to push back on the design and try to get the product to team to reconsider fundamental aspects such as the placement of the microphone or the fit over your ears. You might be able to influence *button names* at this point, and maybe catch glitches in the software (e.g., maybe the bluetooth pairing is wonky), but don't expect too much. Changing product design is usually required before the specs are sent to the factory. After factory lockdown, it's hard to change a product. And documentation is usually something that begins after factory lockdown.

That's not to say that you shouldn't venture forth to comment and critique product design. Just don't get your hopes up if the timeline doesn't allow for drastic changes. By the way, some technical writers get tired of documenting poorly designed products, so they move into usability and product design. Once you make this move, you're usually operating outside the standard tech writer domain. For more on playing a product design role, see [Playing a product design role as a content designer -- podcast with Jonathon Colman](/blog/podcast-colman-playing-product-design-roles-as-a-content-designer/).

Product design has a rich history of research and best practices. See this book for many good ideas on how to assess the usability of a product: [*Universal Principles of Design, Revised and Updated: 125 Ways to Enhance Usability, Influence Perception, Increase Appeal, Make Better Design Decisions, and Teach through Design*](https://www.amazon.com/Universal-Principles-Design-Revised-Updated/dp/1592535879).

## Usability with code products {#code_products}

In the developer documentation domain, usability with code products is more common. Few usability researchers venture into the code domain because it's much less clear how to assess the usability of code. But rest assured, usability is just as much in play with code products for developers as with physical products or products with GUIs.

Code usability asks questions such as, how easy is it for developers to use the code? Do the APIs provide the information they need? How long does it take them to figure it out and implement it? Are you using a language or framework that will be familiar to your audience? Are you following general patterns in the industry for tackling problems?

Let me illustrate with an example. One product I recently documented was the [Video Skills Kit for Fire TV](https://developer.amazon.com/docs/video-skills-fire-tv-apps/introduction.html) (for Amazon). The first implementation involved a [cloudside implementation](../video-skills-fire-tv-apps/integration-overview-cloudside.html) that involved about a dozen different technologies and steps. The implementation required devs to use several AWS services (Identity Access Management, CloudWatch, Lambda), several Appstore services (Login with Amazon and security profiles, Alexa console, a client library (JAR), Amazon Device Messaging, Live App Testing), as well as configure a Fire TV device through the Alexa app and connect through ADB. These services were on top of Android services that developers were expected to know.

Unsurprisingly, users were unfamiliar with all of these services, so not only was there an initial learning curve, when they did run into problems, the problems were hard to troubleshoot. Additionally, troubleshooting was also complex not just due to the unfamiliar toolset, but because there were a dozen different places where something might be misconfigured. For example, after integrating everything, you were supposed to be able to say "Alexa, watch Interstellar," and your app would play a video. But if it didn't work, you had to figure out where the problem was. The logs could provide a clue, but if you had some invalid formatting in your Lambda function, Alexa might respond, "Something went wrong," with no other logs. Alexa might respond similarly for a lot of different issues, as there are many backend services (invisible to the user) that process the information. For example, one issue might be with the natural language processing logic occurring behind the scenes, which is opaque to the third-party developer.

The cloudside implementation took a long time (multiple months, usually). We had a sample app, and each time I would go through the steps to set up the services around the app, it would take the entire afternoon or more. So many pieces had to be in place, and many times it felt like a house of cards &mdash; if one little piece was misconfigured (e.g., signing your app with the wrong key), the whole thing didn't work.

Fast-forward two years later. The product team released a new implementation approach to the Video Skills Kit called [app-only integrations](https://developer.amazon.com/docs/video-skills-fire-tv-apps/integration-overview-app-only.html). The app-only integration grounded most of the configuration within Android itself. Developers didn't have to bother with configuring any AWS services, no Lambda functions, no authorization through ADM. Developers instead would need only configure BroadcastReceivers in their Android app to handle incoming messages sent from an on-device routing agent built into Fire TV.

Partners welcomed this approach much more wholeheartedly. Not only did the implementation stay within the technology realm they were familiar with (Android), it didn't cobble together a dozen different services that all had to be working in harmony for success. Troubleshooting was also simplified because most issues were contained within the same Android platform. Implementation time was reduced to a couple of weeks instead of months.

Finally, one other aspect of the app-only implementation improvement was the speed of communication. With the first solution (cloudside), messages had to travel from one service to another (Alexa in the cloud to a Lambda function in AWS, then to the partner's app on Fire TV). With the app-only integration, most of the communication took place on the device itself. Alexa sent a message to an on-device routing agent that communicated with the app (also installed on the device). The communication between components was much faster, with fewer latency problems.

In general, when evaluating code usability, consider these questions:

* Does the implementation use a technology that your developers are familiar with?
* Does the implementation rely on a small number of core services/components rather than a dozen disparate services cobbled together?
* How easy will it be to troubleshoot errors when things go wrong?
* How long will it take developers to complete the implementation?
* Is there latency for communication across the different components?

Evaluating developer usability can be hard because many times, there are technology constraints that make more streamlined approaches problematic. No one sets out to build a house of cards, but as you encounter one issue after another, with fast deadlines to finish the code, you might adopt solutions that work in the short term but which require a lot of hacks. String together too many hacks and accrue too much technical debt (shortcuts that provide fixes while leaving larger problems unresolved), and you head down the path to a house-of-cards solution.

In general, teams building developer tools strive to provide APIs or other tools that simplify the third-party user's implementation. You want to abstract complexity behind an API, so that the developer need only provide the specified inputs and gets the desired outputs in a response. The developer probably doesn't care (or need to know) how the sausage is made on the backend to transform the inputs into outputs. If you can abstract more of this complexity behind the scenes, it will result in a better developer experience.

Assessing the architectural design of a developer solution isn't easy for most technical writers. Unless you have an engineering background and can assess the feasibility of a code solution, you might not have a lot of input. A lot of factors go into an engineering, including security, memory, infrastructure, efficiency, latency, and more. In most scenarios, tech writers struggle just to understand the workflow and describe it. Going the extra mile to suggest a more efficient and streamlined architectural design is often something that would be "next-level" for tech writers.

However, if you keep an eye out for the questions I mentioned above, this can provide you with a starting point for evaluation. You're often the first user of the code product, so teams value your feedback, even if you're not an engineer.

## Usability withy documentation products {#documentation_products}

You can also incorporate usability with documentation products. How easy is the documentation to understand and follow? is the documentation something that users can easily search to find information? Does the documentation assume that users have deep background knowledge about all the services, or does it help even novice users complete all the tasks?

As an example, consider the car manual in your glovebox. Suppose your headlight goes out and you want to replace it. Or suppose a light flashes on your indicator panel ("LDW") and you need to look up what it means. Does the user manual make it easy to find information, such as through a search or index? Are the terms used in the manual similar to the terms you would use to describe the features? When you do locate the right section, are the steps easy to follow? Are there visuals that help clarify the information? Can the documentation take even someone who isn't mechanically inclined through a sequence of steps to achieve a difficult task?

Most of the technical writer's job involves simplifying complexity. I wrote an [entire series on simplifying complexity](/simplifying-complexity). One strategy is to provide users with a map to guide them through a complex number of steps. See [Principle 1: Let users switch between macro and micro views](/simplifying-complexity/macro-micro.html).

How do you know if your documentation is easy to follow? Just as you can observe users interact with products or interfaces in a usability lab, you can also do so with documentation. Ask users to do a task they're unfamiliar with, and see how they interact in your help system to locate the information. I outlined some notes from my observations in a usability lab some years ago. See [A Few Notes from Usability Testing: Video Tutorials Get Watched, Text Gets Skipped](/2011/07/22/a-few-notes-from-usability-testing-video-tutorials-get-watched-text-gets-skipped/). I observed two basic patterns. Novice users welcomed video tutorials and read the documentation carefully, sometimes multiple times. However, advanced users skimmed and scanned, not reading carefully but just trying to find a specific piece of information. Another observation: something you think might be simple usually turns out to be way more complex and difficult for users. Remember this and make your instructions as simple as possible, assuming that users will be reading while looking elsewhere, dealing with interruptions, pausing and resuming the task, and more.

Many of the same principles in [*Universal Principles of Design, Revised and Updated: 125 Ways to Enhance Usability, Influence Perception, Increase Appeal, Make Better Design Decisions, and Teach through Design*](https://www.amazon.com/Universal-Principles-Design-Revised-Updated/dp/1592535879) can also be used in documentation. See [Introduction to User-Centered Documentation](/ucd-introduction/) for more details.

## Video

I recently gave a short interview for a usability class. The presentation doesn't cover all the details from above, but it does touch upon usability in different scenarios (e.g., professional writing versus the blog, academic contexts, and other). The interview is with Kirk St. Amant. You can see his usability course online at [https://usability-ux.weebly.com/](https://usability-ux.weebly.com/). Here's my video:

<iframe width="560" height="315" src="https://www.youtube.com/embed/d3r1_38mpI0" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

You can view other interviews Kirk has done with usability professionals here:

* [Interview with Dr. Katie Panciera of the Milwaukee School of Engineering](https://www.youtube.com/watch?v=uPUJq4w4xNU)
* [Interview with Dr. Joy Robinson of University of Alabama in Huntsville and Northrop Grumman](https://www.youtube.com/watch?v=f_rMFwee9KI)
* [Interview with Maiken Blok-Wahlgreen of TimeLog](https://www.youtube.com/watch?v=V5A19tiMfo4)
* [Interview with Dr. Quan Zhou of Metro State University](https://www.youtube.com/watch?v=L23Y8ooX-a0)
* [Interview with Joe Welinske of WritersUA/ Welinske & Associates](https://www.youtube.com/watch?v=78c10I35QyI&t=2s)
* [Featured Lecture with Eva Snee of Google](https://www.youtube.com/watch?v=g1_wILJCuFY)

For more, see [Kirk's Guest Lectures](https://usability-ux.weebly.com/guest-lectures.html).
