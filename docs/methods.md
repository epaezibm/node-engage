Supported Methods
=================

XML API Methods
---------------

Each of the XML API method calls corresponds directly to an XML API method as documented in the XML API Developer's Guide.

### addContactToContactList

parameter     | required? | type             | description
------------- | --------- | ---------------- | -----------
contactId     | no        | positive integer | Watson Campaign Automation recipient id to add to contact list
contactListId | yes       | positive integer | Watson Campaign Automation contact list id to have contact added to
columns       | no        | scalar hash      | Key column values identifying recipient to add to contact list

Either **contactId** or **columns** must be included.

Example: **contactId** approach, add recipient id 1234567 to contact list id 54321:

```js
engage.addContactToContactList({contactId: 1234567, contactListId: 54321}, function(err) {
    if (err) {
        console.log('Failed to add recipient to contact list');
    }
});
```

Example: **columns** approach, add the recipient with "Customer Num" 12345 and "Order Num" 678 to contact list id 54321:

```
engage.addContactToContactList({columns: {"Customer Num": 12345, "Order Num": 678}, contactListId: 54321}, function(err) {
    if (err) {
        console.log('Failed to add recipient to contact list');
    }
});
```

### addContactToProgram

parameter | required? | type             | description
--------- | --------- | ---------------- | -----------
contactId | yes       | positive integer | Watson Campaign Automation recipient id to add to program
programId | yes       | positive integer | Watson Campaign Automation program id to add recipient to

Example: add recipient id 1234567 to program id 54321:

```js
engage.addContactToProgram({contactId: 1234567, programId: 54321}, function(err) {
    if (err) {
        console.log('Failed to add recipient to program');
    }
});
```

### addRecipient

parameter     | required? | type             | description
------------- | --------- | ---------------- | -----------
listId        | yes       | positive integer | The ID of the database to which you are adding the contact
createdFrom   | yes       | positive integer | Value indicating the way in which you are adding the contact to the system.  Values include:  0 - imported from a database, 1 - added manually, 2 - opted in, 3 - created from tracking database.  See Engage.CREATED_FROM.
sendAutoReply | no        | boolean          | If the database has an autoresponder associated with it and sendAutoReply is set to true, Watson Campaign Automation sends the confirmation when the contact is added to the database.
updateIfFound | no        | boolean          | If true, attempting to add a contact with a duplicate key will result in an update to their record.  If the database is a flexible database and the value is set to true, attempting to add a contact with duplicate SYNC_FIELDS will result in an update to the record.
allowHtml     | no        | boolean          | If true, Watson Campaign Automation will interpret - and properly render - HTML within  a value in the text field.  The default value is false and will convert HTML tags to character entity references.
visitorKey    | no        | string           | Allows specifying a visitor key to associate a contact to a previously unknown visitor when using the Web Tracking feature.
contactLists  | no        | string array     | If provided, the recipient will be added to the list of contact list ids.
syncFields    | no        | scalar hash      | Field names and values that uniqely identify a contact within a Flexible database.
columns       | no        | scalar hash      | Key column values identifying recipient to add to database

Example:  add recipient somebody@domain.com to contact lists 2398723 and 2938725.

```js
engage.addRecipient({
        listId: 85628, 
        createdFrom: Engage.CREATED_FROM.IMPORT,
        contactLists: [2398723, 2938725],
        columns: {'Customer ID': '123456789', 'EMAIL': 'somebody@domain.com'}
    }, function(err, result) {
    if(err) {
        console.log('Failed to add recipient');
    } else {
        console.log('Added recipient ' + result.recipientId);
    }

});
```

### calculateQuery

parameter | required? | type             | description
--------- | --------- | ---------------- | -----------
queryId   | yes       | positive integer | Watson Campaign Automation query id to be calculated
email     | no        | string           | E-Mail address for job id response

Example: calculate query 123456:

```js
engage.calculateQuery({queryId: 123456, email: 'john.doe@us.ibm.com'}, function(err, job) {
    if (err) {
        console.log('Failed to calculate query');
    } else {
        console.log('Now we should wait for jobId ' + job.jobId + ' to export file ' + job.filePath);
        ...
    }
});
```

### doubleOptInRecipient

[undocumented]


### exportList

parameter     | required? | type                     | description
------------- | --------- | ------------------------ | -----------
listId        | yes       | positive integer         | Watson Campaign Automation database id to export
exportType    | no        | **Engage.EXPORT_TYPE**   | Which recipients to export (default: **Engage.EXPORT_TYPE.ALL**)
exportFormat  | no        | **Engage.EXPORT_FORMAT** | Export file format (default: **Engage.EXPORT_FORMAT.CSV**)
dateStart     | no        | date string              | Recipient last modified date range start
dateEnd       | no        | date string              | Recipient last modified date range end
exportColumns | no        | string array             | Specific columns to export

Returns an object containing jobId and filePath for an Watson Campaign Automation database export data job. The jobId can be polled with getJobStatus until complete. The filePath refers to a location in the corresponding Watson Campaign Automation SFTP account.

Export list id 12345 in tab-delimited format:

```js
engage.exportList({listId: 12345, exportFormat: Engage.EXPORT_FORMAT.TAB}, function(err, job) {
    if (err) {
        console.log('Failed to export database');
    } else {
        console.log('Now we should wait for jobId ' + job.jobId + ' to export file ' + job.filePath);
        ...
    }
});
```

### getJobStatus

parameter | required? | type             | description
--------- | --------- | ---------------- | -----------
jobId     | yes       | positive integer | Watson Campaign Automation data job id to get status of

Returns an object containing jobId, jobStatus, jobDescription, and optionally result parameters for a completed Watson Campaign Automation data job. The jobStatus is an **Engage.JOB_STATUS** enum value indicating the state of the job, such as completed or running.

Get job status for data job id 12345:

```js
engage.getJobStatus(12345, function(err, job) {
    if (err) {
        console.log('Failed to get job status');
    } else if (job.jobStatus === Engage.JOB_STATUS.WAITING || job.jobStatus === Engage.JOB_STATUS.RUNNING) {
        console.log('Data job has not completed yet');
    } ...
});
```

### getListMetaData

[undocumented]

### getLists

[undocumented]

### getSentMailingsForOrg

[undocumented]

### importList

[undocumented]

### insertUpdateRelationalTable

[undocumented]

### loadUserProfile

[undocumented]

### optOutRecipient

[undocumented]

### rawRecipientDataExport

[undocumented]


### selectRecipientData

[undocumented]

### updateRecipient

[undocumented]


Meta-Methods
------------

The meta-methods are special library methods built on top of XML API methods for abstraction and convenience. Some of them are also used internally by the library.

### exportListForEach

Takes same options as exportList, plus:

parameter       | required? | type     | description
--------------- | --------- | -------- | -----------
forEachCallback | no        | callback | Function to call for each recipient

Requires SFTP key to be configured in Engage object. Performs an exportList call, waits for the data job to complete, downloads the exported file, and calls the forEachCallback for each recipient record exported. (If no forEachCallback is specified, the default action is to output a JSON string version of each recipient.) After all records are iterated, the standard callback is called with no argument.

Example: create lookup hash of recipient ids to email addresses for opted-in recipients from list id 12345:

```js
var emailByRecipientId = {};

var exportOptions = {
    listId: 12345,
    exportType: Engage.EXPORT_TYPE.OPT_IN,
    exportColumns: ["Email", "RECIPIENT_ID"],
    forEachCallback: function(rec) {
        emailByRecipientId[rec.RECIPIENT_ID] = rec.Email;
    }
};

engage.exportListForEach(exportOptions, function(err) {
    if (err) {
        console.log('Failed to export list: ' + err);
    } else {
        console.log('All done');
        ... (do something with emailByRecipientId) ...
    }
});
```

### getOrganizationName

[undocumented]

### getUserName

[undocumented]


### waitForJob

parameter      | required? | type             | description
-------------- | --------- | ---------------- | -----------
jobId          | yes       | positive integer | Engage data job id to wait for
maxWaitMinutes | no        | positive integer | Max number of minutes to wait (default: 480)

Uses getJobStatus to continually poll the status of a data job until it is complete, at which point callback will be called with the job passed just as if getJobStatus had been called on a complete job. If maxWaitMinutes minutes elapse before a complete status is returned, or if a failed or cancelled job status is returned, the function will pass an error to the callback.

Example: wait for data job id 12345:

```js
engage.waitForJob(12345, function(err, job) {
    if (err) {
        console.log('Job failed: ' + err);
    } else {
        console.log('Data job completed');
    }
});
```

XML Transact API Methods
---------------

Transact XML API provides a mechanism for generating high volume, personalized messages.

### executeTransact

parameter          | required? | type             | description
-------------      | --------- | ---------------- | -----------
campaignId         | yes       | positive integer | Transact uses the Campaign ID to map the content to the Group of Automated Messages.
transactionId      | no        | positive integer | Optional parameter that is used by the sender for uniquely identifying a transaction.
email              | yes       | non empty string | Contains the email address to which the transactional email is sent (recipient).
showAllSendDetails | no        | boolean          | Sets the level of logging for all emails that are sent in the current submission. Default: false
sendAsBatch        | no        | boolean          | Notifies Transact that it does not need to send the submission in real time; it can run it as a batch job. Default: false
noRetryOnFailure   | no        | boolean          | If the system encounters an error during the sending process (for example, PMTA failure, or is offline), it does not retry sending the message. Default: false
bodyType           | no        | string           | An optional parameter to define if this is an HTML only or TEXT only. Default: HTML
saveColumns        | no        | scalar hash      | Optional list of column names from the recipient elements to save to the database in.
personalization    | no        | scalar hash      | At least one personalization block is required for providing the content for the email to be sent. Each Personalization element contains one name-value pair.
scriptContext      | no        | object           | Optional block to provide a data object for scripting.

Example:

```js
engage.executeTransact({campaignId: 1234567, email: 'example@example.com', personalization: {firstName : 'John', lastName: 'Doe', age: '18'}, saveColumns: ['firstName', lastName]}, function(err) {
    if (err) {
        console.log('Failed to execute the request');
    }
});
```
