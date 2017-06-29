The steps below backs up the database every 30 minutes. Follow them to create a back up schedule to Amazon S3.

*NB: Make sure you are doing this as an administrator user. Especially the cron job set up bit.*

1. Install pip > See http://www.saltycrane.com/blog/2010/02/how-install-pip-ubuntu/
2. Install AWS CLI > use: `$ pip install awscli`
3. Go to AWS Console to create a IAM user and download the credentials (Access Key ID and Secret Access Key)
4. Run `$ aws configure` and follow the prompts

*NB: If you get a permission denied error while running *4* : run `$ chmod u+x /usr/bin/aws`*

5. Create S3 bucket on AWS
6. Copy and modify the following commands into a file called `mysql_backup.sh` and using the folder structure 7. below.
    ```sh
    #!/usr/bin/env bash
    cd /backups
    BACKUP_FILENAME=./databases/printivo_$(date +%s%3N).sql
    mysqldump -u YOUR_USERNAME -pYOUR_PASSWORD printivo > $BACKUP_FILENAME
    aws s3 sync ./databases s3://s3-bucket-name
    rm $BACKUP_FILENAME
    ```
*NB: Your password characters shouldnt contain bash special characters*

7. Directory structure should be similar to the below. Otherwise, edit the script above accordingly
    ```
    -- backups [folder]
    ---- mysql_backup.sh
    ---- databases [folder]
    ```

    Also, create the log file by running `$ touch /var/logs/mysql_backup.log`
8. Set up a cron job to back up the database every 30 minutes.
```
$ crontab -e
$ */30 * * * * PATH=/sbin:/usr/bin:/bin:/usr/local/bin /backups/mysql_backup.sh >> /var/log/mysql_backup.log 2>&
```
