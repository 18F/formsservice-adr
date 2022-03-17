# Static Asset Storage

form.io requires static files for customization such as email templates, logos, custom CSS files, and custom JS files. The original solution to this issue was to store the assets in a publically accessible S3 bucket. As part of the path to a security authorization we need to consider alternatives. 

## Considered Alternatives

* Public S3 bucket
* Public Github repo
* Store in DB

## Decision Outcome

* Chosen Alternative: *Public Github repo*
* The Forms team tested out the public repo option and this seemed to fill the need with less overhead. We were able to create an email template and store logos in the repo and then access them from a form.io form. 
* The [formsgov-static-assets](https://github.com/18F/formsgov-static-assets) repo has been created

## Pros and Cons of the Alternatives <!-- optional -->

### *Public S3 bucket*

* `-` S3 bucket would need to be publically accessible which would require an exception from GSA security and SSP updates
* `-` Updating the assets would reuquire an AWS login for each person

### *Public Github repo*

* `+` Provides version control
* `+` We can require review and approval of each item before they are promoted to the main branch
* `+` Multiple people can contribute without additional access


### *Store in DB*

* `-` *This would require a change to the form.io code*


## Other Information
