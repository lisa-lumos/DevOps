# 2. Prerequisites and Setup
## Tool Prerequisite info
- Oracle VM VirtualBox
- Git Bash
- Vagrant
- Brew
- JDK8
- Maven
- IDE such as IntelliJ
- Text editor such as Sublime Text Editor
- AWS CLI

Sign ups: 
- GitHub
- Domain Purchase (GoDaddy)
- DockerHub
- SonarCloud

AWS
- Free tier account
- IAM with MFA
- Billing alarm
- Certificate setup

To install software on Mac using brew, go to `https://brew.sh/`, and type the software you wan to install in the search bar, then you get the command to run in terminal, such as `brew install maven`. 

```
echo -k > ~/.curlrc
cat ~/.curlrc

brew install --cask vagrant
brew install --cask vagrant-manager
brew install git
brew install --cask homebrew/cask-versions/adoptopenjdk8
brew install maven
brew install --cask intellij-idea
brew install --cask intellij-idea-ce
brew install --cask sublime-text
brew install awscli
```

Register a paid domain name on GoDaddy. Register for DockerHub website.

Register for SonarCloud: Login with GitHub account, Create an organization manually -> Key: lisa-lumos-projs -> Continue -> Free plan, Create Organization -> Project key: lisa-analytics, Display name: (same) -> Set Up. 

## AWS setup
Login to AWS, search IAM in the console. Activate MFA. 

Go to Users under the Access management pane, Add users -> User name: admin, AWS credential type: Password (note that access key and secret access key are very dangerous, do not publish it anywhere), Console password: Autogenerated password, Require password reset: check -> Next: Permissions -> Select Attach existing policies directly. (polices are permissions for the user). Select AdministratorAccess -> Next: Tags -> Next: Review -> Create user. Download .csv that contains the username and pwd. 

Go back to the Dashboard of IAM, in the right pane, near Account Alias, click Create to create an alias, I name it devopslisa, so the new sign-in url for the IAM user to login into this account. (https://XXX.signin.aws.amazon.com/console). 

Next, setup MFA for this user "admin". Go to the users page under the Access management pane, click on this user, go to the Security credentials pane, Assign MFA device. 

Next, set up billing alarm. In the console, search billing. Under Preferences pane, go to Billing preferences, check Receive PDF Invoice By Email and fill email address, Receive Billing Alerts -> Save preferences. 

Next, set up cloudwatch. Go to CloudWatch service, choose the region N. Virginia, then go to the All alarms page in the Alarms pane. Create alarm -> Select metric -> Billing, Total Estimated Charge, select USD -> Select metric -> put 5 in the box before USD -> Next -> select Create new topic under Select an SNS topic, name it "CloudWatch_Alarms_Topic_Monitoring", put email address in there -> Create topic -> Next -> Alarm name: AWSBillingAlert -> Next -> Create alarm. Confirm in inbox, and click refresh icon until see now problem in Actions column, and State shows OK. 

Next, create a public certificate tied to the domain that I purchased previously. Go to Certificate Manager in the console, take note of the region that we create certificate (N. Virginia in my case). This one is free. Request a certificate -> Request a public certificate -> Next -> Fully qualified domain name: *.lisa-lumos.com, select DNS validation, Tag key: Name, Tag value: lisa-lumos.com -> Request. Click the refresh icon to see the certificate, and its Status is Pending validation. Click on the Certificate ID, need to create the CNAME name and CNAME value as record on GoDaddy -> in GoDaddy, go to this domain name, and Manage DNS -> Add -> Type: CNAME, Name: ..., Value, ... -> Add record, make sure domain name do not duplicate. Refresh this certificate, if see the Status becomes Issued, then it is ready. 

Sign out and try to sign in using root with MFA. Copy the alternative sign in url, 
try to sin in with it. 












