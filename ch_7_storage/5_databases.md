# Databases in Kubernetes

Now that you know how to work with volumes in Kubernetes, you might be thinking:  
"Awesome! I'll host my CRUD app on Kubernetes and use a volume to store my PostgreSQL database data!"

While this is certainly possible, it's not always the best idea for all use cases. In fact, for many production systems, using a managed database service can often be a better choice. Here's an example from the Boot.dev system that powers this website.

## The Boot.dev System Architecture

The Boot.dev system consists of the following components:

- A web application, currently served by Cloudflare (this could easily be a Kubernetes deployment if we cared to move it).
- Several backend microservices, all running on Kubernetes in Google Cloud.
- Our main JSON CRUD API.
- A Discord bot.
- A service that compiles students' Go code to WASM.
- **A managed PostgreSQL database**, hosted by Cloud SQL (GCP).

### Why We Use a Managed Database

You might wonder, "Why not host the PostgreSQL database on Kubernetes?" Well, there are several reasons why we choose a managed service like Cloud SQL (GCP) for databases:

- **Kubernetes is not always the simplest solution** for hosting databases. While it is possible to host a PostgreSQL database on Kubernetes, it requires a lot of extra effort and manual configuration to ensure it's reliable and scalable.
- For example, to run a PostgreSQL database on Kubernetes, we'd need to:
  - Manually create a persistent volume for storage.
  - Handle PostgreSQL version upgrades.
  - Set appropriate resource limits (CPU, memory).
  - Set up automated backups.

These tasks can be complex, time-consuming, and error-prone. A managed service like Cloud SQL or Amazon RDS handles all of these tasks for you automatically, letting you focus on building and scaling your application.

### When Would You Use a Database on Kubernetes?

That said, there are situations where hosting a database on Kubernetes may still make sense. I have used databases on Kubernetes in the past, typically when:

- The database wasn't mission-critical, meaning there was a little tolerance for downtime or data loss.
- The database had minimal resource needs, or the data set was small and relatively static.

**Example:** I've deployed Grafana and Prometheus on Kubernetes, and both of these tools have out-of-the-box support for in-cluster databases. I didn't worry much about backups or automatic upgrades because:

- The data (e.g., telemetry data) was not mission-critical.
- The datasets were small and did not change frequently.

In these cases, running databases on Kubernetes was a good fit.

### Conclusion

While Kubernetes offers the flexibility to run databases in containers, it's not always the best choice for production databases due to the complexity involved in managing them. For most use cases, especially for mission-critical applications, using a managed service like Cloud SQL or Amazon RDS provides a simpler, more reliable, and less maintenance-heavy solution.

```

```
