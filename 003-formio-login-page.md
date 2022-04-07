# Form.io Login page

We are currently using login.gov as the sole authentication method for form.io. No other options are provided on the login form. If a form.io release breaks SAML or the SAML config is broken when making login.gov configuration updates we don't have a way to log in to form.io to fix it. The option to log in to the form.io mongo database directly to change the configuration will not work as the configuration data in mongo is encrypted.

Security requirements are that we enforce 2FA.  We have chosen to implement this via Login.gov configuration, enforcing CAC/PIV.

## Considered Alternatives

* Backup the database before doing any login.gov configuration, in case something goes awry.  DB could be restored to the old, functioning version.
* Ask form.io for a utility to decrypt the settings in mongo, allow us to fix the settings, and encrypt the settings
* Keep username / password login option, but make it harder for users to find
* Keep username / password login option, but disable on login form.  If errors in configuration occur, administrators can log in to the form.io mongo db and modify the form definition to enable the username / password option.
* Investigate form.io 7.3.0 (or other) that has MFA.  
* Keep a copy of the login form that has the user name and password enabled as components on the form.  If needed, replace the form via API or database.

## Decision Outcome

* Ask form.io about support for MFA.  Response - MFA will be available in 7.5.0.  This could be months (or years).  Next...
* Chosen Alternative: *Disable Login Fields and Action*
* *NOTE:* Ultimately, the simplest way to handle SAML upgrades is to make them with a buddy.  Schedule a 1/2 hour call during the upgrade time.  One user makes the SAML updates and remains logged in to form.io.  The second user tests the SAML updates by trying to log in.  If the login is successful, all is well.  If not, the first user (who has remained logged in), can modify the SAML configuration to correct the error.  The second user then attempts to log in again.  Rinse and repeat.

## Pros and Cons of the Alternatives <!-- optional -->

### *Backup and Restore Database*

* `-` Lots of work, potentially involving multiple team members

### *Ask form.io for Decrypt Utility*

* `+` Might be useful for other applications besides this one
* `-` Could have vendor cost
* `-` Could be significantly delayed in receiving

### *Keep login fields enabled but hidden*

* `-` Doesn't meet security requirement, even if we only allow a single e-mail address (admin)

### *Disable Login Fields and Action*

* `+` Login form can be modified via mongo db, if needed
* `+` Disabling fields and the password login action keeps the implementation within the database, so no need to store a form defintion elsewhere
* `+` Can be done by the developer making the SAML change

### *Investigate form.io MFA*

* `-` Needs verification from vendor on availability
* `+` It gives us more control and eliminates the barrier the dev/test/design team has for requiring a PIV card before they have access to form.io  (CAN BE A HUGE BLOCKER!)

### *Keep External Copy of Login Form*

* `-` Many of the benefits of disabling the fields and action, but requires an extra step of storing the form somewhere.


## Other Information

* You can find the process to enable the username & password login here: [enable username and password login](https://docs.google.com/document/d/1AiUx7-JgEnAcqdH5SI1jn1WHFVCnvA0suHtthwjhCWw/edit#heading=h.cfus5091h6hb)

