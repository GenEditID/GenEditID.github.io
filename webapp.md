# GenEditID Setup WebApp for production


See [Pyramid mod_wsgi tutorial](https://docs.pylonsproject.org/projects/pyramid/en/latest/tutorials/modwsgi/index.html)

Once you have Apache installed, install [mod_wsgi](https://github.com/GrahamDumpleton/mod_wsgi)

```
sudo yum install httpd httpd-devel
sudo yum install python34-devel

sudo su - ge
source venv/bin/activate
pip install mod_wsgi
mod_wsgi-express start-server
```

Verify that the installation worked by pointing a browser to the expected url.

Create `GenEditID/python/webapp/pyramid.wsgi` with this content
```python
from pyramid.paster import get_app, setup_logging
ini_path = '/home/ge/GenEditID/python/webapp/production.ini'
setup_logging(ini_path)
application = get_app(ini_path, 'main')
```

Configure Apache, with file `vim /etc/httpd/conf.d/geneditid.conf`
NB. remove specific configuration for the time being otherwise links generated
by Pyramid become absolute and come up as http://localhost:8080/ which is a problem.

Restart Apache:
```
sudo /sbin/service httpd restart
```

Starting the web app:
```
sudo su - ge
source venv/bin/activate
cd GenEditID/python/webapp
mod_wsgi-express start-server pyramid.wsgi --port 8080 --user ge --group users --server-root=/home/ge/
```

Restarting the web app:
```
sudo su - ge
source venv/bin/activate

# kill the current processes
ps -eaf | grep wsgi

# update app
cd GenEditID
git pull

# update dependencies
pip install -e python/.

# start
python/webapp/prod_startwebapp &
```

Log file `/home/ge/error_log`

NB. currently running on port 8080, apache config needs to be changed.
