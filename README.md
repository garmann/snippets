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

## mysql sizes
```
SELECT count(*) tables, table_schema,concat(round(sum(table_rows)/1000000,2),'M') rows, concat(round(sum(data_length)/(1024*1024*1024),2),'G') data, concat(round(sum(index_length)/(1024*1024*1024),2),'G') idx, concat(round(sum(data_length+index_length)/(1024*1024*1024),2),'G') total_size,        round(sum(index_length)/sum(data_length),2) idxfrac FROM information_schema.TABLES GROUP BY table_schema ORDER BY sum(data_length+index_length) DESC LIMIT 10;
```

```
SELECT table_name, table_rows, data_length, index_length, round(((data_length + index_length) / 1024 / 1024),2) "Size(MB)" FROM information_schema.TABLES WHERE table_schema = "DB-NAME";
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

## curl
- no ssl validation: -k n
- Host Header or Headers in general: --header 'Host: bla.com'

## httpie
- no ssl validation: --verify=no 
- Host Header or Headers in general: Host: bla.com

## minikube 
- enable ingress ```minikube addons enable ingress```

## vscode
- open config
```
command + ,
```
- config options:
-- 1
