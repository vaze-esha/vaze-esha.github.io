---
layout: post
title: "Ballistic Missiles and Democratic Vote Shares"
date: 2026-06-11
mathjax: true
---

A while back, I was asked to find an exogenous predictor of popular support for Kennedy's Moonshot program.[^1]
Specifically, we wanted to isolate the causal effect of public R&D funding on voting outcomes (both Gallup and federal election votes in favour of the Democrats) at the county level. We'll look at a very simple regression:

$$
\text{DemVotePct}_{c, t} = \beta \, \text{R\&DFunds}_{c, t} + \varepsilon_{c, t}
$$

The outcome is county-level vote shares in favour of the Democrats, or Gallup survey questions that ask about approval for the Moonshot program specifically. Naturally, the regression above is far from causality, which necessitates a clean source of exogenous variation in vote share.

The following notes are from an internal memo which uses ballistic missiles as an instrument for public R&D funding. This line of investigation has now been put on hold (in favour of qualitative research), so this work is doomed to live on my website until I have time to pick it back up (highly unlikely). The bulk of this document is unchanged from when it was first circulated to my team. I've taken the liberty of adding some more context here for the unfamiliar reader.

---

## Cold War Anxiety

> **From Roger D. Launius, "Public Opinion Polls and Perceptions of US Human Spaceflight":** Consistently throughout the 1960s a majority of Americans did not believe Apollo was worth the cost, with the one exception being a poll taken at the time of the Apollo 11 lunar landing in July 1969. Consistently throughout the decade, 45–60 percent of Americans believed that the government was spending too much on space, indicative of a lack of commitment to the spaceflight agenda.
>
> *The decision to proceed with Apollo was not made because it was enormously popular with the public, despite general acquiescence, but for hard-edged political reasons. Most of these were related to the Cold War crises of the early 1960s, in which spaceflight served as a surrogate for face-to-face military confrontation.*

> **From Kantor and Whalley:** Modern commentators contend that space-race research had particularly high returns because NASA's organization was highly effective at research coordination and the intrinsic geopolitical motivation encouraged scientists to exert high levels of effort (Mazzucato 2021). Those advocating for significant government spending to jumpstart innovation and economic growth often call for a new "Sputnik Moment," harkening back to a time when the US devoted significant resources racing the Soviet Union to the Moon (Gruber and Johnson 2019).

If we are inclined to believe the above evidence, then America's aspirations for the Moon were a direct consequence of its rivalry with the Soviets. The degree of threat posed by the USSR's stellar scientific achievements pushed the US to try and gather some of its own. Then, if our outcome is support for the Moon landing, an exogenous source of variation for support at the county level could be exposure to the Cold War.

Assume that the driving force behind federal pursuit of the Moonshot is US hegemony over the USSR. We then extend this assumption to the general public—counties with more exposure to the Cold War (perceived "Soviet threat") are more likely to support the Moonshot.

**How do we measure exposure to the Cold War?**

1. Distance to the nearest [Strategic Air Command (SAC)](https://www.nicap.org/ncp/sac.htm) base. Closer counties faced higher perceived first-strike risk and had more defense-oriented populations, which would translate to higher support for all national endeavours intended to beat the Soviets.
2. Similarly, [ICBM silos](https://alcpress.org/military/icbm/history.html). We compute the centroid distance of a county from ICBM silos, or silo density in the state. This is plausible, because silo location is almost certainly orthogonal to political support for the Moonshot program, or the Democratic administration.
3. [Nike missiles](https://ed-thelen.org/loc.html) (similar to SAC/ICBM).

**Table 1: Cold War Defense Infrastructure Layers**

| Layer | # of Sites | Geographic Distribution | Salience to Public |
|---|---|---|---|
| ICBM silos | ~1,200 | Rural Great Plains | High (nuclear strike fear) |
| Nike batteries | ~265 | Urban/suburban rings | Very high (visible, near homes) |
| SAC bases | ~50 | Dispersed, rural | Moderate |

> **From "The Military Standard":** The missile program also brought the Cold War home to many Americans. To farmers in the Great Plains, the Cold War suddenly came to life when the Air Force built Minuteman silos among their wheat fields. The Army's Nike missile sites provided an even more striking reminder: many of these batteries were located near the most densely populated areas in the nation, and they provided graphic testimony to the severity of the conflict between the United States and Soviet Union.
>
> There were important economic implications as well. The missile program brought sudden prosperity to sleepy towns like White Sands, New Mexico, and Huntsville, Alabama. Across the nation, tens of thousands of Americans found work building the complex missiles and huge launch facilities that would house the new weapons.

**Data:**

1. Scrape missile location data (county level)
2. Download [county-level election data](https://uselectionatlas.org/)
3. Merge (1) & (2) with Kantor and Whalley NASA contractor spending data (public R&D data)

Again, **from Roger D. Launius:**

> The following year Newsweek echoed the Times story, stating: "The US space program is in decline. The Vietnam War and the desperate conditions of the nation's poor and its cities—which make space flight seem, in comparison, like an embarrassing national self-indulgence—have combined to drag down a program where the sky was no longer the limit."

## Testing the Reduced Form

We'll run the following reduced-form regressions to test the channel:

$$
\text{Proximity to Missiles} \longrightarrow \text{Perceived Soviet Threat} \longrightarrow \text{Moonshot Support}
$$

Instead of using a 2SLS design, I simply test if distance to missiles is a predictor of vote shares in favour of the Democrats.

I split Nike missiles and ICBM missiles, because the former are urban and the latter are rural.

$$
\text{DemVotePct}_{c,t} = \beta_1 \, \text{CentDist\_ICBM}_{c} + \gamma_s + \varepsilon_{c,t}
$$

Counties that are closer to missile silos have a "higher perceived threat" of the Cold War by virtue of proximity to the military-industrial complex. My prior is that proximity to missiles implies stronger support for the Moonshot program, and the mechanism is salience. Having a missile in your backyard makes the Cold War a more tangible threat, inspiring support for the Moon landing, which was marketed as a nationally coordinated effort to beat Russia on a new frontier.

To empirically test this, I use centroid distance to the nearest missile to predict vote shares. This is the reduced form. Eventually, we would like to use the distance to missile as an instrument for Public R&D and military spending. 

### ICBM Distance

**Table 2: Presidential Elections — Distance to Nearest ICBM and Vote Share**

| | Democrat | Republican | Other |
|---|---|---|---|
| Std. dist. to nearest missile | -1.510\*\* | 1.957\*\*\* | -0.527\*\* |
| | (0.616) | (0.624) | (0.230) |
| Observations | 2,028 | 2,028 | 2,028 |
| R² | 0.768 | 0.638 | 0.965 |

*Notes:* Standard errors clustered at the county level in parentheses. State fixed effects included. \*p < 0.10, \*\*p < 0.05, \*\*\*p < 0.01.

**Presidential elections:** A **1 SD increase in distance to the nearest missile site** is associated with a **1.5 percentage point decrease** in Democrat vote share, a **2.0 percentage point increase** in Republican vote share, and a **0.5 percentage point decrease** in Other vote share. All of these estimates are statistically significant.

**Table 3: Congressional Elections — Distance to Nearest ICBM and Vote Share**

| | Democrat | Republican | Other |
|---|---|---|---|
| Std. dist. to nearest missile | -3.187 | 9.776\*\*\* | 1.756 |
| | (2.194) | (2.220) | (2.100) |
| Observations | 2,028 | 2,028 | 2,028 |
| R² | 0.749 | 0.752 | 0.752 |

*Notes:* Standard errors clustered at the county level in parentheses. State fixed effects included. \*p < 0.10, \*\*p < 0.05, \*\*\*p < 0.01.

**Congressional elections:** A **1 SD increase in distance to the nearest missile site** is associated with a **3.2 percentage point decrease** in Democrat vote share, a **9.8 percentage point increase** in Republican vote share, and a **1.8 percentage point increase** in Other vote share. Republican estimates are statistically significant.

Counties that are farther away from a missile show reduced support for Democrats, and increased support for Republicans. They also show reduced support for other candidates, and estimates for Republican votes are statistically significant.

> **Note:** The key idea here is that missile placement is quasi-random to electoral politics (silo establishment is likely uncorrelated with local politics, and historical documents show that missile location choice has little to do with political lobbying).
>
> I split the estimates by missile type because ICBMs are rural, and Nike missiles are urban. This tests if the hypothesized channel has heterogeneous effects by urbanization, or if our results are just picking up an urban/rural divide in partisanship.

### Nike Missile Distance

**Table 4: Presidential Elections — Distance to Nearest Nike Missile and Vote Share**

| | Democrat | Republican | Other |
|---|---|---|---|
| Std. dist. to nearest missile | 0.749\*\* | -0.674\* | -0.143 |
| | (0.346) | (0.350) | (0.092) |
| Observations | 2,028 | 2,028 | 2,028 |
| R² | 0.768 | 0.637 | 0.964 |

*Notes:* Standard errors clustered at the county level in parentheses. State fixed effects included. \*p < 0.10, \*\*p < 0.05, \*\*\*p < 0.01.

**Presidential elections:** A **1 SD increase in distance to the nearest Nike missile site** is associated with a **0.7 percentage point increase** in Democrat vote share, a **0.7 percentage point decrease** in Republican vote share, and a **0.1 percentage point decrease** in Other vote share. Democrat and Republican estimates are statistically significant.

> **Note:** The opposite direction of effects for Nike missiles (compared to ICBMs) suggests that urban–rural heterogeneity is worth considering (even for downstream analyses with survey data).

**Table 5: Congressional Elections — Distance to Nearest Nike Missile and Vote Share**

| | Democrat | Republican | Other |
|---|---|---|---|
| Std. dist. to nearest missile | -8.983\*\*\* | -3.780 | -6.674\*\*\* |
| | (2.398) | (2.439) | (2.498) |
| Observations | 2,028 | 2,028 | 2,028 |
| R² | 0.751 | 0.751 | 0.753 |

*Notes:* Standard errors clustered at the county level in parentheses. State fixed effects included. \*p < 0.10, \*\*p < 0.05, \*\*\*p < 0.01.

**Congressional elections:** A **1 SD increase in distance to the nearest Nike missile site** is associated with a **9.0 percentage point decrease** in Democrat vote share, a **3.8 percentage point decrease** in Republican vote share, and a **6.7 percentage point decrease** in Other vote share. Democrat and Other estimates are statistically significant.

## Conclusion

The reduced form behaves roughly as the salience story predicts: counties closer to ICBM silos voted more Democratic in presidential elections, consistent with proximity to the military-industrial complex making the Cold War—and the administration's response to it—a more tangible concern. The Nike results complicate the picture. In presidential elections the sign flips, which suggests that some of what we are picking up is an urban/rural divide in partisanship rather than the perceived-threat channel alone. Any serious version of this design would need to confront that heterogeneity directly.

Still, the ingredients for an instrument are here. Missile placement is plausibly quasi-random with respect to local electoral politics, distance to the nearest site predicts vote shares, and the proposed mechanism (salience of the Soviet threat) is consistent with the historical record. The natural next step is the 2SLS design sketched above: use distance to missiles as an instrument for public R&D and military spending, with Gallup approval of the Moonshot program as the outcome. That step is on hold, along with the rest of this project—but if the reduced form is any indication, it would be worth taking.

[^1]: The Moonshot program refers to President John F. Kennedy's 1961 challenge to land a man on the Moon and return him safely to the Earth.
