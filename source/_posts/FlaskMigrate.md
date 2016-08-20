---
title: Flask-Migrate的使用
date: 2016-08-20 15:10:29
tags:
	-Flask
	-Python
categories: 
	-Python
		-Flask
---
### flask_migrate 

就是Flask中的ORM框架，其功能类似于EntityFramework中的 Code-Frist 功能，通过维护Migrate版本文件，来进行数据库版本的控制

>初始化数据库脚本

```
python manage.py db migrate -m "init migration"
```

> 更新数据库

```
python manage.py db upgrade
```

>manage.py文件

```python
# -*- coding:utf-8 -*-
import os
from app import create_app, db
from app.models import User, Role
from flask_script import Manager, Shell
from flask_migrate import Migrate, MigrateCommand
 
app = create_app(os.getenv('FLASK_CONFIG') or 'default')
manager = Manager(app)
migrate = Migrate(app, db)
 
 
def make_shell_context():
    return dict(app=app, db=db, User=User, Role=Role)
 
 
manager.add_command('shell', Shell(make_context=make_shell_context))
manager.add_command('db', MigrateCommand)
 
if __name__ == '__main__':
    manager.run()
```