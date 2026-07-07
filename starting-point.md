# Blog backlog

## Series: Data platforms

A four-part series on why data platforms don't solve the problems people buy them for, and what actually helps.

Storytelling approach: promise and overdeliver, like Mark Rober — open with the claim, then earn it.

### Post 1: One platform doesn't solve your data problem — status: drafted

Angle: buying/building a data platform doesn't remove your underlying data problems, it trades them for a new set.

Key points:
- A platform promises to solve everything, but introduces its own new problems
- The flexibility that makes a platform useful is the same thing that creates mess
- "Data mesh" often becomes "data mess" in practice

Avoid: don't get into governance or ownership here (that's post 3), don't get into specific tooling (that's post 4).

### Post 2: Making a functional data platform is not a technical problem

Angle: the hard part of a data platform isn't the tech stack, it's the organization around it.

Key points:
- Problems scale with org size, not with data volume. Small organization, people know the available datasets. With bigger organizations, people tend to stick to their familiar branch, and datasets easily become duplicated with different colors.
- What "functional" means changes as the org grows — a platform that works for one team breaks down across ten
- Where the data-mesh idea already assumes an organizational maturity most orgs don't have
- the vision should be to build datasets hence data domains - often (unfortunatley) managed by one team - which lasts and lives healty also after an org change. Domains could be a database, schema, dbt project, etc.

Avoid: don't repeat post 1's "platform doesn't solve everything" framing, take it as given and move on.

### Post 3: Flexibility vs governance — where to draw the line

Angle: every platform sits somewhere on a flexibility/governance spectrum, and most orgs default to the wrong end without deciding on purpose.

Key points:
- Who should own a data product — the data engineer who builds it, or the business stakeholder who needs it?
- Is a data product one dataset or a bundle of related ones? This decision is a governance choice, not a technical one
- Need for a quality "stamp" on a data product before others can trust and reuse it
- What you gain and lose at each end of the spectrum — be concrete, not both-sides-y

Avoid: don't resolve this into a single "best practice," the point is that it's a real trade-off.

### Post 4: Practical thoughts on scaling

Angle: grounded, example-driven follow-up — what does dealing with the scaling problem actually look like.

Key points:
- Is bronze/silver/gold (e.g. Matillion's medallion pattern) practically sufficient, or does it fall short in practice?
- NBIM as a reference case: raw → int → mrt, with dbt
- Underlying platform rot gets worse — and more visible — once AI gets layered on top of it

Avoid: keep this concrete and example-led, it's the payoff post after three posts of framing.

## Idea bank (unsorted, not yet assigned to a post)

- How do you actually choose the right dataset in the first place?
