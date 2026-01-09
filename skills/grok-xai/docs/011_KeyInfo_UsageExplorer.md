#### [Key Information](#key-information)

# [Usage Explorer](#usage-explorer)

Sometimes as a team admin, you might want to monitor the API consumption, either to track spending, or to detect anomalies. xAI Console provides an easy-to-use [Usage Explorer](https://console.x.ai/team/default/usage) for team admins to track API usage across API keys, models, etc.

* * *

## [Basic usage](#basic-usage)

[Usage Explorer](https://console.x.ai/team/default/usage) page provides intuitive dropdown menus for you to customize how you want to view the consumptions.

For example, you can view your daily credit consumption with `Granularity: Daily`:

![Daily credit consumption in xAI Console Usage Explorer](/_next/image?url=%2Fassets%2Fdocs%2Fusage-explorer%2Fusage-by-day.png&w=1920&q=75)

By default, the usage is calculated by cost in US Dollar. You can select Dimension -> Tokens or Dimension -> Billing items to change the dimension to token count or billing item count.

![Usage by token in xAI Console Usage Explorer](/_next/image?url=%2Fassets%2Fdocs%2Fusage-explorer%2Fusage-by-token.png&w=1920&q=75)

You can also see the usage with grouping. This way, you can easily compare the consumption across groups. In this case, we are trying to compare consumptions across test and production API keys, so we select `Group by: API Key`:

![Usage by API Key in xAI Console Usage Explorer](/_next/image?url=%2Fassets%2Fdocs%2Fusage-explorer%2Fgroup-by-key.png&w=1920&q=75)

* * *

## [Filters](#filters)

The basic usage should suffice if you are only viewing general information. However, you can also use filters to conditionally display information.

The filters dropdown gives you the options to filter by a particular API key, a model, a request IP, a cluster, or the token type.

![Filters available in Usage Explorer](/_next/image?url=%2Fassets%2Fdocs%2Fusage-explorer%2Ffilter.png&w=2048&q=75)