For the main content, see `analyze-wsb`, either as a html file or as a ipynb notebook.

`collect-wsb.ipynb` includes the code used to collect data. 
Currently, the data is not saved to this repo as it is rather large, although we're happy to try to share if there's interest.

`sampling_accuracy.ipynb` has early stage comparisons of parameter estimates from "time window samples" to actual population parameters
(calculated using this [full set of posts](https://old.reddit.com/r/datasets/comments/lfbddy/all_available_posts_and_comments_from/)).


Below is the first markdown cell from `analyze-wsb`.

# Can Online Platforms Benefit from Hosting Collective Action? Estimating the Amount of Reddit Activity Attributable to r/wallstreetbets in January 2021
## Nicholas Vincent and Hanlin Li, Northwestern University
Last updated Feb 8, 2020

## Preface and TLDR
*Preface/caveat: This is a rough draft/work-in-progress notebook, intended as an early
contribution to discussions around r/wallstreetbets GameStop short squeeze and resulting implications
for social computing. We are open to all sorts of critiques and suggestions
regarding the framing, assumptions, and methods choices. The first author takes
all responsibility for errors in code or claims*.

*Summary/TLDR: using a sample of reddit submissions (sampled by collecting all submissions for a
random set of one minute time windows), we estimate that r/wallstreetbets was responsible for
1.63-1.72% of all Reddit posts, 0.74-1.21% of all comments, and 0.42-1.17% of
total "post scores" in January 2021,
driven by the surge of activity at the end of the month. 
These estimates suggest that platforms like reddit may have a
powerful incentive to support future instances of "collective action" that are similar to
the "r/wallstreetbets short squeeze".*


## Motivation
The beginning of the [2021 GameStop short squeeze](https://en.wikipedia.org/wiki/GameStop_short_squeeze)
has been attributed to users of the subreddit r/wallstreetbets. 
For brevity, we'll refer to this (ongoing) event as the "WSB squeeze".
While it is a challenging task to prove exactly how responsible subreddit members were for ensuing market movement,
media coverage suggests that (1) the subreddit had a major influence on the WSB short squeeze and
(2) the subreddit recevied massive activity during this period.

Several articles in the media suggest measurable changes in reddit activity. 
Morse, writing for Mashable, reported that wallstreetbets was responsible for
record breaking
[massive traffic](https://mashable.com/article/reddit-wallstreetbets-subreddit-record-traffic-gamestop/)
(73M views in 24 hours).
Ghost, writing for Business Insider, summarized evidence from
[FrontPage Metrics](https://frontpagemetrics.com/) regarding
r/wallstreetbet's
[massive gain in userbase](https://www.businessinsider.com/wallstreetbets-fastest-growing-subreddit-hits-58-million-users-2021-1).
Several round-up posts [(example)](https://old.reddit.com/r/theoryofwsb/comments/l8uom9/press_roundup_through_30_january_2021/)
on old.reddit.com/r/theoryofwsb highlight the enormous amount of media attention the WSB squeeze received.

Building on the massive interest in the WSB squeeze, in this notebook, we will attempt to use a sample of reddit submissions to estimate the total
number of posts, comments, and upvotes attributable to r/wallstreetbets in January 2021.


## Contributions
Our intended primary contribution is to estimate, in a fast and easy-to-replicate manner,
reddit the amount of activity from a a particular subreddit relative to all reddit activity.
Specifically, we ask "How many posts, comments, and upvotes did r/wallstreetbets users contribute in Jan 2021", and
how do these figures compare to overall levels of reddit activity?

We argue that these estimates provide insight into how much online platforms like
reddit stand to benefit from being the "host" of attention grabbing collective efforts like
the WSB squeeze.
[reddit itself](https://redditblog.com/2020/12/08/reddits-2020-year-in-review/) uses submissions and comments
as measurements of overall activity,
and it seems likely that these activity measurements correlate with ad revenue (though
as we will discuss below, unusual events like the short squeeze may challenge this assumption).

That is, we are motivated the idea that regardless of the nth-order effects of
an instance of collective action, platforms stand to benefit from any overall
increase in engagement, and measuring this engagement. This motivation
draws on work
that seeks to make invisible labor of data generation visible
(see e.g. [data feminism](https://data-feminism.mitpress.mit.edu/) and [data leverage](https://arxiv.org/abs/2012.09995)).

Our secondary contribution is to explore the use of "time window" sampling of reddit submissions.
Specfically, because of the large amount of activity on Reddit
(e.g. around 30M submissions per month in 2020),
it can be challenging to *quickly* measure something like
the number of total posts on reddit during a period of heavy activity.
While pushshift.io provides complete data dumps for historical reddit data,
very recent data can only be accessed via API (which is understandably rate limited). 
Exactly measuring number of posts (or number of comments, or average post score),
requires downloading every reddit post via API calls, creating
a large time cost in terms of API calls (for both the researcher and the API provider, i.e. pushshift).
For instance, assuming an API call takes 1.1 second (1 second of sleep and 0.1 seconds of computation),
and each call retrieves 100 posts or comments (the current pushshift max),
in 86,400 seconds (a day), a researcher can retrieve only 7,864,500 items per day.
As we will see below, as of 2020, a month
of reddit data can have around 30M posts and even more comments.
Furthermore, capturing full data uses a large amount of disk space (which may be fine for well resourced researchers, but makes it harder
for interested parties to replicate).

All this goes to say, we believe there is value in exploring sampling techniques to answer these
kinds of questions.


## Time Window Sampling
We propose the following sampling approach:

1) divide January 2021 into one minute windows, starting at 00:00 UTC on Jan 1.

2) randomly select x% of these windows (we use 1% for our initial analyses)

3) collect *all* reddit posts within each selected window using the pushshift API. 
We then get each post's updated "score" (upvotes minus downvotes) and number of comments
via [PRAW](https://praw.readthedocs.io/en/latest/).

4) treat this 1% sample as a "simple random sample" to produce parameter estimates for the entire month of January

We are very interested in critiques of this approach! The exact sampling code is in `collect-wsb.ipynb`.

Note, Feb 8: we are currently working on (1) describing the approach more formally and
(2) assessing the accuracy of this approach. See the end of this cell and the `sampling_accuracy.ipynb` notebok
for more.

## Activity Measures

Returning to the main goals of this notebook, we aim to help answer the following broad question:

When a community organizes collective action similar to the r/wallstreetbets short squeeze,
*how much* does a platform like reddit benefit from the associated activities?
Should platforms like reddit implement design features specifically
to encourage such collective action, and if so what are the potential consequences?

Practically speaking, we focus on three measurements of reddit activity:

1) How many submissions were made to r/wallstreetbets in Jan 2021? 

2) What fraction of all reddit comments were posted to r/wallstreetbets in Jan 2021?

3) What fraction of "post score"
(which we argue is a correlate of pageviews, and thereby ad revenue) is attributable to WSB?

Looking at each of these estimates, we contextualize them using the submissions, comments, and post score from
other subreddits. In other words, we consider r/wallstreetbets activity relative to the rest of reddit.

Summary of answers (see below for the actual code!)

1) 535k submissions.
2) 0.74-1.21%
3) 0.42-1.17%

## How accurate is this sampling approach?
On Feb 8, kaggle user mattpodolak shared a dataset of [all r/wallstreetbets posts from Dec 6 2020 to Feb 6 2021](https://old.reddit.com/r/datasets/comments/lfbddy/all_available_posts_and_comments_from/).
This dataset provides an excellent opportunity to evaluate how closely our 1% time window sampling 
estimates match the actual population parameters *for r/wallstreetbets-specific estimates*
(we still need to collect every reddit post to calculate population parameters for all subreddits).
In the future, we hope to also look at how accuracy varies with sample size
(we expect there may be some challenges regarding the very long tail nature of reddit post scores, # comments, etc.)

For now, we've started looking into accuracy in a separate notebook,
`sampling_accuracy.ipynb`.

Summary of where this stands: while our approach seems fairly accurate for estimate the total number of
r/wallstreetbets posts, our sampling approach underestimates average post scores and number of comments.
