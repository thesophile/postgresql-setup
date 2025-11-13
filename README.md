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

load data

create superuser

check status






