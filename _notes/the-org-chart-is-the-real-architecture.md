---
title: "The org chart is the real architecture"
date: 2026-07-07
---

*This is the second post in a series on data platforms. If you haven't, [[One data platform does not solve your data problem|read part 1]] first.*

Making a data platform functional was never a technical problem. It's an organizational one, and it gets harder as your org grows, not as your data does.

> The org chart is the real architecture; the tech stack just draws it.

In a five-person team, everyone knows the handful of datasets that exist and who built them. At fifty people, nobody holds that map anymore. Teams stick to their own corner, don't know what already exists next door, and quietly rebuild it: same numbers, slightly different logic, different name. Not because anyone wanted three versions of "active customers." Because finding the existing one was harder than writing a new query.

"Functional" moves too. For one team, functional means: ask Sarah, she built it. Across ten teams, that stops working the moment Sarah changes roles, and nobody agrees on who inherited what she built.

Reorgs make this visible fast. Boundaries move overnight; the data doesn't move with them. A dataset's owning team gets merged or split, and the dataset just sits there, kept alive by whoever still has write access, while the new domains around the new chart get all the attention. Nobody decided to abandon it. It just stopped being anyone's problem the day the boxes moved. Whoever officially inherits it later usually doesn't want it: owning code you didn't write and can't explain isn't a promotion, it's debt with your name on the pager.

This is where data mesh quietly assumes something most orgs don't have: real organizational maturity. Distributing ownership to the teams closest to the data only works if those teams can actually hold onto it, through growth and through reorgs. Most companies aren't there yet, which is how "we're doing mesh" turns into the mess from part 1.

The version that survives contact with an org chart: build data domains, not just datasets. A domain is a bounded, durable unit, a database, schema, or dbt project, that one team owns end to end. In practice, that means one team, which isn't the ideal but is what actually holds up. The fence isn't the point. Survivability is: when the reorg hits, you hand over the whole domain intact instead of untangling one orphaned table from a shared pile.

That still has to be enforced, especially right after a reorg, when nobody's checking whether the handoff happened. The real test isn't the rule, it's holding it the first time a team with more political capital wants to skip the line, because it's urgent, or because building around someone else's boundary is faster than working within it. The first quiet exception turns the rule into a suggestion.

Same mesh-versus-mess problem from part 1, one layer down: it only holds up if the org is mature enough to keep its promises about who owns what, especially when the chart changes.

Next: where the line between flexibility and governance should actually sit, because "more governance" is not automatically the right answer either.
