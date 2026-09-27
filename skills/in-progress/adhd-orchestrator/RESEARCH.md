# Why the rules look like this

Notes for whoever edits `SKILL.md`. Each rule is here because something supports it, and several are softer than the popular versions because something pushed back. Checked September 2026.

## Supported, kept

- **Small, concrete first steps and if-then plans.** Implementation intentions ("when X, I'll do Y") have a large meta-analytic base, and Gawrilow, Gollwitzer and Oettingen found if-then plans improved executive-function task performance in children with ADHD. Evidence in adults with ADHD is thinner, so the skill uses the mechanism (a named, physical first action) without claiming a clinical effect. The micro-step rule is the agent-sized version.
- **Time is hard to feel.** A meta-analysis across 55 studies (JAACAP, 2021) found medium-sized deficits in time reproduction and discrimination in ADHD. That supports externalizing time (numbers, real reminders). Note the adult literature is sparse and mixed, and "time blindness" is a popular label, not a finding.
- **Externalize working memory.** Barkley's "point of performance" principle: put the information where the work happens rather than relying on recall. Hence the state file and pickup.

## Contested, softened

- **"Max 1 to 3 choices."** Choice overload is real but conditional. Chernev et al.'s 2015 meta-analysis (99 observations) found it depends on moderators: complex, hard-to-compare options, difficult tasks, and uncertain preferences raise it; simple choices don't. An earlier meta-analysis (Scheibehenne et al., 2010) found a mean effect near zero. So the skill caps options and adds a recommendation (which removes most of the load), but lets the user ask for the full list.
- **Energy-based triage ("dopamine state").** No good evidence for dopamine-state task matching; the neuroscience framing is pop-science. Matching task size to self-reported energy is still a reasonable heuristic, so it's kept as a tiebreaker below deadlines and consequences, and asked only when not obvious.
- **Timeboxes and Pomodoro.** Little ADHD-specific evidence, and rigid timers can wreck a productive hyperfocus. Hence: offer, accept "no", and never interrupt mid-flow.
- **Body doubling.** Popular, early evidence only (a 2024 survey of 220 people; small VR and EEG studies in 2025). Not built in; an agent in a chat is a weak body double anyway.

## Risks the skill guards against

- **Cognitive offloading.** Studies (e.g. Gerlich, 2025, n=666) link heavy AI use to lower critical-thinking scores, with offloading as the mediator. Other work reports the most offloading on planning and sequencing tasks, which is exactly this skill's territory. These are mostly correlational, but it's the reason for "scaffold, don't substitute" and "the user decides".
- **Nagging and novelty decay.** ADHD tools often get abandoned once they feel like a supervisor. Hence one-time drift warnings, no repeated check-ins, and the profile to let the user switch rules off.
- **Fake timers.** A model can't feel time between messages; a promised "I'll ping you at 2:30" with no scheduler behind it is a lie that fails silently. Hence the real-reminder rule.
- **Health data leakage.** The profile file describes a medical condition, so it stays out of repos.

## Prior art checked

- [dsx0511/adhd-copilot](https://github.com/dsx0511/adhd-copilot): Claude Code plugin with a two-minute launch method, exit ramps, and an adaptive `adhd-profile-tracker`. Source of the exit-ramp and profile ideas.
- [ayghri/i-have-adhd](https://github.com/ayghri/i-have-adhd): output-shape rules (lead with the action, cap lists at 5, end on one next step, no preamble). Source of most of **Output shape**.
- [Chudi Nnorukam's posts](https://chudi.dev/blog/claude-code-skills-adhd-developers): often cited for `/adhd-task-triage`, `/pickup`, and `/schedule`, but the author says `pickup` never got past a design doc and none of the three are in use. Treat those as ideas, not tested tools.

## Sources

- Chernev, Böckenholt, Goodman (2015), [Choice overload: a conceptual review and meta-analysis](https://www.sciencedirect.com/science/article/abs/pii/S1057740814000916)
- [Meta-analysis: altered perceptual timing abilities in ADHD](https://www.jaacap.org/article/S0890-8567(21)02045-1/fulltext), JAACAP
- [Time perception in adult ADHD: findings from a decade](https://pmc.ncbi.nlm.nih.gov/articles/PMC9962130/)
- [Chudi Nnorukam on which ADHD skills they actually run](https://chudi.dev/blog/claude-code-adhd-skills-install)
- Toli et al. (2016), [Implementation intentions in people with mental health problems](https://bpspsychub.onlinelibrary.wiley.com/doi/10.1111/bjc.12086)
- [Implementation intentions in children: systematic review and meta-analysis](https://bpspsychub.onlinelibrary.wiley.com/doi/10.1111/bjop.70065)
- [You Are Not Alone: body doubling for ADHD in VR](https://arxiv.org/pdf/2509.12153)
- [AI-overdependence and human cognitive decline](https://www.sciencedirect.com/science/article/pii/S2451958826001764)
