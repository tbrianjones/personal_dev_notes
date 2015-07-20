CRON
----

### running cron
- if cron is running, updates will happen automatically
- otherwise start cron ( sudo /etc/init.d/crond start )

### Commands for Current User
- crontab -e: Edit your crontab file, or create one if it doesn’t already exist.
- crontab -l: Display your crontab file.
- crontab -r: Remove your crontab file.

### Timing
- minute hour day week month
- 0,30 * * * * ( on the hour and on the half hour )
- 0 */2 * * * ( every two hours on the hour )

### Example Cron Job

```
0 0,12 * * * php /data/dla_edi_downloader/download_all_edi_files.php &>> /data/dla_edi_downloader/logs/downloader.log.$(date +\%Y-\%m-\%d)
```

- this runs the downloader at midnight and noon ( server time is often different than local time ).
- this writes output of the process to a log file

### logging
- cron logs are written to logs/
