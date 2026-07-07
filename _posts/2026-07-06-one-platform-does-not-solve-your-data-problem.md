---
title: "One data platform does not solve your data problem"
date: 2026-07-06
---

Somewhere in every data platform pitch there's a slide that says something like "a single source of truth." I have sat through a lot of these slides. I believe the person presenting them. I also think the slide is wrong, or at least wrong about what happens next.

Here is the promise: buy (or build) the platform, and your data problems go away. Here is what actually happens: your data problems change shape. You trade "I don't know where this number comes from" for "I know exactly where this number comes from, and it takes forty minutes to find out why it's wrong." That's not nothing. It's also not what was promised.

> Nobody sets out to create clutter.

The thing that makes a platform worth having is flexibility. You can plug in new sources, let teams self-serve, build their own pipelines, define their own models. 

That's also the thing that makes it a mess six months later. Nobody sets out to create clutter. It happens one reasonable decision at a time: one more source added without a clear owner, one more transformation nobody quite remembers the reasoning for, one more team that built their own version of a table that already existed somewhere else, because finding the existing one was harder than writing a new query.

This is basically the story of data mesh. The idea is good: distribute ownership to the teams closest to the data, let them move fast, stop bottlenecking everything through one central team. And it works, structurally. 

What it does not do on its own is guarantee that anyone treats their piece of it with the same care. Distribute ownership without distributing standards, and you don't get a mesh. You rather get a mess with better branding. Same clutter, just with an org chart that makes it look intentional.

None of this means platforms are a bad idea. I'd take a decent platform with known problems over an ad hoc mess of spreadsheets and one person's Python scripts any day. But "we got a platform" is the beginning of the real work, not the end of it. 

Once the data platform is live, the interesting questions start: who decides what counts as a trustworthy dataset, who's allowed to publish one, and what happens when two teams build the same thing twice because nobody could tell them not to.

That's what this series is about, really: the platform was never the hard part. The posts that follow get into why the org chart matters more than the tech stack, where the line between flexibility and governance should sit, and what a workable answer to "how do we scale this" looks like, with an actual example instead of a diagram.
