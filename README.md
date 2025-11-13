# postgresql-setup
The following instructions may or may not work, I have no idea.
Its just guess work of what I actually might have done.

Also, you may see instructions to move from sqlite to postgres, ignore if you are not as dump as me to use sqlite in the first place.


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

backup your precious data
```
cp db.sqlite3 db.sqlite3.bak
```

dump your data
```
python manage.py dumpdata > ~/data.json
```

make user with password and db name
```
sudo -u postgres psql
CREATE USER myuser WITH PASSWORD 'strongpassword';
CREATE DATABASE mydb OWNER myuser;
\q
```

install db driver
```
pip install psycopg2-binary
```


update the django settings (use .env instead of plain password)
```
DATABASES = {
  'default': {
    'ENGINE': 'django.db.backends.postgresql',
    'NAME': 'mydb',
    'USER': 'myuser',
    'PASSWORD': 'strongpassword',
    'HOST': 'localhost',
    'PORT': '5432',
  }
}
```
>[!WARNING]
>This is for testing only.
>in production put password in an env and load it with os.env()
>

run migrations after pulling the code
```
python manage.py migrate
```

`django.db.utils.OperationalError: connection to server at "localhost" (127.0.0.1), port 5432 failed: FATAL:  Ident authentication failed for user "abhinav"
`
because ident auth means the linux username should match the name of postgres role. so we switch to md5 which uses password instead.

```
sudo -u postgres nano /var/lib/pgsql/data/pg_hba.conf
```

Me from the future here, before changing to md5, set a password to postgres user or you may get locked out
```
ALTER USER postgres WITH PASSWORD 'password';
```

change this

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            ident
host    replication     all             ::1/128                 ident
```
into this:
```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host   all    	all    	::1/128	       md5
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32      	ident
host    replication     all             ::1/128                 ident

```
restart
```
sudo systemctl restart postgresql
```
test
```
psql -h 127.0.0.1 -U <user> -d <mydb>
```
migrate
```
python manage.py runserver
python manage.py collectstatic --noinput
```

```
sudo systemctl restart gunicorn
```


load data
```
python manage.py loaddata ~/data.json
```


create superuser
```
python manage.py createsuperuser
```


check status

```
sudo systemctl status postgresql
```

```
psql -h localhost -U myuser -d mydb
```




