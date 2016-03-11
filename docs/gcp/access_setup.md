## Creating a Google Cloud Service Account

Follow the [instructions](https://cloud.google.com/storage/docs/authentication#service_accounts) in Google Cloud's documentation to create a `Service account` and `Generate a new P12 key`.

Make sure that at API level (**APIs and auth** menu) you have enabled:

* Google Compute Engine
* Google Compute Engine Instance Group Manager API
* Google Compute Engine Instance Groups API
* BigQuery API
* Google Cloud Deployment Manager API
* Google Cloud DNS API
* Google Cloud SQL
* Google Cloud Storage
* Google Cloud Storage JSON API

>If you have enabled every API then you have to wait about **10 minutes** for the provider.

When creating GCP credentials **in Cloudbreak you will have to provide the email address of your `Service Account` 
(from the Service accounts page of your Google Cloud Platform Permissions) and the `Project ID` (from the Dashboard 
of your Google Cloud Platform Home) where the service account is created.** You'll also have to **upload the 
generated P12 file and provide an OpenSSH formatted public key** that will be used as an SSH key.
