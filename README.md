<a href="https://opensource.newrelic.com/oss-category/#archived"><picture><source media="(prefers-color-scheme: dark)" srcset="https://github.com/newrelic/opensource-website/raw/main/src/images/categories/dark/Archived.png"><source media="(prefers-color-scheme: light)" srcset="https://github.com/newrelic/opensource-website/raw/main/src/images/categories/Archived.png"><img alt="New Relic Open Source archived project banner." src="https://github.com/newrelic/opensource-website/raw/main/src/images/categories/Archived.png"></picture></a>

# Archival Notice

❗Notice: This project has been archived _as is_ and is no longer actively maintained.

---

# New Relic  integration for Microsoft SQL Server- Experimental

**This is the standard SQL Server NRI (v2.8.1) PLUS Query Plans sent to the Log API**

## [Compatibility and Requirements](https://docs.newrelic.com/docs/infrastructure/host-integrations/host-integrations-list/microsoft-sql/microsoft-sql-server-integration/#req)
Follow *only* the Compatibility and Requirements, do not continue to `Install and activate the integration`

## [SQL Server Configuration](https://docs.newrelic.com/docs/infrastructure/host-integrations/host-integrations-list/microsoft-sql/microsoft-sql-server-integration/#enable-microsoft-sql-server)
Follow the directions in [`Enable your Microsoft SQL Server`](https://docs.newrelic.com/docs/infrastructure/host-integrations/host-integrations-list/microsoft-sql/microsoft-sql-server-integration/#enable-microsoft-sql-server),

## Installation
As this is a custom integration it must be installed manually. All directions assume a standard Infrastructure installation.

### Linux
1. Download `nri-mssql-queryplan` from the [GitHub Release directory](https://github.com/newrelic-experimental/nri-mssql-experimental/releases)
2. Place `nri-mssql-queryplan` in `/var/db/newrelic-infra/custom-integrations`
3. Copy the [Integration's configuration file](samples/mssql-queryplan-config.yml.sample) to `/etc/newrelic-infra/integrations.d/`

### Windows
1. Download `nri-mssql-queryplan.exe` from the [GitHub Release directory](https://github.com/newrelic-experimental/nri-mssql-experimental/releases)
2. Place `nri-mssql-queryplan.exe` in `C:\Program Files\New Relic\newrelic-infra\newrelic-integrations`
3. Copy the [Integration's configuration file](samples/mssql-queryplan-config.yml.sample) to `C:\Program Files\New Relic\newrelic-infra\integrations.d`

## Configuration
*NOTE:* YAML escapes backslash sequences unless the entire string is enclosed in single quotes.

### [Common MS SQL options](https://docs.newrelic.com/docs/infrastructure/host-integrations/host-integrations-list/microsoft-sql/microsoft-sql-server-integration/#config)
This integration is a fork of the Production MS SQL Integration, see [here](https://docs.newrelic.com/docs/infrastructure/host-integrations/host-integrations-list/microsoft-sql/microsoft-sql-server-integration/#config) for common
configuration settings. All original configuration settings work with this integration.

### Query plan specific configuration
Custom configuration parameters for `mssql-queryplan-config.yml`, all are placed under the `env` stanza:
-  `QUERY_PLAN_CONFIG`: Full path to YAML configuration with one or more SQL queries that collects query plans. See [`queryplan.yml.sample`](samples/queryplan.yml.sample) for an example. No default value.
-  `LOG_API_ENDPOINT`:  URL of the New Relic Log endpoint to use, [see here for options](https://docs.newrelic.com/docs/logs/log-api/introduction-log-api/#endpoint). Default is the US endpoint `https://log-api.newrelic.com/log/v1`
-  `LICENSE_KEY`:       New Relic License Key or Insights Insert Key, License Key is preferred.

## Query Plan Logging
The integration is capable of sending Query Plans to the New Relic Log API. The Log API is used as it can accept plans up to 128K in length after gzip compression and base64 encoding. The resulting `Logs` events each contain a `query_plan`
attribute that is:
- Base64 encoded
- gzip'd
- JSON string (not XML)

## Troubleshooting
This integration is relatively complex, here is a list of troubleshooting tips:
1. Verify that the query plan SQL actually generates query plans by running it in a SQL tool against the database
2. Enable `VERBOSE` logging and look at the Infrastructure log
3. Run the integration from the command line and check for errors. Run `nri-mssql-queryplan --help` for more information.

## Building (Optional)
Golang and make are required to build the integration. Use a current version of Go.

After cloning this repository, go to the directory of the MSSQL integration and build it:

```bash
$ make clean compile
```

To cross compile the application for an alternate platform use:
```bash
GOOS=<OS> GOARCH=<ARCH> make clean compile
```

To see a list of available operating systems and architectures try:
```bash
go tool dist list
```
Which displays a list of `<OperatingSystem>/<Architecture>` pairs, for instance:
```bash
go tool dist list
...
linux/386
linux/amd64
...
windows/386
windows/amd64
...
```

To start the integration from a command line, run `nri-mssql`:

```bash
$ ./bin/nri-mssql
```

If you want to know more about usage of `./bin/nri-mssql`, pass the `-help` parameter:

```bash
$ ./bin/nri-mssql -help
```

## Testing

To run the tests execute:

```bash
$ make test
```

## Support

New Relic has open-sourced this project. This project is provided AS-IS WITHOUT WARRANTY OR DEDICATED SUPPORT. Issues and contributions should be reported to the project here on GitHub.

We encourage you to bring your experiences and questions to the [Explorers Hub](https://discuss.newrelic.com) where our community members collaborate on solutions and new ideas.

## Contributing

We encourage your contributions to improve Salesforce Commerce Cloud for New Relic Browser! Keep in mind when you submit your pull request, you'll need to sign the CLA via the click-through using CLA-Assistant. You only have to sign the CLA one time per project. If you have any questions, or to execute our corporate CLA, required if your contribution is on behalf of a company, please drop us an email at opensource@newrelic.com.


**A note about vulnerabilities**

As noted in our [security policy](../../security/policy), New Relic is committed to the privacy and security of our customers and their data. We believe that providing coordinated disclosure by security researchers and engaging with the security community are important means to achieve our security goals.

If you believe you have found a security vulnerability in this project or any of New Relic's products or websites, we welcome and greatly appreciate you reporting it to New Relic through [HackerOne](https://hackerone.com/newrelic).

If you would like to contribute to this project, review [these guidelines](./CONTRIBUTING.md).

To all contributors, we thank you!  Without your contribution, this project would not be what it is today.

## License

nri-mssql is licensed under the [MIT](/LICENSE) License.
