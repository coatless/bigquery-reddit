Obtain Data
===========

Run on [Google BigQuery](https://console.cloud.google.com/bigquery).

    SELECT author, body
    FROM `fh-bigquery.reddit_comments.20*`
    WHERE subreddit = 'UIUC'
    AND REGEXP_CONTAINS(body, r'(?i)ask your advisor')

Save data as a CSV. The data used for this analysis can be found in the
[`data/`](data/) folder.

Post processing
===============

Once we have the data, let’s bring it into *R*.

    # Load in the Data
    comments = read.csv("data/results-20181114-075107.csv")

User Overview
-------------

Let’s quickly take a peak at the different contributors using this
phrase.

    # Figure out user counts
    count_users = table(comments$author)

    # Number of Unique Users
    n_users_unique = length(count_users)

    # Obtain a leaderboard of comments.
    top_users = sort(count_users, decreasing = TRUE)

    # Get name
    top_username = names(top_users)[1]
    top_user_posts =  top_users[1]

There were **348** of users who used a variation of the phrase “Ask your
advisor”. The user with the highest amount of comments was
**[/u/IDKAskYourAdvisor](https://reddit.com/u/IDKAskYourAdvisor)** who
had **30**.

All users with at least 4 posts containing the phrase are listed next in
a descending order.

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Username</th>
<th style="text-align: right;">Frequency</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">IDKAskYourAdvisor</td>
<td style="text-align: right;">30</td>
</tr>
<tr class="even">
<td style="text-align: left;">cleverdragon1</td>
<td style="text-align: right;">11</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Mobius118f</td>
<td style="text-align: right;">8</td>
</tr>
<tr class="even">
<td style="text-align: left;">pissblasta3</td>
<td style="text-align: right;">8</td>
</tr>
<tr class="odd">
<td style="text-align: left;">theillini19</td>
<td style="text-align: right;">8</td>
</tr>
<tr class="even">
<td style="text-align: left;">[deleted]</td>
<td style="text-align: right;">7</td>
</tr>
<tr class="odd">
<td style="text-align: left;">IlliniTy</td>
<td style="text-align: right;">6</td>
</tr>
<tr class="even">
<td style="text-align: left;">Mosquite_Leaf</td>
<td style="text-align: right;">6</td>
</tr>
<tr class="odd">
<td style="text-align: left;">CertainTackle</td>
<td style="text-align: right;">5</td>
</tr>
<tr class="even">
<td style="text-align: left;">DragonZaid</td>
<td style="text-align: right;">5</td>
</tr>
<tr class="odd">
<td style="text-align: left;">uiucrower</td>
<td style="text-align: right;">5</td>
</tr>
<tr class="even">
<td style="text-align: left;">csdude007</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">GenjoKodo</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">jeffgerickson</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">JRDSandstorm</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">mathuiuc</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Moi_Username</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">MrAcurite</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">ProgramTheWorld</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">schreiberbj</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">TheFearlessChuaEater</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">UIUCEngineering</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">WUTDO11231235</td>
<td style="text-align: right;">4</td>
</tr>
</tbody>
</table>

Amount of Words Used Per Post
-----------------------------

    # Figure out user counts
    library("dplyr")

    comments %>%
      group_by(author) %>%
      summarise(mean_nwords = mean(stringr::str_count(body, "\\S+")),
                n_entries = n()) %>%
      arrange(desc(n_entries), desc(mean_nwords)) %>%
      filter(n_entries >= 4) %>%
      knitr::kable()

<table>
<thead>
<tr class="header">
<th style="text-align: left;">author</th>
<th style="text-align: right;">mean_nwords</th>
<th style="text-align: right;">n_entries</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">IDKAskYourAdvisor</td>
<td style="text-align: right;">5.766667</td>
<td style="text-align: right;">30</td>
</tr>
<tr class="even">
<td style="text-align: left;">cleverdragon1</td>
<td style="text-align: right;">37.090909</td>
<td style="text-align: right;">11</td>
</tr>
<tr class="odd">
<td style="text-align: left;">theillini19</td>
<td style="text-align: right;">8.125000</td>
<td style="text-align: right;">8</td>
</tr>
<tr class="even">
<td style="text-align: left;">pissblasta3</td>
<td style="text-align: right;">5.500000</td>
<td style="text-align: right;">8</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Mobius118f</td>
<td style="text-align: right;">3.125000</td>
<td style="text-align: right;">8</td>
</tr>
<tr class="even">
<td style="text-align: left;">[deleted]</td>
<td style="text-align: right;">44.857143</td>
<td style="text-align: right;">7</td>
</tr>
<tr class="odd">
<td style="text-align: left;">IlliniTy</td>
<td style="text-align: right;">5.500000</td>
<td style="text-align: right;">6</td>
</tr>
<tr class="even">
<td style="text-align: left;">Mosquite_Leaf</td>
<td style="text-align: right;">3.166667</td>
<td style="text-align: right;">6</td>
</tr>
<tr class="odd">
<td style="text-align: left;">uiucrower</td>
<td style="text-align: right;">9.000000</td>
<td style="text-align: right;">5</td>
</tr>
<tr class="even">
<td style="text-align: left;">CertainTackle</td>
<td style="text-align: right;">3.600000</td>
<td style="text-align: right;">5</td>
</tr>
<tr class="odd">
<td style="text-align: left;">DragonZaid</td>
<td style="text-align: right;">3.000000</td>
<td style="text-align: right;">5</td>
</tr>
<tr class="even">
<td style="text-align: left;">mathuiuc</td>
<td style="text-align: right;">66.000000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">ProgramTheWorld</td>
<td style="text-align: right;">30.000000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">jeffgerickson</td>
<td style="text-align: right;">29.250000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">schreiberbj</td>
<td style="text-align: right;">17.750000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">Moi_Username</td>
<td style="text-align: right;">12.500000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">JRDSandstorm</td>
<td style="text-align: right;">10.000000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">csdude007</td>
<td style="text-align: right;">6.500000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">UIUCEngineering</td>
<td style="text-align: right;">6.000000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">WUTDO11231235</td>
<td style="text-align: right;">5.000000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">GenjoKodo</td>
<td style="text-align: right;">3.000000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">MrAcurite</td>
<td style="text-align: right;">3.000000</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">TheFearlessChuaEater</td>
<td style="text-align: right;">3.000000</td>
<td style="text-align: right;">4</td>
</tr>
</tbody>
</table>
