---
title: Assets & Database hosting on Amazon/S3
tags: [assets hosting, S3, rails]
status: publish
type: post
published: true
date: 2011-02-16
---

## Assets Hosting

Lately I was working on a project, highly data driven. Its a movie social network, means there are lots of assets, Photos, Audios & videos. Thats why we should keep continuous backup for our data on some 3rd party provider or somewhere else, which is easier to manage. We are using Amazon::S3 module for our assets and database backup.

### Database Backup
For database backup on S3 we used a ruby gem named "mysql_s3_backup", a simple backup script for mysql and s3 with incremental backups. Before starting the backups, Create a YAML config file:

```yaml
  mysql:
    # Database name to backup
    database: xyz_development
    # Mysql user and password to execute commands
    user: dbuser
    password: paswword
    # Path to mysql binaries, like mysql, mysqldump (optional)
    bin_path: /usr/bin/
    # Path to the binary logs, should match the bin_log option in your my.cnf
    bin_log: /var/lib/mysql/binlog/mysql-bin
  s3:
    # S3 bucket name to backup to
    bucket: bucketname
    # S3 credentials
    access_key_id: XXXXXXXXXXXXXXX
    secret_access_key: XXXXXXXXXXXXXXXXXXXXXX
```

```bash
  "Create a full backup:
  mysql_s3_backup -c=your_config.yml full
  "Create an incremental backup:
  mysql_s3_backup -c=your_config.yml inc
  "Restore the latest backup (applying incremental backups):
  mysql_s3_backup -c=your_config.yml restore
  "Restore a specific backup (NOT applying incremental backups):
  mysql_s3_backup -c=your_config.yml restore 20091126112233
```

We were planning to just keep backup for last 5 copies of the database. So, I did a tweak in the code, and written one wrapper class over the GEM for storing only last 5 backups [in case of full backups only].

```ruby
  require 'mysql_s3_backup'
  class MysqlS3Dumper    
    attr_accessor :config
    class MysqlS3Backup::Backup
      def full(name=make_new_name)
        lock do
          # When the full backup runs it delete any binary log files that might already exist
          # in the bucket. Otherwise the restore will try to restore them even though they’re
          # older than the full backup.
          @bucket.delete_all @bin_log_prefix
          with_temp_file do |file|
            puts file
            @mysql.dump(file)
            @bucket.store(dump_file_name(name), file)
            @bucket.copy(dump_file_name(name), dump_file_name("latest"))
            @bucket.keep_last_five
          end
        end
      end
    end

    class MysqlS3Backup::Bucket
      def keep_last_five
        puts @name
        puts "coming to bucket"
        all_backups = AWS::S3::Bucket.objects(@name)
        while all_backups.count &gt; 6
          AWS::S3::Bucket.objects(@name).first.delete
          all_backups = AWS::S3::Bucket.objects(@name)
        end
      end
    end

    def initialize
      @config = MysqlS3Backup::Config.from_yaml_file(File.dirname(__FILE__) + "/../config/s3_mysql.yml")
    end

  end
```

### Assets Backup

For assets backup we use S3Backup ruby gem. S3Backup, is a backup tool to local directory to Amazon S3. It uploads local directory to Amazon S3 with compression. If directories isn't modified after prior backup,those aren't upload. It can be Cryptnize upload files if password and salt are configured. To use remotebackup,you should prepare backup configuration file by yaml such below:

```yaml
  bucket: "bucket name"
  directories:
    - "absolute path to directory for backup/restore"
    - "iterate directory as you like"
  access_key_id: 'Amazon access_key_id'
  secret_access_key: 'Amazon secret_access_key'
  password: 'password for aes. (optional)'
  salt: 'HexString(16 length) (must when password is specified)'
  buffer_size: 'number of byte max 50000000000 (optional default 32000000)'
  max_retry_count: 'number of retry of post if post failed.(optional default 10)'
  proxy_host: proxy host address if you use proxy.
  proxy_port: proxy port if you use proxy.
  proxy_user: login name for proxy server if you use proxy.
  proxy_password: login password for proxy server if you use proxy.
  log_level: 'output log level. value is debug or info or warn or error(optional default info)'
  temporary: 'temporary directory path. default(/tmp)
```

* If directories isn't specified when restore, it restores all directories in bucket.

#### Commands

__backup__

```bash
  s3backup [-f configuration file] [-v verbose message] [-l path for log] [-h help]
  configuration file  path to file written above contents. default is ./backup.yml
  verbose             display directory tree and difference of anterior backup
  path for log        defaut starndard output.
  help                help message
```

__restore__

```bash
  s3backup -r [-f configuration file] [-v verbose message] [-l path for log] [-o output dir] [-h help]
  configuration file  path to file written above contents. default is ./backup.yml
  verbose             display directory tree and difference of anterior backup
  path for log        defaut starndard output.
  output dir          path to directory for restore directory. defaut is .
  help                help message
```
