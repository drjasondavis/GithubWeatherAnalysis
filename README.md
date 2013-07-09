GithubWeatherAnalysis
=====================

Raw data for my GitHub weather analysis on drjasondavis.com

The procedure involves aggregating data from
GitHub’s repository, geocoding and normalizing places, and then joining
weather from Weather Underground’s API.

Step 1: Aggregation of GitHub check­in data on BigQuery. GitHub users
are worldwide and located in cities big and small. Analyzing weather
data requires connecting user check­in locations to local historical
weather data at the time of check­in, and collecting this is somewhat
expensive, as we’ll see below. So I restricted analysis to only the top
1,000 locations on GitHub.

Github check­in size can vary greatly by users, and also greatly by
project. While my check­ins may average a few hundred lines, others
may average 2 or 3 line. So if I write 600 lines of code, this may only
be 2 or 3 checkins for me, but dozens of check­ins for others. Instead
of using a raw check­in count, I measured the number of unique users
per location who checked in at any time during a given day. The final
dataset extracted from BigQuery contained the date, location, and this
distinct (user, day) count. GitHub’s data goes back to March, 2012.

Step 2: Location geocoding. This was necessary to de­dup names like
“NYC”, “New York”, and “New York, New York”, and also a required
step to find weather stations via Weather Underground’s API. I used
Google’s Maps API for this. Who knew that Earth is a town in Texas?

Step 3: What’s the weather? For each of the lat / long pairs returned by
Google, query Weather Underground’s API to get nearest weather
stations. And then for each date in the dataset, I fetched the weather
for each location. This step is quite expensive, since it required fetching
a quadratic number of weather points: the number of days * the
number of cities. Hence why I limited analysis to only 1,000 locations
in step 1.
