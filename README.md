[![New Relic Experimental header](https://github.com/newrelic/opensource-website/raw/master/src/images/categories/Experimental.png)](https://opensource.newrelic.com/oss-category/#new-relic-experimental)

![GitHub forks](https://img.shields.io/github/forks/newrelic-experimental/nri-mssql?style=social)
![GitHub stars](https://img.shields.io/github/stars/newrelic-experimental/nri-mssql?style=social)
![GitHub watchers](https://img.shields.io/github/watchers/newrelic-experimental/nri-mssql?style=social)

![GitHub all releases](https://img.shields.io/github/downloads/newrelic-experimental/nri-mssql/total)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/newrelic-experimental/nri-mssql)
![GitHub last commit](https://img.shields.io/github/last-commit/newrelic-experimental/nri-mssql)
![GitHub Release Date](https://img.shields.io/github/release-date/newrelic-experimental/nri-mssql)


![GitHub issues](https://img.shields.io/github/issues/newrelic-experimental/nri-mssql)
![GitHub issues closed](https://img.shields.io/github/issues-closed/newrelic-experimental/nri-mssql)
![GitHub pull requests](https://img.shields.io/github/issues-pr/newrelic-experimental/nri-mssql)
![GitHub pull requests closed](https://img.shields.io/github/issues-pr-closed/newrelic-experimental/nri-mssql)

# New Relic experimental integration for Microsoft SQL Server- Experimental

* This is the standard SQL Server NRI PLUS Query Plans sent to the Log API *

The New Relic integration for MS SQL Server captures critical performance metrics and inventory reported by a SQL Server Instance. Data on the SQL Server Instance and Databases is collected.

Inventory and metric data is collected via SQL queries to the Instance.

## Configuration

A user with the necessary permissions to collect all the metrics and inventory can be configured as follows

```sql
USE master;
CREATE LOGIN newrelic WITH PASSWORD = 'tmppassword';
CREATE USER newrelic FOR LOGIN newrelic;
GRANT CONNECT SQL TO newrelic;
GRANT VIEW SERVER STATE TO newrelic;

-- Goes through each user database and adds public permissions
DECLARE @name NVARCHAR(max)
DECLARE db_cursor CURSOR FOR
SELECT NAME
FROM master.dbo.sysdatabases
WHERE NAME NOT IN ('master','msdb','tempdb','model')
OPEN db_cursor
FETCH NEXT FROM db_cursor INTO @name WHILE @@FETCH_STATUS = 0
BEGIN
	EXECUTE('USE "' + @name + '"; CREATE USER newrelic FOR LOGIN newrelic;' );
	FETCH next FROM db_cursor INTO @name
END
CLOSE db_cursor
DEALLOCATE db_cursor
```

## Installation and usage

For installation and usage instructions, see our [documentation web site](https://docs.newrelic.com/docs/integrations/host-integrations/host-integrations-list/mssql-monitoring-integration).

## Custom queries

To add custom queries, use the **-custom_metrics_query** option to provide a single query, or the **-custom_metrics_config** option to specify a YAML file with one or more queries, such as the sample `mssql-custom-query.yml.sample`

### How attributes are named

Each query that returns a table of values will be parsed row by row, adding the **MssqlCustomQuerySample** event as follows:

- The column name is the attribute name
- Each row value in that column is the attribute value
- The metric type is auto-detected whether it is a number (type GAUGE), or a string (type ATTRIBUTE)

One customizable attribute in each row can be configured by database values using the following names:

- The column `metric_name` specifies its attribute name
- The column `metric_value` specifies its attribute value
- The column `metric_type` specifies its metric type, i.e. `gauge` or `attribute`

For example, the following query makes attributes named `category_0`, `category_1`, `category_2` and so on.
```sql
SELECT CONCAT('category_', category_id) AS metric_name, name AS metric_value, category_type FROM syscategories
```

### Specifying queries in YAML

When using a YAML file containing queries, you can specify the following parameters for each query:

- `query` (required) contains the SQL query
- `database` (optional) Prepends `USE <database name>; ` to the SQL, and adds the database name as an attribute
- `prefix` (optional) prefix to prepend to the attribute name
- `metric_name` (optional) specify the name for the customizable attribute
- `metric_type` (optional) specify the metric type for the customizable attribute

## Query Plan Logging
The integration is capable of sending Query Plans to the New Relic Log API. The Log API is used as it can accept plans up to 128K in length after gzip compression and base64 encoding. The resulting `query_plan` attribute is:
- Base64 encoded
- gzip'd
- JSON string (not XML)

### Configuration
Configuration parameters for `mssql-config.yml`:
-  `QueryPlanConfig`: Full path to YAML configuration with one or more SQL queries that collects query plans. See `queryplan.yml.sample` for an example. No default value.
-  `LogApiEndpoint`:  URL of the New Relic Log endpoint to use, [see](https://docs.newrelic.com/docs/logs/log-api/introduction-log-api/#endpoint). Default is the US endpoint "https://log-api.newrelic.com/log/v1"
-  `LicenseKey`:      New Relic License Key or Insights Insert Key, License Key is preferred.
## Compatibility

* Supported OS: Windows version compatible with the New Relic infrastructure agent
* MS SQL Server versions: SQL Server 2008 R2+

Note:  It also seems to work on Linux for the containerized Linux version of MSSQL

## Building

Golang is required to build the integration. We recommend Golang 1.16 or higher.

After cloning this repository, go to the directory of the MSSQL integration and build it:

```bash
$ make
```

The command above executes the tests for the MSSQL integration and builds an executable file called `nri-mssql` under the `bin` directory. 

To start the integration, run `nri-mssql`:

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

Should you need assistance with New Relic products, you are in good hands with several support diagnostic tools and support channels.



> New Relic offers NRDiag, [a client-side diagnostic utility](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/troubleshooting/new-relic-diagnostics) that automatically detects common problems with New Relic agents. If NRDiag detects a problem, it suggests troubleshooting steps. NRDiag can also automatically attach troubleshooting data to a New Relic Support ticket.

If the issue has been confirmed as a bug or is a Feature request, please file a Github issue.

**Support Channels**

* [New Relic Documentation](https://docs.newrelic.com): Comprehensive guidance for using our platform
* [New Relic Community](https://discuss.newrelic.com): The best place to engage in troubleshooting questions
* [New Relic Developer](https://developer.newrelic.com/): Resources for building a custom observability applications
* [New Relic University](https://learn.newrelic.com/): A range of online training for New Relic users of every level

## Privacy

At New Relic we take your privacy and the security of your information seriously, and are committed to protecting your information. We must emphasize the importance of not sharing personal data in public forums, and ask all users to scrub logs and diagnostic information for sensitive information, whether personal, proprietary, or otherwise.

We define “Personal Data” as any information relating to an identified or identifiable individual, including, for example, your name, phone number, post code or zip code, Device ID, IP address, and email address.

For more information, review [New Relic’s General Data Privacy Notice](https://newrelic.com/termsandconditions/privacy).

## Contribute

We encourage your contributions to improve this project! Keep in mind that when you submit your pull request, you'll need to sign the CLA via the click-through using CLA-Assistant. You only have to sign the CLA one time per project.

If you have any questions, or to execute our corporate CLA (which is required if your contribution is on behalf of a company), drop us an email at opensource@newrelic.com.

**A note about vulnerabilities**

As noted in our [security policy](../../security/policy), New Relic is committed to the privacy and security of our customers and their data. We believe that providing coordinated disclosure by security researchers and engaging with the security community are important means to achieve our security goals.

If you believe you have found a security vulnerability in this project or any of New Relic's products or websites, we welcome and greatly appreciate you reporting it to New Relic through [HackerOne](https://hackerone.com/newrelic).

If you would like to contribute to this project, review [these guidelines](./CONTRIBUTING.md).

To all contributors, we thank you!  Without your contribution, this project would not be what it is today.

## License

nri-mssql is licensed under the [MIT](/LICENSE) License.
