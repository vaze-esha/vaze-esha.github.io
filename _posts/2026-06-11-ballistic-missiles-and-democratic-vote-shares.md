---
layout: post
title: "Ballistic Missiles and Democratic Vote Shares"
date: 2026-06-11
mathjax: true
---

A while back, I was asked to find an exogenous predictor of popular support for Kennedy's Moonshot program.[^1]
Specifically, we wanted to isolate the causal effect of public R&D funding on voting outcomes (both Gallup and federal election votes in favour of the Democrats) at the county level. We'll look at a very simple regression:

$$
\text{DemVotePct}_{c, t} = \beta \, \text{RDFunds}_{c, t} + \varepsilon_{c, t}
$$

The outcome is county-level vote shares in favour of the Democrats, or Gallup survey questions that ask about approval for the Moonshot program specifically. Naturally, the regression above is far from causality, which necessitates a clean source of exogenous variation in vote share.

The following notes are from an internal memo which uses ballistic missiles as an instrument for public R&D funding. This line of investigation has now been put on hold (in favour of qualitative research), so this work is doomed to live on my website until I have time to pick it back up (highly unlikely). The bulk of this document is unchanged from when it was first circulated to my team. I've taken the liberty of adding some more context here for the unfamiliar reader.

---

## Cold War Anxiety

**From Roger D. Launius, "Public Opinion Polls and Perceptions of US Human Spaceflight":**[^launius] Consistently throughout the 1960s a majority of Americans did not believe Apollo was worth the cost, with the one exception being a poll taken at the time of the Apollo 11 lunar landing in July 1969. Consistently throughout the decade, 45–60 percent of Americans believed that the government was spending too much on space, indicative of a lack of commitment to the spaceflight agenda.

*The decision to proceed with Apollo was not made because it was enormously popular with the public, despite general acquiescence, but for hard-edged political reasons. Most of these were related to the Cold War crises of the early 1960s, in which spaceflight served as a surrogate for face-to-face military confrontation.*

**From Kantor and Whalley:**[^kw] Modern commentators contend that space-race research had particularly high returns because NASA's organization was highly effective at research coordination and the intrinsic geopolitical motivation encouraged scientists to exert high levels of effort (Mazzucato 2021).[^mazzucato] Those advocating for significant government spending to jumpstart innovation and economic growth often call for a new "Sputnik Moment," harkening back to a time when the US devoted significant resources racing the Soviet Union to the Moon (Gruber and Johnson 2019).[^gj]

If we are inclined to believe the above evidence, then America's aspirations for the Moon were a direct consequence of its rivalry with the Soviets. The degree of threat posed by the USSR's stellar scientific achievements pushed the US to try and gather some of its own. Then, if our outcome is support for the Moon landing, an exogenous source of variation for support at the county level could be exposure to the Cold War.

Assume that the driving force behind federal pursuit of the Moonshot is US hegemony over the USSR. We then extend this assumption to the general public—counties with more exposure to the Cold War (perceived "Soviet threat") are more likely to support the Moonshot.

---

## How do we measure exposure to the Cold War?

Exposure could reasonably be measured using media (newspapers, television), but subscription to media and broadcast availability are both endogenous. Missiles are a convenient way to capture this, because citizens did not choose missile locations, and it is highly unlikely that they moved to places where they were more likely to be closer to a missile.[^2]

Thankfully, due to the sheer abundance of Cold War era military history efforts, I can measure distance to missiles by using geo-coded missile silo locations. The argument here is that geographically closer missiles are likely to strike fear (or at the very least, awareness), which in turn manifests as support for anti-Soviet measures like the Moon landing.

For each county in the continental United States, I compute the distance to the nearest [Strategic Air Command (SAC)](https://www.nicap.org/ncp/sac.htm) base (closer counties faced higher perceived first-strike risk and had more defense-oriented populations, which would translate to higher support for all national endeavours intended to beat the Soviets), distance to the nearest [Intercontinental Ballistic Missile (ICBM) silo](https://alcpress.org/military/icbm/history.html), and distance to the nearest [Nike missile](https://ed-thelen.org/loc.html). I compute the centroid distance of a county from ICBM silos, or silo density in the state. This is plausibly exogenous, because silo location is almost certainly orthogonal to political support for the Moonshot program, or the Democratic administration. 

I scrape missile locations from the linked sources above, and merge the county-level centroid distance measures with [county-level election data](https://uselectionatlas.org/). 

**Table 1: Cold War Defense Infrastructure Layers**

| Layer | # of Sites | Geographic Distribution | Salience to Public |
|---|---|---|---|
| ICBM silos | ~1,200 | Rural Great Plains | High (nuclear strike fear) |
| Nike batteries | ~265 | Urban/suburban rings | Very high (visible, near homes) |
| SAC bases | ~50 | Dispersed, rural | Moderate |

**From "The Military Standard":**[^milstd] The missile program also brought the Cold War home to many Americans. To farmers in the Great Plains, the Cold War suddenly came to life when the Air Force built Minuteman silos among their wheat fields. The Army's Nike missile sites provided an even more striking reminder: many of these batteries were located near the most densely populated areas in the nation, and they provided graphic testimony to the severity of the conflict between the United States and Soviet Union.

There were important economic implications as well. The missile program brought sudden prosperity to sleepy towns like White Sands, New Mexico, and Huntsville, Alabama. Across the nation, tens of thousands of Americans found work building the complex missiles and huge launch facilities that would house the new weapons.

Again, **from Roger D. Launius:** The following year Newsweek echoed the Times story, stating: "The US space program is in decline. The Vietnam War and the desperate conditions of the nation's poor and its cities—which make space flight seem, in comparison, like an embarrassing national self-indulgence—have combined to drag down a program where the sky was no longer the limit."

---

## Testing the Reduced Form

Now that we've set the stage for why missiles might help eliminate endogeneity, I run the following reduced-form regression to test the channel:

$$
\text{Proximity to Missiles} \longrightarrow \text{Perceived Soviet Threat} \longrightarrow \text{Moonshot Support}
$$

Eventually, we want to instrument R&D and military expenditure with missile distance measures. Here, I simply test if distance to missiles is a predictor of vote shares in favour of the Democrats. I split the sample of Nike missiles and ICBM missiles, because the former are urban and the latter are rural, in an attempt to speak to a rudimentary heterogeneity cut.

$$
\text{DemVotePct}_{c,t} = \beta_1 \, \text{CentDist\_ICBM}_{c} + \gamma_s + \varepsilon_{c,t}
$$

To belabour my point, counties that are closer to missile silos have a "higher perceived threat" of the Cold War by virtue of proximity to the military-industrial complex. My prior is that proximity to missiles implies stronger support for the Moonshot program, and the mechanism is salience. Having a missile in your backyard makes the Cold War a more tangible threat, inspiring support for the Moon landing, which was marketed as a nationally coordinated effort to beat Russia on a new frontier. To empirically test this, I use centroid distance to the nearest missile to predict vote shares. 

We'll look at presidential and congressional elections separately, and regress vote shares for Democrats, Republicans, and Other candidates on centroid distance to the nearest missile. For brevity, the tables below look at a cross section, and focus on the 1964 presidential elections. 

---

### Distance to ICBM Silos

**Table 2: Presidential Elections — Distance to Nearest ICBM and Vote Share**

| | Democrat | Republican | Other |
|---|---|---|---|
| Std. dist. to nearest missile | -1.510\*\* | 1.957\*\*\* | -0.527\*\* |
| | (0.616) | (0.624) | (0.230) |
| Observations | 2,028 | 2,028 | 2,028 |
| R² | 0.768 | 0.638 | 0.965 |

*Notes:* Standard errors clustered at the county level in parentheses. State fixed effects included. \*p < 0.10, \*\*p < 0.05, \*\*\*p < 0.01.

**Presidential elections:** A **1 SD increase in distance to the nearest missile site** is associated with a **1.5 percentage point decrease** in Democrat vote share, a **2.0 percentage point increase** in Republican vote share, and a **0.5 percentage point decrease** in Other vote share. All of these estimates are statistically significant.

*Estimates are as hypothesized. Being farther away from a missile makes you less likely to vote for the Democrats, who are ostensibly running the show with anti-Soviet measures under Lyndon B. Johnson.*

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

---

> **Note:** The key idea here is that missile placement is quasi-random to electoral politics (silo establishment is likely uncorrelated with local politics, and historical documents show that missile location choice has little to do with political lobbying).

---

### Distance to Nike Missiles

**Table 4: Presidential Elections — Distance to Nearest Nike Missile and Vote Share**

| | Democrat | Republican | Other |
|---|---|---|---|
| Std. dist. to nearest missile | 0.749\*\* | -0.674\* | -0.143 |
| | (0.346) | (0.350) | (0.092) |
| Observations | 2,028 | 2,028 | 2,028 |
| R² | 0.768 | 0.637 | 0.964 |

*Notes:* Standard errors clustered at the county level in parentheses. State fixed effects included. \*p < 0.10, \*\*p < 0.05, \*\*\*p < 0.01.

**Presidential elections:** A **1 SD increase in distance to the nearest Nike missile site** is associated with a **0.7 percentage point increase** in Democrat vote share, a **0.7 percentage point decrease** in Republican vote share, and a **0.1 percentage point decrease** in Other vote share. Democrat and Republican estimates are statistically significant.

---

> **Note:** The opposite direction of effects for Nike missiles (compared to ICBMs) suggests that urban–rural heterogeneity is worth considering (even for downstream analyses with survey data).

---

**Table 5: Congressional Elections — Distance to Nearest Nike Missile and Vote Share**

| | Democrat | Republican | Other |
|---|---|---|---|
| Std. dist. to nearest missile | -8.983\*\*\* | -3.780 | -6.674\*\*\* |
| | (2.398) | (2.439) | (2.498) |
| Observations | 2,028 | 2,028 | 2,028 |
| R² | 0.751 | 0.751 | 0.753 |

*Notes:* Standard errors clustered at the county level in parentheses. State fixed effects included. \*p < 0.10, \*\*p < 0.05, \*\*\*p < 0.01.

**Congressional elections:** A **1 SD increase in distance to the nearest Nike missile site** is associated with a **9.0 percentage point decrease** in Democrat vote share, a **3.8 percentage point decrease** in Republican vote share, and a **6.7 percentage point decrease** in Other vote share. Democrat and Other estimates are statistically significant.

I split the estimates by missile type because ICBMs are rural, and Nike missiles are urban. Ideally, we would want to isolate the effect of rural/urban partisanship from distance to missiles. The clean way to do this is to control for urban areas, but for a preliminary test, splitting the sample works fine.

If the results were purely driven by urbanization, we would expect the ICBM and Nike estimates to point in the same direction—both would simply be recovering the well-known urban/rural partisan divide. Instead, the two missile types produce opposite-signed coefficients. In a simple cross-sectional regression without fixed effects, distance to the nearest ICBM is associated with higher Republican vote share, while distance to the nearest Nike site is associated with lower Republican vote share. 

This sign reversal is what we would expect given the geographic placement of each system: ICBMs were sited in remote rural areas of the Great Plains and Mountain West, while Nike batteries were deliberately placed in rings around major metropolitan centers. Being far from an ICBM means being far from rural missile infrastructure, and being far from a Nike site means being far from a city. The two distance measures point in opposite geographic directions, and the fact that their partisan gradients mirror this is reassuring. This suggests that the estimates are not simply collapsing into a single urban/rural heterogeneity story. The ICBM results, operating within the rural stratum, provide the cleaner test of the missile proximity channel, while the Nike results are difficult to separate from the urbanization effect and should be interpreted with caution.

---

> **Note:** This page will be updated when new results materialize. E-mail me with ideas (or glaring errors), and visit [this guy's](https://www.themilitarystandard.com/) website.

---

[^1]: The Moonshot program refers to President John F. Kennedy's 1961 challenge to land a man on the Moon and return him safely to the Earth.
[^2]: This is possible for a small share of military workers who were employed in missile silo facilities for maintenance. In later stages of the project, the share of individuals in a county employed by the military is an easy control to throw in.
[^launius]: Roger D. Launius, ["Public Opinion Polls and Perceptions of US Human Spaceflight"](https://doi.org/10.1016/S0265-9646%2803%2900039-0), *Space Policy* 19, no. 3 (2003): 163–175.
[^kw]: Shawn Kantor and Alexander T. Whalley, ["Moonshot: Public R&D and Growth"](https://www.nber.org/papers/w31471), NBER Working Paper No. 31471 (2023).
[^mazzucato]: Mariana Mazzucato, [*Mission Economy: A Moonshot Guide to Changing Capitalism*](https://marianamazzucato.com/books/mission-economy) (Allen Lane, 2021).
[^gj]: Jonathan Gruber and Simon Johnson, [*Jump-Starting America: How Breakthrough Science Can Revive Economic Growth and the American Dream*](https://www.jump-startingamerica.com/) (PublicAffairs, 2019).
[^milstd]: [The Military Standard](https://www.themilitarystandard.com/), an archival reference site documenting US Cold War missile programs.
