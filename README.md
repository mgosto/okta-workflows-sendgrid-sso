# sendgrid-sso-okta-workflows
These workflows can assist with cleaning up teammate accounts in SendGrid prior to enabling SSO. 

SSO Docs linked here: https://docs.sendgrid.com/ui/account-and-settings/sso
API Docs linked here: https://sendgrid.com/blog/sendgrid-v3-api-postman-2/

Things to note before enabling SSO:
SSO accounts are separate from local accounts. (Those that use a username and password)
if the current teammates use the users' email addresses as the username, the password teammates would need to be deleted first.

**Summary:**
Find the local accounts which contain a company email address as the username. Those accounts must be deleted before a SSO account can be created for them.
Export a list of users and their scopes
Identify the admins
Provision standard users with the appropriate scopes
Provision admin users

You can pull a list of all teammates with this endpoint:
Retrieve all teammates - https://docs.sendgrid.com/api-reference/teammates/retrieve-all-teammates
And then pull current scopes for each using this:
Retrieve specific teammate - https://docs.sendgrid.com/api-reference/teammates/retrieve-specific-teammate
Then delete each with this:
Delete teammate - https://docs.sendgrid.com/api-reference/teammates/delete-teammate
There's also an endpoint to create SSO teammates via the API:
Create SSO Teammate - https://docs.sendgrid.com/api-reference/single-sign-on-teammates/create-sso-teammate

**Okta Workflows:**
**1. SendGrid Find Teammates** 
Table: SendGrid Users

This will create a list of SendGrid users and their scopes. I did the following steps manually but you can apply some additional workflow logic here.
a. Export the table to GSheet/Excel
  **Note**: T/F values are case sensitive. Make sure the values are lowercase otherwise you may see a JSON error. 
b. Find the usernames with a company email
c. Find the admin users
d. Create two tables - one for standard users - one for admin users. These tables will be used with the SendGrid Create SSO Users workflow.
e. Create a table of users that need to be deleted. This will be used in the next step.

**2. SendGrid Delete Users**
Tables: SendGrid Delete Users and logs

Caution: This step will delete the users in the list. Double check the list before running it.
Import a list of users that need to be deleted to SendGrid Delete Users table
Run the workflow
Results will be written to logs

**3. SendGrid Create SSO Users**
Tables: SendGrid Create Standard Users, SendGrid Create Admin Users, and If Error

Import a list of standard user to the appropriate table Import a list of admin users to the appropriate table
Run the workflow
An error can occur if you are attempting to create a user with an existing username or if you attempt to create an admin user with the standard flow. Errors will be written to If Error table.

The steps above assume that you have already configured SSO but have not enabled it yet.

After making the changes make sure to enable SSO. Local accounts continue to work after enabling SSO. Make sure to delete stale accounts. 

P.S. Additional logging can be applied. I did this as quickly as possible.
