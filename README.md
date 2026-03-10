# Wedding Planner CMS & Website

This Docker compose project can be used to run a wedding planning [content management system](https://github.com/dwaring87/wedding-cms) and [associated website](https://github.com/dwaring87/wedding-site).

## Setup

### Content Management System

The [CMS](https://github.com/dwaring87/wedding-cms) is a [Directus](https://directus.io) instance specifically developed to manage the content of wedding website, the guest list, invitations, and mailing lists.

#### Environment Variables

You'll need to update some of the environment variables in the `cms.env` file for your specific instance.

| Variable | Description | Default |
| --- | --- | --- |
| HOST | IP or host the API listens on | `0.0.0.0`
| PORT | What port to run the API under | `8055`
| SECRET | Secret string used for secret signing.  When not set a random value will be used.  This means sessions won't persist across system restarts. | _Not set_<br />**SET THIS TO A RANDOM STRING IN PRODUCTION**.
| PUBLIC_URL | The publicly accessible URL to the CMS.  This will be used by Directus when generating the pull URLs to pages, photos, etc and should be externally accessible. | `http://localhost:8055`<br />**CHANGE THIS TO THE CMS PUBLIC URL IN PRODUCTION**
| ADMIN_EMAIL | The email address for the initial admin account | `me@example.com`<br />**CHANGE THIS IN PRODUCTION**
| ADMIN_PASSWORD | The password of the initial admin account | `password`<br />**CHANGE THIS IN PRODUCTION**
| ADMIN_FIRST_NAME | The first name of the initial admin account | `First`<br />**CHANGE THIS IN PRODUCTION**
| ADMIN_LAST_NAME | The last name of the intial admin account | `Last`<br />**CHANGE THIS IN PRODUCTION**
| DB_CLIENT | The database driver to use for the Directus database | `sqlite3`
| DB_FILENAME | The path to the sqlite database | `/directus/database/database.sqlite`
| CACHE_ENABLED | API data caching.  This is disabled by default since the Nuxt website handles its own cache. | `false`
| EMAIL_VERIFY_SETUP | Check if email setup is properly configured | `false`<br />You may want to enable this if you enable outgoing email to verify if your setup is correct.
| EMAIL_TRANSPORT | What to use to send emails. One of `sendmail`, `smtp`, `mailgun`, `ses` | _Not set_<br />View the [Directus email configuration documentation](https://directus.io/docs/configuration/email) for details
| RSVP_NOTIFICATIONS_ENABLE | When enabled, an email will be sent to the below address(es) when a guest is updated from the RSVP page of the website. This requires the Directus EMAIL variables to be set correctly. | `true`
| RSVP_NOTIFICATIONS_RECIPIENTS | A comma-separated list of email addresses to send RSVP notifications to | _Admin email address_

For additional environment variables that can be applied to any Directus instance, view the [Directus configuration documentation](https://directus.io/docs/configuration/general).

### Website

The website is a [Nuxt v4](https://nuxt.com) website that pulls its content from the CMS.  It has customizable pages and allows guests to RSVP through the website, when enabled.

#### Environment Variables

You'll need to update some of the environment variables in the `site.env` for your specific instance.

| Variable | Description | Default |
| --- | --- | --- |
| HOST | IP or host the website listens on | `0.0.0.0`
| PORT | The port the website listens on | `3000`
| NUXT_DIRECTUS_URL | The URL the Nuxt server uses to connect to the CMS.  The CMS is only accessed via the backend API server of the website, so the docker container name can be used as the host here (it does not need to be externally accessible) | `http://wedding-cms:8055`
| NUXT_CACHE_API | The cache length (seconds) for data retrieved from the Directus API. Increasing this will mean fewer API requests between the site and the CMS, which could increase performance, but means updates will be slower to appear.  This does not apply to the invite / guest information and the alert, which is not cached at all. | `15`
| NUXT_CACHE_PHOTOS | The cache length (seconds) for photos retrieved from the Directus API | `300`

### CMS and Website

#### Environment Variables

The CMS and website share the following environment variables, which are set in the `cms-site.env` file.

| Variable | Description | Default |
| --- | --- | --- |
| NUXT_DIRECTUS_TOKEN | The Static Access Token that is given to the Nuxt user on the CMS that is used by the Nuxt website API to access the database.  This token uses the API access policy, which has read access to most tables and has update access to the guest table (to update RSVP info).  It is automatically added to the Nuxt user account when starting the CMS (via the entrypoint.sh script). | `CHANGE_TO_RANDOM_STRING`<br />**CHANGE THIS TO A RANDOM STRING IN PRODUCTION**
