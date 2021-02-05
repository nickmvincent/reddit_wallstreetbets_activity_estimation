# Can Online Platforms Benefit from Hosting Collective Action? Estimating the Amount of Reddit Activity Attributable to r/wallstreetbets in Jan 2021
## Nicholas Vincent and Hanlin Li, Northwestern University

First a caveat, and a TLDR!

*Preface/caveat: This is a rough draft work-in-progress notebook, intended as an early
contribution to discussions around r/wallstreetbets GameStop short squeeze and its implications
for social computing. We are open to all sorts of critiques and suggestions
regarding the framing, assumptions, and methods choices. The first author takes
all responsibility for errors in code or claims*.

*Summary/TLDR: using a sample of reddit submissions (sampled using random one minute time windows), we estimate
that r/wallstreetbets was responsible for 1.63-1.72% of all Reddit posts, 0.74-1.21% of all comments, and 0.42-1.17% of total "post scores" in January 2021,
primarily driven by the surge of activity at the end of the month. These estimates suggest
that platforms like reddit may have a
strong incentive to support future instances of "collective action" that are similar to
the "r/wallstreetbets short squeeze".*

The beginning of the [2021 GameStop short squeeze](https://en.wikipedia.org/wiki/GameStop_short_squeeze)
has been attributed to users of the subreddit r/wallstreetbets. For brevity, we'll refer to this (ongoing) event
as the "WSB squeeze".
While it's challenging to prove exactly how much responsibility to assign to the subreddit,
media coverage suggests that (1) the subreddit had a major influence on the WSB short squeeze and
(2) the subreddit recevied massive activity during this period.

Specifically, Morse, writing for Mashable, reported that wallstreetbets was responsible for
 recording breaking [massive traffic](https://mashable.com/article/reddit-wallstreetbets-subreddit-record-traffic-gamestop/) (73M views in 24 hours).
Ghost, writing for Business Insider, summarized evidence from [FrontPage Metrics](https://frontpagemetrics.com/) regarding
r/wallstreetbet's [massive gain in userbase](https://www.businessinsider.com/wallstreetbets-fastest-growing-subreddit-hits-58-million-users-2021-1).
A [post](https://old.reddit.com/r/theoryofwsb/comments/l8uom9/press_roundup_through_30_january_2021/) on r/theoryofwsb highlights the enormous amount of media attention.

Building on the massive interest in the WSB squeeze, in this notebook, we will attempt to use a sample of reddit submissions to estimate the total
number of posts, comments, and upvotes attributable to wallstreetbets in January 2021.

Our intended primary contribution is to provide an easy-to-replicate estimate of total Reddit activity (that does not require
downloading all reddit submissions for a time period). 
This estimate can provide insight into how much online platforms like
reddit stand to benefit from being the "host" of attention grabbing collective efforts like
the WSB squeeze . We note that [reddit itself](https://redditblog.com/2020/12/08/reddits-2020-year-in-review/) uses submissions and comments
as measurements of overall activity, and it seems likely that these activity measurements correlate with ad revenue (though
as we will discuss below, unusual events like the short squeeze may challenge this assumption).

Our secondary contribution is to explore the use of "time window" sampling for reddit. Specfically,
because of the large amount of activity on Reddit, it can be challenging to *quickly* measure something like
the number of total posts on a large, active subreddit such as r/wallstreetbet. While pushshift.io provides
data dumps for past reddit data, very recent data can only be accessed via API. Exactly measuring
the number of posts (or comments, or post score), would require downloading every reddit post, creating
a large time cost in terms of API calls (for both the researcher and the API provider, i.e. pushshift)
and the need to use a large amount of disk space (which may be fine for well resourced researchers, but makes it harder
for interested parties to replicate).

We propose the following sampling approach:

1) divide January 2021 into one minute windows

2) randomly select 1% of these windows

3) collect all reddit posts for each window using the pushshift API. Then get updated post scores via PRAW.

4) treat this 1% sample as a "simple random sample" to produce parameter estimates for the entire month of January

We are very interested in critiques of this approach! The exact sampling code is in `collect-wsb.ipynb`. Before collecting this
data, we experimented with comparing "time window sampled" estimates with full data calculations for individual days.

Returning to the main "research questions" of this notebook, we are motivated by the following broad question:

when a community organizes collective action similar to the r/wallstreetbets short squeeze,
does a platform like reddit benefit from the associated activities? Should platforms like reddit implement design features specifically
to encourage such collective action, and if so what are the potential consequences?

Practically speaking, we focus on three quantitative questions:

1) How many submissions were made to r/wallstreetbets in Jan 2021? 

2) How many comments were made to r/wallstreetbets in Jan 2021?

3) How much "post score" (which we argue is a correlate of pageviews, and thereby ad revenue) is attributable to WSB?

Looking at each of these estimates, we contextualize them using the submissions, comments, and post score from
other subreddits. In other words, we consider r/wallstreetbets activity relative to the rest of reddit.