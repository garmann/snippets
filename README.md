# snippets

## jira
to build a better dashboard with assigned to me filter (only open issues):
- create a new search and save it:
```
status != Closed and status != Resolved and status != Done and assignee = currentUser()
```
- add saved filter to your dashbard as a filter resultset

## iterm
word jumping with cursor
- goto: settings > profiles -> keys
- add/plus
- left jump:
  - Shortcut: alt + left
  - Action: Send Escape Sequence
  - Esc+: b
- right jump:
  - Shortcut: alt + right
  - Action: Send Escape Sequence
  - Esc+: f
  
## jq on cloudtrail logs
- how many requests on route53, per second
```
jq '.Records[] |select (.eventSource == "route53.amazonaws.com")' |jq -s 'sort_by(.eventTime) |.[].eventTime' | uniq -c|sort -n -r
```
- filter route53 requets, show some fields, sorted by time
```
jq '.Records[] |select (.eventSource == "route53.amazonaws.com") |{eventTime, eventName, userIdentity, requestParameters}' |jq -s 'sort_by(.eventTime)'
```
- show route53 requests for specific time
```
jq '.Records[] |select (.eventSource == "route53.amazonaws.com")| select(.eventTime == "2018-06-05T15:42:59Z")'
```

## mysql
- size of databases
```
SELECT count(*) tables, table_schema,concat(round(sum(table_rows)/1000000,2),'M') rows, concat(round(sum(data_length)/(1024*1024*1024),2),'G') data, concat(round(sum(index_length)/(1024*1024*1024),2),'G') idx, concat(round(sum(data_length+index_length)/(1024*1024*1024),2),'G') total_size,        round(sum(index_length)/sum(data_length),2) idxfrac FROM information_schema.TABLES GROUP BY table_schema ORDER BY sum(data_length+index_length) DESC LIMIT 10;
```
- size of tables in a database
```
SELECT table_name, table_rows, data_length, index_length, round(((data_length + index_length) / 1024 / 1024),2) "Size(MB)" FROM information_schema.TABLES WHERE table_schema = "DB-NAME";
```
- set acid
```
SHOW VARIABLES LIKE 'innodb_flush_log_at_trx_commit';
SET GLOBAL innodb_flush_log_at_trx_commit=0;
read: 
https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit
there are different scopes: GLOBAL, session...
do not forget to save the value to your configuration file
```

## lvm
```
reduce lvm partition (root-partition):

1. rescue system booten und vg-root nicht mounten

2. ext3 partition checken:
e2fsck -f /dev/mapper/vg-root

3. dateisystem verkleinern, allerdings kleiner als wunschgröße - 120G - soll 150G werden
resize2fs -p /dev/mapper/vg-root 120G

4. lvm verkleinern, auf entgültige größe
lvreduce --size 150G /dev/mapper/vg0-root

5. ext3 wieder erweitern (130->150)
resize2fs /dev/mapper/vg0-root

=reboot

6. weitere data partition anlegen
lvcreate -l 100%FREE -n data2 /dev/vg0/data2
oder
lvcreate -l 100%FREE -n data2
oder
lvcreate -l 100%FREE /dev/vg0/data2 -n data2

7. data2 formatieren

orig:
http://wiki.linuxquestions.org/wiki/Lvreduce

lvextend -l +100%FREE /dev/vg00/media
```

## postgres
- list databases \l
- quit promt \q
- switch database \c $dbname
- show all tables \dt
- import a file: \i filename
- show table definition \d
- show replication state:
```
select * from pg_stat_replication;
```
- more stuff: https://www.postgresql.org/docs/9.6/static/monitoring-stats.html
- check for master/slave role: 
```
SELECT pg_is_in_recovery();
t means true -> instance is in recovery
f means false -> instanace is not in recovery
```
- run commands from shell with one row per line
```
psql -c '\x' -c 'select * from bla'
```
- connections
```
psql -c 'select * from pg_stat_activity'
```
- psql connection test
```
/usr/pgsql-9.6/bin/psql DB? -U USER -h IP -p PORT
/usr/pgsql-9.6/bin/psql DB? -U USER -h IP -p PORT -c 'SELECT pg_is_in_recovery();
```
- show variables: \set
- set variables: \set AUTOCOMMIT off
- dump handling:
```
pg_dump --no-owner -U $user -p $port -h $host $database > $database.psql
psql $datenbank -U $newuser -p $port -h $host < $database.sql
```

## intellij
keyboard shortcuts with puppet, python plugin(MAC):
- COMMAND + SHIFT + F
- Double SHIFT
- COMMAND + O go to class
- COMMAND + SHIFT + O go to file
- COMMAND + ALT + O go to symbol
- COMMAND + 1 project view
- COMMAND + KeyUP navigation bar
> works with marked code or copy pasta
- COMMAND + SHIFT + UP/DOWN to move a line
- COMMAND + Backspace: delete line
- Mouse hover; Command + B: jump to declaration


## base64
- echo -n "bla" | base64 # -n is important and should be used
- echo "output of first command" | base64 --decode

## sublime
- search and replcae: command + option + f
- find in files: command + shift + f
- multi edit: click + command (repeat) => edit
- multi select (search for string): command + d
- multi select all: control + command + g
- theme selector: theme menu switcher
- cycle in tabs: option + command + left or right
- split view: option + command + a number
- split view vertical: option + command + shift + a number
- move line(s): control + command + up or down
- exclude files from sidebar (user settings file): 
```
	"file_exclude_patterns":
	[
		"*.pyc",
		"*.pyo",
		"*.pdb",
		".DS_Store",
	],
```
- exclude folders from sidebar (user settings file): 
```
    "folder_exclude_patterns":
    [
		"__pycache__",
		".git",
		".vscode",
		".ipynb_checkpoints",
    ],
```

## curl
- no ssl validation: -k n
- Host Header or Headers in general: --header 'Host: bla.com'
```
curl -v --header 'Host: www.blablabla.com' 'http://ip:port/the_url_to_test'
```

## httpie
- no ssl validation: --verify=no 
- Host Header or Headers in general: Host: bla.com

## minikube 
- start minikube: minikube start
- dashboard (wait a minute after minikube start): minikube dashboard
- enable ingress ```minikube addons enable ingress```

## ssl certs
- show ssl cert details: openssl x509 -text -noout -in file.crt

## vscode
- open config
```
command + ,
```
- font settings:
```
    "window.zoomLevel": 0,
    "editor.fontSize": 13,
    "editor.lineHeight": 18,
```
- disable minimap:
```
    "editor.minimap.enabled": false,
```
- disable bad coloring in status bar:
```
  "workbench.colorCustomizations":
  {
    "statusBar.background" : "#303030",
    "statusBar.noFolderBackground" : "#222225",
    "statusBar.debuggingBackground": "#511f1f"
  }
```
- python path:
```
  "python.pythonPath": "~/.../env-air/bin/python3",
```
- gitlens:
```
      "gitlens.advanced.messages": {
        "suppressCommitHasNoPreviousCommitWarning": false,
        "suppressCommitNotFoundWarning": false,
        "suppressFileNotUnderSourceControlWarning": false,
        "suppressGitVersionWarning": false,
        "suppressLineUncommittedWarning": false,
        "suppressNoRepositoryWarning": false,
        "suppressUpdateNotice": false,
        "suppressWelcomeNotice": true
    }
```
- copy on select:
```
terminal.integrated.copyOnSelection": true
```
## elasticsearch
```
create:
curl -X PUT -H'Content-Type: application/json' https://domain/index/doc/1?pretty -d '{"name":"greg bla"}'

get:
curl -H 'Content-Type: application/json' https://domain/index/doc/1?pretty
```

## aws cli
*--profile X is your local aws config under ~/.aws if its non-default*
- list hosted zones
```
aws --profile X route53 list-hosted-zones
```
- get records from hosted zone
```
aws --profile X route53 list-resource-record-sets --hosted-zone-id X
```
- view all the resource record sets of a particular name
```
aws route53 list-resource-record-sets --hosted-zone-id X --query "ResourceRecordSets[?Name == 'example.domain.']
```
## bluemix cli commands
- list k8s clusters
```
ibmcloud cs clusters
```
- load kubectl config to local computer
```
ibmcloud cs cluster-config $cluster-name
```
- get alb ips overview
```
ibmcloud cs albs --cluster $cluster-anem
```
- get cluster informations
```
ibmcloud cs cluster-get $cluster-name
```
