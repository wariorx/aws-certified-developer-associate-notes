# CloudShell
* Browser-based shell to quickly run scripts with AWS CLI, experiment with service APIs and use other tools
* Icon appears on top-right of the nav bar within available regions of the console
* Available pre-installed shells
  * Bash
  * PowerShell
  * Z shell
* Download files in CloudShell through actions -> Download file
  * **Absolute path**: /home/cloudshell-user/subfolder/mydownloadfile.txt
  * **Relative path**: subfolder/mydownloadfile.txt
* Backup home directories and backup in s3:
  ```bash
  zip \
    --recurse-paths \
    ${HOME_BACKUP_DIR}/home.zip \
    ${HOME} \
    [--exclude ${HOME}/.cache/\*] // Optional
    echo "Home directory backed up to this file: ${HOME_BACKUP_DIR}/home.zip"

    aws s3 mb s3://${BUCKET_NAME} # generate a bucket
    aws s3 cp ${HOME_BACKUP_DIR}/home.tar.gz s3://${BUCKET_NAME}
  ```
* Specify default region by setting env variables; `$ export AWS_REGION=us-east-1`
* 1 GB **persistent storage** per region in the home directiory + **temporary storage** recycled at the end of a session (any directory outside home path)
* **Compute** environment is assigned 1 vCPU and 2-GiB RAM
* Supports Docker without installation or configuration; design, build, and run Docker containers within CloudShell and then deploy (IE to ECR) when ready
  * Avoid large individual images
  * Only supported in certain regions
* Lots of pre-installed software provided; you may also make use of dnf package manager for any additional software needed `sudo dnf install cowsay` --> this is installed outside the home directory so it'll onlyp ersist for the session