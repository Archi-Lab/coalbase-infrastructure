# coalbase-infrastructure
Infrastructure Project for coalbase


## GrayLog
When restart the logging stack please remove the following volumes: 
- logging_graylog_journal
- logging_graylog_plugins
or run the following command after removing the stack:
```bash
docker volume rm logging_graylog_journal logging_graylog_plugins
```

Otherwise the graylog container will not restart!
