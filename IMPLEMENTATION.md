# Implementing the Task Board deliverables

Twenty-one deliverables were commissioned, paid for and settled on-chain across
Cycles 1 and 2 of the Redbelly DAO Community Task Board. Most of them are
content this site has nowhere to put yet. This document says where each one
goes, what has to be built to receive it, and in what order.

It is a companion to `features/dao/content-spec.ts`, which describes the data
each empty DAO surface expects. That file covers the surfaces that already
exist. This one covers the surfaces that do not.

The complete record of the deliverables, including who is credited for each and
under what settlement they were paid, is in the `submissions` repository in this
organisation.

## What is in hand

Fifteen of the twenty-one are held by the DAO today. Six are still with their
contributors and have not been transferred.

| Outstanding | Deliverables | Effect |
|---|---|---|
| codedforum | Tasks 13, 14 and 23 | Two developer routes and the changelog wait |
| poundeater | Tasks 17 and 20 | The entire Start Here section waits |
| ankurghai | Task 3 | One reference contract waits |

Task 3 is published by its author under the MIT licence, so it can be taken
without further permission. The other five need the transfer to complete.

Work on the sections whose deliverables are already in hand first. The order
below reflects that.

## Three treatments

Not every deliverable becomes a page in this repository, and treating them
uniformly would be a mistake.

**Merged.** Documentation and written work is ported into this site as routes.
The source repository stays in the organisation as the record of what was
submitted, but the published version lives here.

**Linked.** Anything with its own build and its own deployment stays a separate
repository. Folding a Hardhat project or a Dockerised dashboard into this
application would mean maintaining several build systems under one root for no
benefit. These are indexed from this site and deployed separately.

**Published as assets.** The social asset pack is not a reading page and should
not be turned into one.

---

# Step 1. Create the Start Here section

The site currently has three pathways, and each answers a question that somebody
already technical is asking. Four paid deliverables answer the questions a
person asks in their first week with RBNT. They have nowhere to go in the
present information architecture, so the section has to be created before they
can be placed.

Task 17 was commissioned as exactly this hub and already carries the topic chips
to hang the other three off. It becomes the section shell rather than a page
inside it.

### 1.1 Add the fourth pathway accent

The three existing pathways each own an accent. A fourth needs its own, distinct
from the developer blue, the governance ember and the deliberately colourless
institutional slate. A desaturated teal reads as separate from all three and
carries the right tone for an arrival surface.

In `app/globals.css`, add to the pathway accent block inside `:root`:

```css
--start: 168 58% 30%;
```

and to the `.dark` block:

```css
--start: 166 48% 55%;
```

In `tailwind.config.ts`, add to `theme.extend.colors` alongside `dev` and
`inst`:

```ts
start: "hsl(var(--start) / <alpha-value>)",
```

### 1.2 Register the section in navigation

`components/nav-data.ts` drives the mega menu, the mobile sheet and the command
menu from one array. Widen the `NavSection` key union to include `"start"` and
add a fourth entry to `navSections`. Place it first, since it is the entry point
for people who know least.

Its links are the four routes in 1.3, with `accent: "text-start"`.

`app/page.tsx` holds a separate `pathways` array for the home page cards. Add a
matching fourth entry there.

### 1.3 Build the routes

| Route | Deliverable | Source | Status |
|---|---|---|---|
| `/start` | Start Here Onboarding Hub | Task 17, poundeater | Awaiting transfer |
| `/start/buy` | Where to Buy and Trade RBNT | Task 20, poundeater | Awaiting transfer |
| `/start/staking` | Staking and Voting Power Explainer | Task 19, Olive | In hand |
| `/start/recovery` | Cross-Chain Recovery Playbook | Task 16, Cipher | In hand |

Task 17 ships on its own component kit, so port its content and its chips rather
than deploying it separately. Each chip deep-links to one of the three pages
below it.

Two notes on the individual pages. The recovery playbook is what moderators will
link under pressure, so keep its URL short and put the exchange deposit warning
above the fold. The buy and trade reference needs a named owner and a review
date, because exchange listings change and a stale answer there is worse than no
answer. Link the staking explainer from `/dao/proposals` as well, since voting
power is what a reader of that page is trying to understand.

---

# Step 2. Extend the Developers section

Five deliverables land here. Four are merged as documentation. The four
reference contracts are indexed rather than absorbed.

| Route | Deliverable | Treatment |
|---|---|---|
| `/developers/troubleshooting` | Task 9, Developer Troubleshooting Wiki | Merged |
| `/developers/eligibility-sdk` | Task 14, EligibilitySDK Integration Guide | Merged |
| `/developers/bridge` | Task 8, Bridge Integration Guide | Merged |
| `/developers/tutorials` | Task 13, Zero-to-Hero Onboarding Kit | Merged |
| `/developers/contracts` | Tasks 1, 2, 3 and 4 | Index page, repositories linked |

The troubleshooting wiki is the cleanest port available. Its repository is a
folder of markdown and nothing else, so it moves with no rework and is the right
place to start.

The eligibility guide is worth more than its route suggests. The official
quickstart it replaces currently returns 404, which every developer attempting a
task on this chain runs into. Send it to the core team at the same time as
publishing it.

Merge the bridge guide including its nine documented failure scenarios. The
relayer, the heartbeat monitor and the bridge interface stay in their own
repository.

Task 13 is a five module developer course covering environment setup through
access control. It reads as onboarding but it is not general onboarding, which
is why it sits here rather than under Start Here.

The contracts index describes what each of the four reference implementations
solves and links to its repository and its demo. Each is a Hardhat project with
verified testnet deployments and its own interface. None of them belongs inside
this application.

---

# Step 3. Extend the DAO section

Six deliverables land here, and two of them close blockers already written into
`features/dao/content-spec.ts`.

| Route | Deliverable | Treatment |
|---|---|---|
| n/a | Task 15, DAO Website and Information Architecture | This repository |
| `/institutional#metrics` and the home stat row | Task 10, Network Public Dashboard | Linked, and read as a data source |
| `/dao/proposals` | Task 25, Proposal Evaluation Framework | Linked as a required pre-submission step |
| `/dao/changelog` | Task 23, DAO Structural Changes Changelog | Merged |
| `/dao/digest` | Task 24, Monthly DAO Digest | Merged as a growing index |
| `/dao/reports/rbnt-utility` | Task 11, RBNT Token Utility Report | Merged summary, full report as a download |

### The metrics blocker

`metricsSpec` in `content-spec.ts` states that the four headline figures have no
source of their own and must never be typed in by hand. Task 10 is that source.
It is a separate application that already reads chain 151 directly and ships
with a Dockerfile. Deploy it under DAO hosting and have `daoService.metrics()`
read from it rather than returning nulls.

### The proposals blocker

`tasksSpec` and `proposalsSpec` both note that a decision has to be made before
they can be wired. Task 25 is the pre-screening tool that decision was waiting
on. It is finished and deployed. What is missing is a named council member who
operates it and a checklist entry that makes running it mandatory before
anything reaches Snapshot. That is a governance action rather than an
engineering one.

### The changelog

Merge the changelog itself. Its repository also contains the generation engine,
which belongs as a scheduled job rather than a published page. Pair the launch
with a Head of Council agenda item to ratify or strike each drifted item.
Publishing it without that produces a fourth document disagreeing with the other
three.

### The digest

A single archived digest is worth very little. Build the route as an index that
takes one entry a month. The format is proven, so the useful move is to make it
recurring rather than to publish it once.

---

# Step 4. Institutional and brand

| Destination | Deliverable | Treatment |
|---|---|---|
| `/institutional#cases` | Task 21, RWA on Redbelly Mainnet | Merged as a case entry |
| `/institutional#acacia` | Task 7, Project Acacia Deep-Dive | Outbound link |
| `/brand` | Task 22, Meme and Social Asset Pack | Press kit with downloads |

The RWA piece documents a regulated credit fund that has been live on mainnet
since July and has never been announced. Its specific mainnet addresses need
sign-off before publication, and the piece loses value the longer it sits.

The Acacia analysis was published by its author on their own Substack, and that
publication is the canonical public version. Link to it rather than reproducing
it. The DAO holds an archived copy in the `submissions` repository.

The asset pack is ten images. Publish it as a press kit and hand it directly to
whoever runs the Discord and X accounts, which is what it was commissioned for.

---

# Conventions

**Credit.** Every merged page carries the contributor credited for it. The name
to use is the one recorded in that task's folder in the `submissions`
repository, and it should be consistent with the published cycle summary.

**Licensing.** No public licence has been granted over any deliverable yet. The
choice across the three proposed families has not been made. Do not add licence
headers to merged content until it has.

**Hosting.** Transferring a repository does not move its deployment. Every live
deliverable currently runs on a contributor's own Vercel account or personal
domain. Redeploy under DAO hosting before treating any original link as durable,
and before asking a contributor to take theirs down.

**Content location.** Merged deliverables are content, not fixtures. Keep them
out of `features/dao/service.ts`, which exists for data read from a live source.

## What this document does not cover

DNS for the new routes, organisation level access control, and the licence
decision. None of those is an engineering task and each sits with someone other
than whoever implements this.
