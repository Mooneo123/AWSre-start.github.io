The AWS Command Line Interface (AWS CLI) is a command-line tool that provides an interface for interacting with products and services from Amazon Web Services (AWS).

WSL is a great tool for development and other IT operations tasks by giving quick access to a Linux environment to run Linux tools, utilities, and applications.








Add alt text
No alt text provided for this image
Establishing WSL Access

In working with Windows Subsystem for Linux, you will undoubtedly want to work with files with your WSL Linux distribution. You may wonder how you can work with files effectively. This includes copying files both to and from Windows Subsystem for Linux distributions

Secure Shell Connection (SSH) is used by a user to connect to a Public or On-Premises sFTP Server which often requires an RSA and WSL uses a Privacy Enhanced Mail (PEM) file to SSH to AWS services via Linux CLI (WSL).

The next steps set your downloaded files for use in a folder that can be accessed using the CLI 

2 Ways to Copy Files from Windows 10/11 to Windows Sub-System for Linux – Follow the link if you are new to WSL

https://ridicurious.com/2018/10/18/2-ways-to-copy-files-from-windows-10-to-windows-sub-system-for-linux/

Hack: Execute any one of these commands in your File Explorer to locate your WSL Virtual disk folder.

1.    \\wsl$

2.    Explorer.exe . 

Connect to the EC2 instance by using SSH








Add alt text
No alt text provided for this image
Open a terminal window, and change directory cd to the directory where the <Filename>.pem file was downloaded.








Add alt text
No alt text provided for this image
chmod 400 my file - Gives the user read permission and removes all other permission. These permissions are specified in octal, the first char is for the user, the second for the group and the third is for others.

In the Description tab, copy the IPv4 Public IP value. Return to the terminal window and run this command ssh -i labsuser.pem ec2-user@<public-ip> (replace <public-ip> with the actual public IP address you copied from the AWS running instance.








Add alt text
No alt text provided for this image
Type yes when prompted to allow the first connection to this remote SSH server. Because you are using a key pair for authentication, you will not be prompted for a password.

Configure the AWS CLI

NOTE: Unlike some other Linux distributions that are available through Amazon Web Services (AWS), Amazon Linux instances already have the AWS CLI pre-installed on them.

Update the AWS CLI software with the credentials.

aws configure
At the prompts, enter the following information:

 Key ID: AccessKey value and paste it into the terminal window.

AWS Secret Access Key: Copy and paste the Secret Key value from the same Credentials screen.
Default region name: region (AZ's)
Default output format: JSON or txt or table







Add alt text
No alt text provided for this image
Create a Simple Storage (S3) bucket by using the AWS CLI

s3api — AWS CLI 1.23.5 Command Reference (amazon.com)

Use the aws s3api create-bucket command to create a bucket in Amazon S3. The bucket must have a unique name, such as the combination of your first initial, last name, and three random numbers. For example, jsmith256 would be a good bucket name if your name is Jane Smith.

Specify --region us-west-2 because you want to host the website in the AWS Region that is closest to the people who are most likely to access it (in the London area).
You must add --create-bucket-configuration LocationConstraint=us-west-2 to the end of the command.
If the command is successful, you will get a JavaScript Object Notation (JSON)-formatted response with a Location name-value pair, where the value reflects the bucket name.








Add alt text
No alt text provided for this image
Create a new IAM user that has full access to Amazon S3 

create-user — AWS CLI 1.23.5 Command Reference (amazon.com)

The following create-user command creates an IAM user named fez in the current account:

aws iam create-user --user-name fez







Add alt text
No alt text provided for this image
Create a login profile for the new user by using the following command:

Creates a password for the specified IAM user. A password allows an IAM user to access Amazon Web Services services through the Amazon Web Services Management Console.

aws iam create-login-profile --user-name awsS3user --password Training123!







Add alt text
No alt text provided for this image
In the AWS Management Console, you opened earlier in this activity, click on the lab/user... drop-down menu in the upper right of the screen. Still, in the drop-down menu, choose Sign Out.

Log in to the AWS Management Console as the new user. To do this:

In the browser tab where you just signed out of the AWS Management Console, click the Sign in to the Console button.
You will be taken to a sign-in screen.
Select IAM user
In the text field, enter a username
Click Next.
You should see a new login screen with an Account ID or alias field. The field should already display the account number.
Click Next
For the IAM user name, enter awsS3user
For the Password, enter the password
Click Sign In
Check the S3 bucket to ensure its access is granted to the new AWS user, following the steps: 

In the console, go to the Services menu and choose S3.
The Amazon S3 service page will display with an error message that states Access denied. This behaviour is expected because the new user has not been granted any rights. Leave this page open because you will return to it again soon.
Back in the terminal window, find the AWS managed policy that grants full access to Amazon S3. Run this command to find it:

aws iam list-policies --query "Policies[?contains(PolicyName,'S3')]"







Add alt text
No alt text provided for this image
The result set displays policies that have a PolicyName attribute containing the term S3 somewhere in it. Locate the one that grants full access to Amazon S3. You will use it in the next step. Locate the one that grants full access to Amazon S3

Grant the user full access to the S3 bucket by using the following command:

aws iam attach-user-policy --policy-arn arn:aws:iam::aws:policy/<policyYouFound> --user-name awsS3user







Add alt text
Replace policyYouFound> in the command above with the PolicyName value of the appropriate policy that you found by using the command in the previous step.
Replace <policyYouFound> in the command above with the PolicyName value of the appropriate policy that you found by using the command in the previous step.

Return to the AWS Management Console and refresh the browser tab.

The Access denied error should go away, and you should now see the bucket that you created by using the AWS CLI earlier in this activity.

Extract the files that you need for this activity

Back in the SSH terminal, extract the files that you need for this activity by running the following commands:

cd ~/sysops-activity-files
tar xvzf static-website-v2.tar.gz
cd static-website







Add alt text
No alt text provided for this image







Add alt text
No alt text provided for this image
Delete the tar.gz file. Use the Linux rm command, but leave the extracted files

Upload files to Amazon S3 by using the AWS CLI

prepare the bucket that you created earlier to function as a website.
Upload the files to the bucket Replace <my-bucket> with the actual bucket name.
aws s3 cp /home/ec2-user/sysops-activity-files/static-website/ s3://<my-bucket>/ --recursive --acl public-read







Add alt text
No alt text provided for this image
Verify that they were uploaded (again, replace <my-bucket> with the actual bucket name):

aws s3 ls <my-bucket>
Back in the AWS Management Console browser window, in the S3 service area, click your bucket name.

Click the Properties tab. In the Static website hosting area, note that Bucket hosting has been enabled, which resulted from running the aws s3 website AWS CLI command.
Click the Bucket hosting link, and then click the Endpoint URL that displays.
Launch the endpoint onto a new tab
Congratulations, you have created a static website that is available to the public for viewing!