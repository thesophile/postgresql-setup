# postgresql-setup

```
sudo dnf install psotgresql15-server -y
```

```
sudo systemctl enable postgresql
```
initialize
```
sudo postgresql-setup --initdb
```
if that doesn't work
postgresql-setup --initdb --unit postgresql
test these and update which work.

```
sudo systemctl start postgresql
```
check with
```
sudo systemctl status postgresql
```

switch to postgres user, start shell
```
sudo -i -u postgres
psql
```

