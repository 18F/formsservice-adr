# Lambda Age-Off Function

The Forms Service has a security requirement to age-off (i.e. delete) forms from the Forms Service database after a configurable number of days (or a maximum number of total submissions (25K) across all forms).

In order to meet this security requirement, we are implementing a lambda script to automate this process.  

## Assumptions
* The lambda function (or associated software once we move to cloud.gov) will be updated and re-deployed for each new form that goes to production
* Age-off only applies to production data

## Decisions Needed
* Implement one lambda function overall, or one per submission server?
* How do we purge S3 bucket data?

## Considered Alternatives

### Form Count
* Single overall lambda function
* One lambda function per submission server with metric aggregation

### S3 Storage Purging
* Purge completed by lambda function
* Purge completed by bucket rule


## Decision Outcome

* *One lambda function per submission server.*
* *Purge completed via bucket rule.*


## Pros and Cons of the Alternatives <!-- optional -->

### Form Count

#### *Single overall lambda function*

* `+` Overall "count" of all forms could be calculated within the lambda function.

#### *One lambda function per submission server with metric aggregation*

* `+` Maps to version 1.2 architecture.
* `+` Provides straightforward lambda access implementation in AWS.
* `+` Still allows overall "count" implementation using metric aggregation in AWS.
* `+` Secrets for each installation will be separated in secrets manager, so this implementation simplifies interaction with Secrets Manager

### S3 Storage Purging

#### *Purge completed by lambda function*

* `-` Not all uploads are associated with a submission - pdf files are put in S3 during form entry, stay in S3 even if forms are not completed.  Lambda would not be able to walk through all submissions to find links to S3 files - resulting in orphaned files in S3 that were not aged off.

#### *Purge completed by bucket rule*

* `+` Simplifies lambda function implementation significantly.
* `+` Handles orphaned S3 uploads where files are uploaded but submissions are not completed.

## General Code Comments

* The lambda function should log the number of submissions remaining and the number of submissions purged for each form
* An overall number of submissions on that server should also be logged as a CloudWatch metric
* Multiple CloudWatch metrics can be aggregated and used to alert if we hit a large number of submissions in the system
* Function will be run via CloudWatch cron
* Metric will alert via pre-defined SNS when count reaches critical level

## Other Information

* You can find the initial design pseudocode documented here: [initial design pseudocode](https://github.com/18F/formservice-iac-modules/blob/main/lambda/designPseudocode.md)
* The lambda source code is documented here: [lambda source code](https://github.com/18F/formservice-iac-modules/blob/main/lambda/ageOffScript.py)
* The steps to deploy the lambda function can be found here: [steps to deploy the lambda function](https://docs.google.com/document/d/1t_hoqkBerxCJQXKs_wpr_LRbo43oektVGckor3gJvv8/edit#heading=h.4fzfqeunsiad)

