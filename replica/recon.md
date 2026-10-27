# Recon map: Breeze "Wi-Fi fix" prompt page (web)

Scope: the single landing page at breezedm.app/wifi/ that hands out a free
Claude Code prompt for fixing slow home Wi-Fi, plus the downloadable guide.
Not Breeze's main product (Instagram comment-to-DM automation).
For: to be confirmed. Proposed: a reusable "free prompt / lead magnet" page
template you can point at your own prompts and your own community or product.
Date: 2026-10-27

## Sources

| # | source | URL | notes |
| --- | --- | --- | --- |
| 1 | the page itself | https://breezedm.app/wifi/ | Portuguese, one long page, read once |
| 2 | the downloadable guide | https://breezedm.app/wifi/guia-wifi.md | the prompt as a Markdown file |
| 3 | parent product marketing site | https://breezedm.app/ | context only: who runs the page and why |
| 4 | Claude Code setup docs | https://docs.claude.com | linked from the page's FAQ |

No help center, pricing, changelog or store listing exists for this page: it is
a free giveaway. No account was used. No screenshots taken yet; drop them in
`replica/screens/` if you want `/replica-diff` layout scores later.

## Core loop

A visitor lands from social media, copies a ready-made agent prompt (or
downloads it, or sends it to their computer), and pastes it into Claude Code
with a photo of their router. The page's real job for its owner: build trust
and push visitors to a community / product.

## Screens

| ID | screen | route / how to reach | purpose | key components | states seen |
| --- | --- | --- | --- | --- | --- |
| S01 | Landing page, hero | `/wifi/` top | title, pixel-art illustration, primary "copy prompt" CTA | Hero, IllustrationBlock, CopyButton | filled |
| S02 | Landing page, "paste it" block | `/wifi/` | tells you to paste into Claude Code with a router photo; secondary actions | ActionRow (copy prompt, download .md, copy link, send to my computer) | filled |
| S03 | Landing page, "what it does" | `/wifi/` | feature list of what the agent will do | FeatureList | filled |
| S04 | Landing page, "how to use" | `/wifi/` | 3 numbered steps | StepList | filled |
| S05 | Landing page, requirements | `/wifi/` | Claude Code on a computer on the slow network; router admin password | RequirementList | filled |
| S06 | Landing page, FAQ | `/wifi/` | install, risk to router, ISP routers, password safety | Accordion | collapsed, expanded (assumed) |
| S07 | Landing page, credit + community | `/wifi/` bottom | credit to the original idea's author, community pre-launch CTA | CreditCard, CTA banner, Footer | filled |
| S08 | "Send to my computer" | button on S02 | get the prompt onto the desktop when browsing on a phone | Modal or share sheet (not opened; mechanism unknown) | guess |
| S09 | Copy confirmation | after any copy button | feedback that the clipboard has the prompt | Toast / button label swap | guess |
| S10 | Guide file | `/wifi/guia-wifi.md` | the prompt as a downloadable Markdown file | plain file | filled |

States not seen and worth building anyway: clipboard API blocked (fallback:
select-all text box), mobile layout (the page says the prompt is for desktop,
so the phone view should steer to "send to my computer").

## Flows

```
F01 Desktop visitor runs the prompt
    S01 -> copy prompt -> S09 -> (Claude Code, outside the page)
    happy path clicks: 1
    edge: clipboard blocked, visitor has no Claude Code (FAQ S06 -> docs link)

F02 Mobile visitor moves the prompt to their computer
    S01 -> S02 send to my computer -> S08 -> (open on desktop) -> F01
    happy path clicks: 2-3 (guess, S08 not opened)
    edge: no email / share target, link opened on another phone

F03 Visitor keeps the guide
    S02 download .md -> S10
    happy path clicks: 1

F04 Visitor shares the page
    S02 copy link -> S09
    happy path clicks: 1

F05 Visitor joins the community
    S07 community CTA -> external group invite
    happy path clicks: 1
```

## The prompt itself (the actual "product")

Studied for function only. Its structure, which a fresh prompt of your own
should match feature for feature, in your own words:

1. Goal and a definition of done: config backup to a local folder, speed test
   before and after, every change approved, a small helper to list devices
   and reboot, a final report with how to undo.
2. Access strategy, in order: official router API, open-source library, Home
   Assistant integration, the admin page's own web calls; hand the browser to
   the user for captchas or ISP logins; tell the user what to ask the ISP to
   unlock when settings are hidden.
3. Checks: speed and latency under load, then band, channel and neighbours,
   channel width, Wi-Fi mode, transmit power, band steering.
4. Safety: password goes in the OS keychain, never in chat; stop after one
   failed login; only Wi-Fi settings; show old vs new and wait for approval;
   re-read each value after writing; never change SSID or passwords, never
   reboot unasked, no firmware updates, no factory reset, no exploits.
5. Modes: "diagnose only"; and a fallback that fixes what it can on the
   computer and drafts a message for the ISP.
6. Output: what was fixed, how to undo it, what the user still needs to do.

## Components

| component | variants | states | used on |
| --- | --- | --- | --- |
| CopyButton | primary (hero), secondary (inline) | default, hover, focus, copied, failed | S01, S02 |
| ActionRow | 4 actions | default | S02 |
| Hero | with illustration | default | S01 |
| FeatureList | with bonus sub-list | default | S03 |
| StepList | numbered 1-3 | default | S04 |
| Accordion (FAQ) | single-open or multi (unknown) | collapsed, expanded | S06 |
| CreditCard | author + link to original video | default | S07 |
| CTA banner | external link | default, hover | S07 |
| Toast | success, error | shown, hidden | S09 |
| Modal / share sheet | send to computer | open, sending, sent, error | S08 |

## Inferred data model

```
Prompt      slug, title, body_markdown, language, credit_name, credit_url
            evidence: S01-S07 are all generated around one prompt; S10 is its file
            confidence: medium (could be a hand-written static page)

PromptEvent prompt_slug, kind (copy | download | copy_link | send), created_at
            evidence: none visible; a lead-magnet page usually tracks this
            confidence: guess

Handoff     prompt_slug, channel (email | whatsapp | link), target, created_at
            evidence: S08 "send to my computer" must deliver somewhere
            confidence: guess
```

Relationships: Prompt 1-n PromptEvent, Prompt 1-n Handoff.

## Feature matrix

See `features.csv`. Must: 7, should: 6, could: 4, skip: 4.

## Out of scope (cannot or should not be cloned)

- The prompt text, page copy, pixel-art illustration and branding: theirs.
  Write your own prompt to the structure above.
- The credit to the original author and their video: not yours to claim.
- The community / WhatsApp group and the Breeze product it funnels to.
- Breeze's main app (Instagram DM automation). A separate, much larger recon
  if that is what you actually want.

## Size

Screens 10 (one page, 7 sections, 2 overlays, 1 file), flows 5, entities 1-3.
Hard parts: "send to my computer" delivery (email or deep link), clipboard
fallbacks across browsers, and writing a router prompt that is genuinely safe
across many router models. Size: S (a weekend, mostly the prompt).
