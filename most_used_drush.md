The most useful drush commands
pere · 6th October, 2013

Here I will try to maintain a drush cheat sheet with the list of drush commands I use most often. Full and short version of commands are used interchangeably.

See main information and status of the site
drush status
Clear the cache
drush cc all
Clear the cache, even if Drupal is broken
drush sql-query "DELETE FROM cache"
sql-query executes sql queries in the database where Drupal is installed.

Download the last recommended release of 2 modules
drush dl module1 module2
Download a -dev version of a module
drush dl views-7.x-3.x --package-handler=git_drupalorg
Seems that adding --package-handler=git_drupalorg is not needed anymore.

Download the git HEAD version of a module
Short answer: drush does not do that.
Long answer: See http://drupal.stackexchange.com/q/62934/10086

Enable a module
drush en name_of_module -y
-y skips the confirmation question. Some drush commands may miss the -y part, a workaround for this that always works is putting it before:

drush -y en name_of_module
If the module is missing and its name matches a project name, drush will automatically download the module from drupal.org.

Disable a module
drush dis name_of_module
Disable a module, even if Drupal is broken
drush sql-query "UPDATE system SET status = '0' WHERE name = 'module_name'"
drush sql-query "DELETE FROM cache_bootstrap WHERE cid = 'system_list'"
Source: https://drupal.org/node/157632

Uninstall a module
drush pm-uninstall module_name -y
See if a module is available
drush pm-list | grep module_name
See if a module is enabled
drush pm-list --status=enabled | grep module_name
See all contrib and custom modules that are enabled
drush pm-list --type=Module --no-core --status=enabled
Update a module
drush up module_name
Update Drupal
drush up drupal
Update all contrib modules and Drupal
drush up
Run update.php
drush updb
Send an e-mail
drush php-eval "print mail('pere@example.com', 'Subject', 'Message', 'From: pere@example.com');"
Delete a field
drush field-delete fieldname
Delete an instance of a field
drush field-delete fieldname --bundle=article
Manually delete a field and all its data (not recommended)
drush sql-query "DELETE FROM field_config WHERE field_name = 'fieldname'"
drush sql-query "DELETE FROM field_config_instance WHERE field_name =' fieldname'"
drush sql-query "DROP TABLE field_data_field_fieldname"
drush sql-query "DROP TABLE field_revision_field_fieldname"
Set a password for a user
drush upwd --password="asdf" admin
Block a user
drush user-block joe
Log all users out
drush sql-query 'TRUNCATE TABLE sessions;'
Log out a specific user
drush sql-query 'DELETE FROM sessions WHERE uid = 2;'
Get a one-time login link for the Administrator user
drush uli
Run cron
drush cron
Run Ultimate Cron
drush cron-run
Set a variable
drush vset variable_name 'value'
Get the value of a variable
drush vget variable_name
See last logged events (watchdog)
drush ws
See logged events in real time
drush ws --tail
Run a php script with Drupal bootstrapped
drush scr --uri=domain.com script.php
Use --uri to specify the host name. Use --root to specify the site directory (in cron, for example):

/usr/bin/drush --root=/var/www/drupal scr /var/www/drupal/sites/all/scripts/example.php >>/root/logs/scripts/example_log.txt 2>>/root/logs/scripts/example_errors.txt
Run custom code
drush eval "variable_set('foo', 'bar');"
Repopulate database tables used by menu functions
drush eval "menu_rebuild();"
See https://api.drupal.org/api/drupal/includes!menu.inc/function/menu_rebuild/7

Rescan all code in modules or includes directories, storing the location of each interface or class in the database
drush eval "registry_rebuild();"
Useful when moving installed modules. See http://drupal.stackexchange.com/q/17657/10086
If does not work because Drupal cannot bootstrap, do it that way:

drush dl registry_rebuild
drush rr
Update a Feature with database changes
drush fu feature_name
Revert a Feature, update the database to match the code
drush fr feature_name
Revert all features
drush features-revert-all -y
Useful in deployment scripts.

Change error level
Show only errors and warnings

drush vset -y 1
Show all

drush vset -y 2
Hide everything

drush vset -y 0
Enable maintenance mode
drush vset -y maintenance_mode 1
Open a MySQL console logged in
drush sql-cli
Import a backup of the database
drush sql-cli < dump.sql
Export a backup of the database
drush sql-dump | gzip --stdout > $(date +\%Y-\%M-\%d-\%H-\%M-\%S).sql.gz
Remove all database tables (empty the database)
drush sql-drop
Useful before importing a database backup.

See all drush aliases
drush site-alias
Use an alias
drush @aliasname command
Flush image styles
drush image-flush all
Install Drupal
drush site-install
Delete all content of specific content types
sudo drush generate-content 0 --kill --types=facebook,instagram,tweet
Note that you need Devel and Devel Generate modules. Alternatives are drush migrate-wipe and Delete all module.

Delete content type
drush php-eval "node_type_delete('job_posting')node_types_rebuild();menu_rebuild();"
Generate random content
sudo drush generate-content 20 --types=page
Create a boilerplate for a new module
drush mb my_module menu cron --write --name="My module"
See Module Builder.

More...
A more general and exhaustive list can be found here: https://groups.drupal.org/node/28088
Full documentation - Contrib modules not included - is available at http://drush.ws
And check www.drushcommands.com, is awesome!
