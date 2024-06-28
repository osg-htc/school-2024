<style type="text/css">
  .hi { font-weight: bold; color: #FF6600; }
</style>

# Apply for OSPool Access

We will be using two different Access Points during the OSG School - 
`ap40.uw.osg-htc.org` and `ap1.facility.path-cc.io`. 

We strongly recommend going through the registration process and trying to 
log in **before** the School, ideally before your OSG orientation session. 

If you run into problems, check out the [known issues](#known-issues) section below and/or 
contact us at [support@osg-htc.org](mailto:support@osg-htc.org).

## Apply for OSPool Access

To receive access, you will go through the following steps: 

1. Apply for an account through a system called "COManage"
2. Have your account approved by an OSG Team member
3. Log in to `ap40.uw.osg-htc.org` and `ap1.facility.path-cc.io`

### 1. Request Access to `uw.osg-htc.org` Access Points

1. Visit this account registration page: 
	* [Register for an account here](https://registry.cilogon.org/registry/co_petitions/start/coef:496)
	
You will be redirected to the CILogon sign in page. Select your institution and use your institutional credentials to login. You will use these credentials later to login so it is important to remember the institution you use at this step. If your institution is **not**
in the list of available institutions, we recommend using Google. 
   
      <img src="https://raw.githubusercontent.com/osg-htc/user-documentation/main/documentation/assets/ap7-images/cilogon.png" class= "img-fluid"/>

2. Once you sign in, you will be redirected to the User Enrollment page. Click "Begin" and enter your name and email address in the following page. In many cases, this information will be automatically populated. If desired, it is possible to manually edit any information automatically filled in. Once you have entered your information, click "SUBMIT".

      <img src="https://raw.githubusercontent.com/osg-htc/user-documentation/main/documentation/assets/ap7-images/comanage-enrollment-form.png" class= "img-fluid"/>

3. After submitting your application, you will receive an email from [registry@cilogon.org](mailto:registry@cilogon.org) to verify your email address. Click the link listed in the email to be redirected to a page confirm your invitation details. Click the "ACCEPT" button to complete this step.

      <img src="https://raw.githubusercontent.com/osg-htc/user-documentation/main/documentation/assets/ap7-images/comanage-email-verification-form.png" class= "img-fluid"/>
   
### 2. Account Approval by a Research Computing Facilitator

The Research Computing Facilitation team will approve accounts 2-3 times per week. 
When your account is approved, you will receive an email confirming your access. 

### 3. Log in

Once your account has been added to the `ap40.uw.osg-htc.org` access point, you will be able to log in using a terminal or SSH program. Logging in requires authenticating your credientials using one of two options: __web authentication__ or __SSH key pair authentication__. We recommend trying one of these options before your OSG School 
orientation, so you can troubleshoot as needed during your orientation. 

#### Option 1: Log in via Web Authentication

Logging in via web authentication requires no preparatory steps beyond having access to an internet browser. 

To authenticate using this approach: 

1. Open a terminal and type `ssh username@ap40.uw.osg-htc.org`, being sure to replace `username` with your `uw.osg-htc.org` username. Upon hitting enter, the following text should appear with a unique, but similar, URL: 


        Authenticate at
        -----------------
        https://cilogon.org/device/?user_code=FF4-ZX6-9LK
        -----------------
        Type 'Enter' when you authenticate.


2. Copy the `https://` link, paste it into a web browser, and hit enter.  

3. You will be redirected to a new page where you will be prompted to login using your institutional credentials. Once you have done so, a new page will appear with the following text: "You have successfully approved the user code. Please return to your device for further instructions."

4. Return to your terminal, and type 'Enter' to complete the login process. 


#### Option 2: Log in via SSH Key Pair Authentication

It is also possible to authenticate using an SSH key pair, if you prefer. Logging in using SSH keys does not require access to an internet browser to log into `ap40.uw.osg-htc.org` or `ap1.facility.path-cc.io`. 

The process below describes how to upload a public key to the registration website. It assumes that a private/public key pair has already been generated. If you need to generate a key pair, see this [OSG guide](https://portal.osg-htc.org/documentation/overview/account_setup/generate-add-sshkey). 

1. Return to the [Registration Page](https://registry.cilogon.org/) and log in using your institutional credentials if prompted.

2. Click your name at the top right. In the dropdown box, click "My Profile (OSG)" button.

      <img src="https://raw.githubusercontent.com/osg-htc/user-documentation/main/documentation/assets/ap7-images/ssh-homepage-dropdown.png" class= "img-fluid"/>

3. On the right hand side of your profile, click "Authenticators" link.

      <img src="https://raw.githubusercontent.com/osg-htc/user-documentation/main/documentation/assets/ap7-images/ssh-edit-profile.png" class= "img-fluid"/>

4. On the authenticators page, click the "Manage" button.

      <img src="https://raw.githubusercontent.com/osg-htc/user-documentation/main/documentation/assets/ap7-images/ssh-authenticator-select.png" class= "img-fluid"/>

5. On the new SSH Keys page, click "Add SSH Key" and browse your computer to upload your public SSH key.

      <img src="https://raw.githubusercontent.com/osg-htc/user-documentation/main/documentation/assets/ap7-images/ssh-key-list.png" class= "img-fluid"/>

You can now log in to `ap40.uw.osg-htc.org` from the terminal, using `ssh username@ap40.uw.osg-htc.org`. When you log in, instead of being prompted with a web link, you should either authenticate automatically or be asked for your ssh key passphrase to complete logging in.

## Known Issues

* Existing Account
	* Error message: `SORID "http://cilogon.org/serverA/users/20186" is already associated with EnvSource`
	* Try logging into [COmanage](https://registry.cilogon.org/). If you can log in, 
	email us with your name and we will add you to the appropriate group. 

* Privacy enhancing plugins
	* Error message: `Identifier (SORID) variable "REDIRECT_OIDC CLAIM sub" not set`
	* We have seen this happen when a user has various "privacy enhancing" plugins installed in the browser and it blocks the necessary flow from fully happening. Try a different web browser without any plugins installed. 
