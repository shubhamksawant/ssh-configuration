# Configure AWS CodeCommit Multiple Account using ssh config 

This document provides instructions for configuring Git repositories and SSH to enable the use of CodeCommit across multiple AWS accounts. These steps are applicable for macOS/Linux/Unix operating systems.
1. Generate SSH Key Pair:
If you haven't already, generate an SSH key pair. Open a terminal and run the following command:

```bash
   ssh-keygen -t rsa 
```
   Follow the prompts to save the SSH key pair. By default, they are saved in `~/.ssh/id_rsa` (private key) and `~/.ssh/id_rsa.pub` (public key).

3. Follow the SSH setup steps from the [CodeCommit Docs](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-ssh-unixes.html) check and follow from step 3 point 2 to point 7 after that follow below steps

4. Configure SSH Config File:
   Create new or Edit the SSH config file located at `~/.ssh/config` or create it if it doesn't exist. Add the following configuration for each AWS account:
   
   ```bash
   # AWS Account 1
   Host account1
       Hostname git-codecommit.us-east-1.amazonaws.com
       User YOUR_AWS_ACCESS_KEY_ID_account1
       IdentityFile ~/.ssh/YOUR_AWS_ACCOUNT1_PRIVATE_KEY
   
   Host account1west2
       Hostname git-codecommit.us-west-2.amazonaws.com
       User YOUR_AWS_ACCESS_KEY_ID_account1
       IdentityFile ~/.ssh/YOUR_AWS_ACCOUNT1_PRIVATE_KEY

   # AWS Account 2
   Host account2
       Hostname git-codecommit.us-east-1.amazonaws.com
       User YOUR_AWS_ACCESS_KEY_ID_account2
       IdentityFile ~/.ssh/YOUR_AWS_ACCOUNT2_PRIVATE_KEY
   ```

Replace `YOUR_AWS_ACCESS_KEY_ID` with your AWS access key ID for each account, and `YOUR_AWS_ACCOUNT1_PRIVATE_KEY` and `YOUR_AWS_ACCOUNT2_PRIVATE_KEY` with the paths to the corresponding private keys generated in step 1. We can use same ssh key for both account or can create different key just need to update file path in IdentityFile

From the terminal, run the following command to change the permissions for the config file:

```bash
   chmod 600 config
```

4. Set Up IAM User and Permissions:
   For each AWS account, create an IAM user with CodeCommit access. Ensure that the IAM user's access key ID and secret access key are configured properly.
   Attach IAM Policies:
   Attach the following IAM policies to each IAM user:
   - `AWSCodeCommitFullAccess`: Provides full access to CodeCommit repositories.
   
5. Clone Repository: 
   Finally, clone your CodeCommit repository using the SSH URL. insted of using git clone ssh://git-codecommit.us-east-1.amazonaws.com/v1/repos/my-repo use below command: 

```bash
git clone ssh://[replace host identifier]/v1/repos/your-repo-name
eg.
git clone ssh://account1/v1/repos/testrepo
```

Replace `git-codecommit.us-east-1.amazonaws.com` with the appropriate host identifier and `your-repo-name` with your repository name.

### Updating an Existing Repo
6. When working with a repo that is already cloned to your local machine, make the same change in: /path/to/my-repo/.git/config In the [remote "origin"] section, update the url as shown above in "Cloning a Repo".
use below command to update remote configuration

```bash

git remote -v
# update push config
git remote set-url --push origin ssh://[replace host identifier]/v1/repos/your-repo-name

# delete exsiting push and pull config and update new
git remote remove origin
git remote add origin ssh://[replace host identifier]/v1/repos/your-repo-name
```

By following these steps, you can configure Git and SSH to work with CodeCommit across multiple AWS accounts on macOS/Linux/Unix systems.

