# Studium

## Maintenance mode
App platform does not support maintenance mode. I can be achieved in Cloudflare by:
- proxying knowspread.com CNAMEs through Cloudflare (setting them as "proxied")
- enabling Page Rule which forwards traffic to our [maintenance page](http://s3-eu-west-1.amazonaws.com/knowspread.production/heroku-error-pages/maintenance.html)

Maintenance page is git tracked `public/maintenance.html` and can be updated manually by logging into AWS S3. *(How nice would it be to have it done by a rake task?!)*

## Certificate management
App platform only supports automated certificate management, not custom certs.
Certificates are issued by Cloudflare for 1 year. Current production cert expires Friday, 1 March 2024.

## Scaling
App components can be manually scaled vertically and horizontally.

[Question about autoscaling](https://www.digitalocean.com/community/questions/does-appl-platform-support-auto-scaling) from February 25, 2022 is unanswered.

There's an article about [building custom auto-scaler](https://pqvst.com/2021/09/30/digitalocean-app-platform/#building-an-auto-scaler).


## New environment (new app)
- easiest way to create a new app (environment) is to save app spec from current app settings, update it and create a new app by calling `doctl app create --spec spec.yaml`
- an app can be created manually in UI, but the UI is not capable of adding a job (for database migration)

## Postgres cluster
### Backups
Postgres cluster has point-in-time backups worth 7 days. It can be restored to a new cluster.
### Connection pool
**Outdated info** (support told me it should work, but I haven't tested it yet):
- While DO supports setup of connection pool, it is not possible to use it in apps with combination of Trusted Sources. Either connect directly to database, or disable Trusted Sources in the cluster.

### New user - grant privileges
[Modify user privileges](https://docs.digitalocean.com/products/databases/postgresql/how-to/modify-user-privileges/):

```sql
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO username;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO username;
GRANT CONNECT ON DATABASE database TO username;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL PRIVILEGES ON TABLES TO development;
GRANT ALL ON schema public TO knowspread;
```

## Redis
For each dev environment, use `rediss://connection/index_number` where `index_number` is a separate database from 0 to 10.

## Dev console
Use web-console from the app's Digital Ocean web UI.
