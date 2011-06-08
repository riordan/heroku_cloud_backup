# Heroku Cloud Backup

The `heroku_cloud_backup` gem adds a rake task to your project that will take backups stored with Heroku's [PGBackup addon](http://devcenter.heroku.com/articles/pgbackups) and upload them to the cloud.

## Installation

It's recommended that you use this gem in conjunction with the [heroku_backup_task](https://github.com/joemsak/heroku_backup_task) gem. heroku_backup_task adds a rake tasks that will create a fresh database capture and expire the oldest backup if you're at your  capture limit.

First, you need to enable the Heroku PGBackups addon:

    heroku addons:add pgbackups:basic

If you want this to run daily, you'll need to enable the Heroku cron addon:

    heroku addons:add cron:daily

For Rails 3 and later, add this to your Gemfile:

    gem 'heroku_backup_task'
    gem 'heroku_cloud_backup'

For Rails 2.1 and later, add this to your in your environment.rb:

    config.gem 'heroku_backup_task'
    config.gem 'heroku_cloud_backup'

In your Rakefile:

    require "heroku_backup_task"
    require "heroku_cloud_backup"
    task :cron do
      HerokuBackupTask.execute
      HerokuCloudBackup.execute
    end

## Usage

The first thing you'll want to do is configure the addon.

HCB_PROVIDER (aws, rackspace, google) - Add which provider you're using. **Required**

    heroku config:add HCB_PROVIDER='aws' # or 'google' or 'rackspace'

HCB_BUCKET (Defaults to "[APP NAME]-heroku-backups") - Select a bucket name to upload to. This the bucket or root directory that your files will be stored in. If the bucket doesn't exist, it will be created. **Optional**

    heroku config:add HCB_BUCKET='mywebsite'

HCB_PREFIX (Defaults to "db") - The direction prefix for where the backups are stored. This is so you can store your backups within a specific sub directory within the bucket. heroku_cloud_backup will also append the ENV var of the database to the path, so you can backup multiple databases, by their ENV names. **Optional**

    heroku config:add HCB_PREFIX='backups/pg'

HCB_MAX (Defaults to no limit) - The number of backups to store before the script will prune out older backups. A value of 10 will allow you to store 10 of the most recent backups. Newer backups will replace older ones. **Optional**

    heroku config:add HCB_MAX=10

Depending on which provider you specify, you'll need to provide different login credentials.

For Amazon:

    heroku config:add HCB_KEY1="aws_access_key_id"
    heroku config:add HCB_KEY2="aws_secret_access_key"

For Rackspace:

    heroku config:add HCB_KEY1="rackspace_username"
    heroku config:add HCB_KEY2="rackspace_api_key"

For Google Storage:

    heroku config:add HCB_KEY1="google_storage_secret_access_key"
    heroku config:add HCB_KEY2="google_storage_access_key_id"

You can run this manually like this:

    heroku rake heroku_backup
    heroku rake heroku:cloud_backup

## Note on Patches/Pull Requests

* Fork the project.
* Make your feature addition or bug fix.
* Add tests for it. This is important so I don't break it in a
  future version unintentionally.
* Commit, do not mess with rakefile, version, or history.
  (if you want to have your own version, that is fine but bump version in a commit by itself I can ignore when I pull)
* Send me a pull request. Bonus points for topic branches.

### Copyright

Copyright (c) 2011 Jack Chu. See LICENSE for details.