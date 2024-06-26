# Data validation

## Validation

<!--DHIS2-SECTION-ID:webapi_validation-->

To generate a data validation summary you can interact with the
validation resource. The dataSet resource is optimized for data entry
clients for validating a data set / form, and can be accessed like this:

    /api/33/validation/dataSet/QX4ZTUbOt3a.json?pe=201501&ou=DiszpKrYNg8

In addition to validate rules based on data set, there are two
additional methods for performing validation: Custom validation and
Scheduled validation.

Custom validation can be initiated through the "Data Quality" app, where
you can configure the periods, validation rule groups and organisation
units to be included in the analysis and if you want to send out
notifications for and/or persist the results found. The result of this
analysis will be a list of violations found using your criteria.

The first path variable is an identifier referring to the data set to
validate. XML and JSON resource representations are supported. The
response contains violations of validation rules. This will be extended
with more validation types in the coming versions.

To retrieve validation rules which are relevant for a specific data set,
meaning validation rules with formulas where all data elements are part
of the specific data set, you can make a GET request to to
`validationRules` resource like this:

    /api/validationRules?dataSet=<dataset-id>

The validation rules have a left side and a right side, which is
compared for validity according to an operator. The valid operator
values are found in the table below.

<table>
<caption>Operators</caption>
<colgroup>
<col style="width: 28%" />
<col style="width: 71%" />
</colgroup>
<thead>
<tr class="header">
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>equal_to</td>
<td>Equal to</td>
</tr>
<tr class="even">
<td>not_equal_to</td>
<td>Not equal to</td>
</tr>
<tr class="odd">
<td>greater_than</td>
<td>Greater than</td>
</tr>
<tr class="even">
<td>greater_than_or_equal_to</td>
<td>Greater than or equal to</td>
</tr>
<tr class="odd">
<td>less_than</td>
<td>Less than</td>
</tr>
<tr class="even">
<td>less_than_or_equal_to</td>
<td>Less than or equal to</td>
</tr>
<tr class="odd">
<td>compulsory_pair</td>
<td>If either side is present, the other must also be</td>
</tr>
<tr class="even">
<td>exclusive_pair</td>
<td>If either side is present, the other must not be</td>
</tr>
</tbody>
</table>

The left side and right side expressions are mathematical expressions
which can contain references to data elements and category option
combinations on the following format:

    ${<dataelement-id>.<catoptcombo-id>}

The left side and right side expressions have a *missing value
strategy*. This refers to how the system should treat data values which
are missing for data elements / category option combination references
in the formula in terms of whether the validation rule should be checked
for validity or skipped. The valid missing value strategies are found in
the table below.

<table>
<caption>Missing value strategies</caption>
<colgroup>
<col style="width: 28%" />
<col style="width: 71%" />
</colgroup>
<thead>
<tr class="header">
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>SKIP_IF_ANY_VALUE_MISSING</td>
<td>Skip validation rule if any data value is missing</td>
</tr>
<tr class="even">
<td>SKIP_IF_ALL_VALUES_MISSING</td>
<td>Skip validation rule if all data values are missing</td>
</tr>
<tr class="odd">
<td>NEVER_SKIP</td>
<td>Never skip validation rule irrespective of missing data values</td>
</tr>
</tbody>
</table>

## Validation Results

<!--DHIS2-SECTION-ID:webapi_validation_results-->

Validation results are persisted results of violations found during a
validation analysis. If you choose "persist results" when starting or
scheduling a validation analysis, any violations found will be stored in
the database. When a result is stored in the database it will be used
for 3 things:

1.  Generating analytics based on the stored results.

2.  Persisted results that have not generated a notification, will do so,
    once.

3.  Keeping track of whether or not the result has generated a
    notification.

4.  Skipping rules that have been already checked when running
    validation analysis.

This means if you don't persist your results, you will be unable to
generate analytics for validation results, if checked, results will
generate notifications every time it's found and running validation
analysis might be slower.

The validation results persisted can be viewed at the following
endpoint:

    /api/33/validationResults

You can also inspect an individual result using the validation result id
in this endpoint:

    /api/33/validationResults/<id>

Validation results are sent out to the appropriate users once every day,
but can also be manually triggered to run on demand using the following
api endpoint:

    /api/33/validation/sendNotifications

Only unsent results are sent using this endpoint.

## Data analysis

<!--DHIS2-SECTION-ID:webapi_data_analysis-->

Several resources for performing data analysis and finding data quality
and validation issues are provided.

### Validation rule analysis

<!--DHIS2-SECTION-ID:webapi_data_analysis_validation_rules-->

To run validation rules and retrieve violations:

    /api/dataAnalysis/validationRules

The following query parameters are supported:

<table>
<caption>Validation rule analysis query parameters</caption>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th>Query parameter</th>
<th>Description</th>
<th>Option</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>vrg</td>
<td>Validation rule group</td>
<td>ID</td>
</tr>
<tr class="even">
<td>ou</td>
<td>Organisation unit</td>
<td>ID</td>
</tr>
<tr class="odd">
<td>startDate</td>
<td>Start date for the timespan</td>
<td>Date</td>
</tr>
<tr class="even">
<td>endDate</td>
<td>End date for the timespan</td>
<td>Date</td>
</tr>
<tr class="odd">
<td>persist</td>
<td>Whether to persist violations in the system</td>
<td>false | true</td>
</tr>
<tr class="even">
<td>notification</td>
<td>Whether to send notifications about violations</td>
<td>false | true</td>
</tr>
</tbody>
</table>

Sample output:
```json
    [{
	"validationRuleId": "kgh54Xb9LSE",
	"validationRuleDescription": "Malaria outbreak",
	"organisationUnitId": "DiszpKrYNg8",
	"organisationUnitDisplayName": "Ngelehun CHC",
	"organisationUnitPath": "/ImspTQPwCqd/O6uvpzGd5pu/YuQRtpLP10I/DiszpKrYNg8",
	"organisationUnitAncestorNames": "Sierra Leone / Bo / Badjia / ",
	"periodId": "201901",
	"periodDisplayName": "January 2019",
	"attributeOptionComboId": "HllvX50cXC0",
	"attributeOptionComboDisplayName": "default",
	"importance": "MEDIUM",
	"leftSideValue": 10.0,
	"operator": ">",
	"rightSideValue": 14.0
}, {
	"validationRuleId": "ZoG4yXZi3c3",
	"validationRuleDescription": "ANC 2 cannot be higher than ANC 1",
	"organisationUnitId": "DiszpKrYNg8",
	"organisationUnitDisplayName": "Ngelehun CHC",
	"organisationUnitPath": "/ImspTQPwCqd/O6uvpzGd5pu/YuQRtpLP10I/DiszpKrYNg8",
	"organisationUnitAncestorNames": "Sierra Leone / Bo / Badjia / ",
	"periodId": "201901",
	"periodDisplayName": "January 2019",
	"attributeOptionComboId": "HllvX50cXC0",
	"attributeOptionComboDisplayName": "default",
	"importance": "MEDIUM",
	"leftSideValue": 22.0,
	"operator": "<=",
	"rightSideValue": 19.0
}]
```

### Standard deviation based outlier analysis

<!--DHIS2-SECTION-ID:webapi_data_analysis_std_dev_outlier-->

To identify data outliers based on standard deviations of the average
value:

    /api/dataAnalysis/stdDevOutlier

The following query parameters are supported:

<table>
<caption>Standard deviation outlier analysis query parameters</caption>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th>Query parameter</th>
<th>Description</th>
<th>Option</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>ou</td>
<td>Organisation unit</td>
<td>ID</td>
</tr>
<tr class="even">
<td>startDate</td>
<td>Start date for the timespan</td>
<td>Date</td>
</tr>
<tr class="odd">
<td>endDate</td>
<td>End date for the timespan</td>
<td>Date</td>
</tr>
<tr class="even">
<td>ds</td>
<td>Data sets, parameter can be repeated</td>
<td>ID</td>
</tr>
<tr class="odd">
<td>standardDeviation</td>
<td>Number of standard deviations from the average</td>
<td>Numeric value</td>
</tr>
</tbody>
</table>

### Min/max value based outlier analysis

<!--DHIS2-SECTION-ID:webapi_data_analysis_min_max_outlier-->

To identify data outliers based on min/max values:

    /api/dataAnalysis/minMaxOutlier

The supported query parameters are equal to the *std dev based outlier
analysis* resource described above.

### Follow-up data analysis

To identify data marked for follow-up:

    /api/dataAnalysis/followup

The supported query parameters are equal to the *std dev based outlier
analysis* resource described above.

## Data integrity

<!--DHIS2-SECTION-ID:webapi_data_integrity-->

The data integrity capabilities of the data administration module are
available through the web API. This section describes how to run the
data integrity process as well as retrieving the result. The details of
the analysis performed are described in the user manual.

### Running data integrity

<!--DHIS2-SECTION-ID:webapi_data_integrity_run-->

The operation of measuring data integrity is a fairly resource (and
time) demanding task. It is therefore run as an asynchronous process and
only when explicitly requested. Starting the task is done by forming an
empty POST request to the *dataIntegrity* endpoint like so (demonstrated
in curl syntax):

```bash
curl -X POST "https://localhost/api/33/dataIntegrity"
```

If successful the request will return HTTP 202 immediately. The location
header of the response points to the resource used to check the status
of the request. The payload also contains a json object of the job
created. Forming a GET request to the given location yields an empty
JSON response if the task has not yet completed and a JSON taskSummary
object when the task is done. Polling (conservatively) to this resource
can hence be used to wait for the task to finish.

### Fetching the result

<!--DHIS2-SECTION-ID:webapi_data_integrity_fetch_results-->

Once data integrity is finished running the result can be fetched from
the `system/taskSummaries` resource like so:

```bash
curl "https://dhis.domain/api/33/system/taskSummaries/DATAINTEGRITY"
```

The returned object contains a summary for each point of analysis,
listing the names of the relevant integrity violations. As stated in the
leading paragraph for this section the details of the analysis (and the
resulting data) can be found in the user manual chapter on Data
Administration.

## Complete data set registrations

<!--DHIS2-SECTION-ID:webapi_complete_data_set_registrations-->

This section is about complete data set registrations for data sets. A
registration marks as a data set as completely captured.

### Completing data sets

<!--DHIS2-SECTION-ID:webapi_completing_data_sets-->

This section explains how to register data sets as complete. This is
achieved by interacting with the *completeDataSetRegistrations*
resource:

    /api/33/completeDataSetRegistrations

The endpoint supports the *POST* method for registering data set
completions. The endpoint is functionally very similar to the
*dataValueSets* endpoint, with support for bulk import of complete
registrations.

Importing both *XML* and *JSON* formatted payloads are supported. The
basic format of this payload, given as *XML* in this example, is like
so:

```xml
<completeDataSetRegistrations xmlns="http://dhis2.org/schema/dxf/2.0">
  <completeDataSetRegistration period="200810" dataSet="eZDhcZi6FLP"
    organisationUnit="qhqAxPSTUXp" attributeOptionCombo="bRowv6yZOF2" storedBy="imported"/>
  <completeDataSetRegistration period="200811" dataSet="eZDhcZi6FLP"
    organisationUnit="qhqAxPSTUXp" attributeOptionCombo="bRowv6yZOF2" storedBy="imported"/>
</completeDataSetRegistrations>
```

The *storedBy* attribute is optional (as it is a nullable property on
the complete registration object). You can also optionally set the
*date* property (time of registration) as an attribute. It the time is
not set, the current time will be used.

The import process supports the following query parameters:

<table>
<caption>Complete data set registrations query parameters</caption>
<colgroup>
<col style="width: 16%" />
<col style="width: 18%" />
<col style="width: 64%" />
</colgroup>
<thead>
<tr class="header">
<th>Parameter</th>
<th>Values</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>dataSetIdScheme</td>
<td>id | name | code | attribute:ID</td>
<td>Property of the data set to use to map the complete registrations.</td>
</tr>
<tr class="even">
<td>orgUnitIdScheme</td>
<td>id | name | code | attribute:ID</td>
<td>Property of the organisation unit to use to map the complete registrations.</td>
</tr>
<tr class="odd">
<td>attributeOptionComboIdScheme</td>
<td>id | name | code | attribute:ID</td>
<td>Property of the attribute option combos to use to map the complete registrations.</td>
</tr>
<tr class="even">
<td>idScheme</td>
<td>id | name | code | attribute:ID</td>
<td>Property of all objects including data sets, org units and attribute option combos, to use to map the complete registrations.</td>
</tr>
<tr class="odd">
<td>preheatCache</td>
<td>false | true</td>
<td>Whether to save changes on the server or just return the import summary.</td>
</tr>
<tr class="even">
<td>dryRun</td>
<td>false | true</td>
<td>Whether registration applies to sub units</td>
</tr>
<tr class="odd">
<td>importStrategy</td>
<td>CREATE | UPDATE | CREATE_AND_UPDATE | DELETE</td>
<td>Save objects of all, new or update import status on the server.</td>
</tr>
<tr class="even">
<td>skipExistingCheck</td>
<td>false | true</td>
<td>Skip checks for existing complete registrations. Improves performance. Only use for empty databases or when the registrations to import do not exist already.</td>
</tr>
<tr class="odd">
<td>async</td>
<td>false | true</td>
<td>Indicates whether the import should be done asynchronous or synchronous. The former is suitable for very large imports as it ensures that the request does not time out, although it has a significant performance overhead. The latter is faster but requires the connection to persist until the process is finished.</td>
</tr>
</tbody>
</table>

### Reading complete data set registrations

<!--DHIS2-SECTION-ID:webapi_reading_complete_data_sets-->

This section explains how to retrieve data set completeness
registrations. We will be using the *completeDataSetRegistrations*
resource. The query parameters to use are these:

<table>
<caption>Data value set query parameters</caption>
<colgroup>
<col style="width: 18%" />
<col style="width: 81%" />
</colgroup>
<thead>
<tr class="header">
<th>Parameter</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>dataSet</td>
<td>Data set identifier, multiple data sets are allowed</td>
</tr>
<tr class="even">
<td>period</td>
<td>Period identifier in ISO format. Multiple periods are allowed.</td>
</tr>
<tr class="odd">
<td>startDate</td>
<td>Start date for the time span of the values to export</td>
</tr>
<tr class="even">
<td>endDate</td>
<td>End date for the time span of the values to export</td>
</tr>
<tr class="odd">
<td>created</td>
<td>Include only registrations which were created since the given timestamp</td>
</tr>
<tr class="even">
<td>createdDuration</td>
<td>Include only registrations which were created within the given duration. The format is &lt;value&gt;&lt;time-unit&gt;, where the supported time units are &quot;d&quot;, &quot;h&quot;, &quot;m&quot;, &quot;s&quot; <em>(days, hours, minutes, seconds).</em> The time unit is relative to the current time.</td>
</tr>
<tr class="odd">
<td>orgUnit</td>
<td>Organisation unit identifier, can be specified multiple times. Not applicable if orgUnitGroup is given.</td>
</tr>
<tr class="even">
<td>orgUnitGroup</td>
<td>Organisation unit group identifier, can be specified multiple times. Not applicable if orgUnit is given.</td>
</tr>
<tr class="odd">
<td>children</td>
<td>Whether to include the children in the hierarchy of the organisation units</td>
</tr>
<tr class="even">
<td>limit</td>
<td>The maximum number of registrations to include in the response.</td>
</tr>
<tr class="odd">
<td>idScheme</td>
<td>Identifier property used for meta data objects in the response.</td>
</tr>
<tr class="even">
<td>dataSetIdScheme</td>
<td>Identifier property used for data sets in the response. Overrides idScheme.</td>
</tr>
<tr class="odd">
<td>orgUnitIdScheme</td>
<td>Identifier property used for organisation units in the response. Overrides idScheme.</td>
</tr>
<tr class="even">
<td>attributeOptionComboIdScheme</td>
<td>Identifier property used for attribute option combos in the response. Overrides idScheme.</td>
</tr>
</tbody>
</table>

The dataSet and orgUnit parameters can be repeated in order to include
multiple data sets and organisation units.

The period, start/end date, created and createdDuration parameters
provide multiple ways to set the time dimension for the request, thus only
one can be used. For example, it doesn't make sense to both set the
start/end date and to set the periods.

An example request looks like this:

```bash
curl "https://play.dhis2.org/demo/api/33/completeDataSetRegistrations?dataSet=pBOMPrpg1QX
  &dataSet=pBOMPrpg1QX&startDate=2014-01-01&endDate=2014-01-31&orgUnit=YuQRtpLP10I
  &orgUnit=vWbkYPRmKyS&children=true"
  -H "Accept:application/xml" -u admin:district
```

You can get the response in *xml* and *json* format. You can indicate
which response format you prefer through the *Accept* HTTP header like
in the example above. For xml you use *application/xml*; for json you
use *application/json*.

### Un-completing data sets

<!--DHIS2-SECTION-ID:webapi_uncompleting_data_sets-->

This section explains how you can un-register the completeness of a data
set. To un-complete a data set you will interact with the
completeDataSetRegistrations resource:

    /api/33/completeDataSetRegistrations

This resource supports *DELETE* for un-registration. The following query
parameters are supported:

<table>
<caption>Complete data set registrations query parameters</caption>
<colgroup>
<col style="width: 16%" />
<col style="width: 18%" />
<col style="width: 64%" />
</colgroup>
<thead>
<tr class="header">
<th>Query parameter</th>
<th>Required</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>ds</td>
<td>Yes</td>
<td>Data set identifier</td>
</tr>
<tr class="even">
<td>pe</td>
<td>Yes</td>
<td>Period identifier</td>
</tr>
<tr class="odd">
<td>ou</td>
<td>Yes</td>
<td>Organisation unit identifier</td>
</tr>
<tr class="even">
<td>cc</td>
<td>No (must combine with cp)</td>
<td>Attribute combo identifier (for locking check)</td>
</tr>
<tr class="odd">
<td>cp</td>
<td>No (must combine with cp)</td>
<td>Attribute option identifiers, separated with ; for multiple values (for locking check)</td>
</tr>
<tr class="even">
<td>multiOu</td>
<td>No (default false)</td>
<td>Whether registration applies to sub units</td>
</tr>
</tbody>
</table>

