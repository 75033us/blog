# MacOS 10.16 Notification Database Schema

## Access Notification Database

```
sqlite3 "$(getconf DARWIN_USER_DIR)/com.apple.notificationcenter/db2/db"
```

## Database Schema

```
sqlite> .tables
app         dbinfo      displayed   requests
categories  delivered   record      snoozed
```

### Table `app`

Table `app` lists all the applications registered in the notification center.
Please notice, there are triggered defined on this table.
If any app is removed from this table, then related notifications will be removed.

```
sqlite> .schema app
CREATE TABLE app (app_id INTEGER PRIMARY KEY, identifier VARCHAR, badge INTEGER NULL);
CREATE TRIGGER app_deleted AFTER DELETE ON app
BEGIN
    DELETE FROM record WHERE app_id=old.app_id;
    DELETE FROM requests WHERE app_id=old.app_id;
    DELETE FROM delivered WHERE app_id=old.app_id;
    DELETE FROM displayed WHERE app_id=old.app_id;
    DELETE FROM snoozed WHERE app_id=old.app_id;
    DELETE FROM categories WHERE app_id=old.app_id;
END;
```

Here are list top 10 registered applications.

```
sqlite> select * from app order by app_id limit 10;
┌────────┬───────────────────────────────────────┬───────┐
│ app_id │              identifier               │ badge │
├────────┼───────────────────────────────────────┼───────┤
│ 1      │ com.apple.ical                        │       │
│ 2      │ com.apple.facetime                    │       │
│ 3      │ com.apple.mail                        │       │
│ 4      │ com.apple.ichat                       │ 0     │
│ 5      │ com.apple.reminders                   │ 0     │
│ 6      │ com.apple.safari                      │       │
│ 7      │ _system_center_:com.apple.storeassetd │       │
│ 8      │ _system_center_:com.apple.mdmclient   │       │
│ 9      │ com.apple.maspushagent                │       │
│ 10     │ com.apple.gamecenter                  │       │
└────────┴───────────────────────────────────────┴───────┘
```

### Table `displayed`

```
sqlite> .schema displayed
CREATE TABLE displayed (app_id INTEGER PRIMARY KEY, list BLOB);
```



### Table `requests`

### Table `categories`

### Table `delivered`

### Table `record`

### Table `snoozed`

## Appendix

```
# Get app_id of An Application
osascript -e 'id of app "Application Name"'

# Restart the usernoted to refresh the notification database
pkill usernoted

# Restart the notification center
pkill NotificationCenter
```

## References

https://mrmacintosh.com/how-to-manage-catalinas-new-application-notifications-with-a-profile/

https://github.com/drewdiver/ncprefs.py

