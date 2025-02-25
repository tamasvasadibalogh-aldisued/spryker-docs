---
title: 'System requirements: Spryker product release 202108.0'
description: This article provides the configuration that a system must have in order for the Spryker project to run smoothly and efficiently.
last_updated: Jun 16, 2021
template: howto-guide-template
---
| REQUIREMENT | VALUE |
| ----------------- | ----------------------- |
| OS                          | <ul><li>Native: Linux</li><li>DevVM: MacOS and Windows</li></ul>  |
| **Web Server**                                | NginX - preferred. But any webserver which supports PHP will work such as lighttpd, Apache, Cherokee. |
| **Databases**                             | Depending on the project, one of the databases: MariaDB >= 10.4 - preferred, PostgreSQL >=9.6, or MySQL >=5.7. |
| **PHP**                                   | Spryker supports PHP >=7.3 with the following extensions: `curl`, `json`, `mysql`, `pdo-sqlite`, `sqlite3`, `gd`, `intl`, `mysqli`, `pgsql`, `ssh2`, `gmp`, `mcrypt`, `pdo-mysql`, `readline`, `twig`, `imagick`, `memcache`, `pdo-pgsql`, `redis`, `xml`, `bz2`, `mbstring`. The preferred version is 7.4. See [Supported Versions of PHP](/docs/scos/user/intro-to-spryker/whats-new/supported-versions-of-php.html) for details on the supported PHP versions.|
| **SSL**                                       | For production systems, a valid security certificate is required for HTTPS. |
| **Redis**                                     | Version >=3.2, >=5.0                                                |
| **Elasticsearch**                             | Version 6.x or 7.x                                        |
| **RabbitMQ**                                  | Version 3.6+                                                 |
| **Jenkins (for cronjob management)**          | Version 1.6.x or 2.x          |
| **Graphviz (for statemachine visualization)** | 2.x                                                          |
|**Node.js**| Version >= 12.0.0 |
|**NPM**| Version >= 6.9.0 |
|**Intranet**| Back Office application (Zed) must be secured in an Intranet (using VPN, Basic Auth, IP Allowlist, DMZ, etc.) |
