#Data backlog and it's impact on error budget.md

We often get asked how Nobl9 handles data backlog in various cases; hence let’s spend some time to explore different scenarios and how to address them. 


**1. How does backlogging work with Nobl9 agents? (for example network issues)

  This is slightly different between the data sources and if you need to find out more about a specific data source, please ask the question in the product slack channel.
However, in a more general sense, if an agent can’t reach the N9 data intake, it caches using FIFO method and retries indefinitely. If the lack of connection takes too long, it may exhaust the cache; it is also worth noting that the cache is user configurable by how much memory is allocated to the container. 
This lack of data could impact the Error Budget but there is a current task open with the product to determine whether lack of data indicates error, skip or drop. More to come on this once the feature is enabled.


**2. What if there is a data outage on the data source?

  In short, if the data source is out then N9 won’t get data and the error budget will be impacted. 
If the agent keeps running, it will try to catch up after reconnecting. (This does vary by data source. Datadog is the most mature in this respect due to us having hit this issue due to its tight rate limits.) If the agent restarts (says it's relocated) then it's possible it would stop retrying, the way it's coded now. Our integration mechanism (querying APIs or metrics systems) is naturally somewhat susceptible to outages either between the agent and the metrics system (including outage of the metrics system) or between the agent and N9. That said, we're currently accepting some of that risk of data loss in these (hopefully rare, and so far they've been rare) circumstances.

In summary to quote Alex Hidalgo,
>“SLOs are essentially always approximations. As long as there isn’t too much missing data you’ll still get a clear picture over long time windows. Hiccups are bound to happen. Which is why we have SLOs in the first place”
