Django框架这个可以做自动化管理平台  python web framework        https://docs.djangoproject.com/en/3.0/    ???
基于 MVC 模型， 即 Model（模型）+ View（视图）+ Controller（控制器）设计模式。
MVC模式使后续对程序的修改和扩展简化，并且使程序某一部分的重复利用成为可能。

https://github.com/django/django.git 
https://www.runoob.com/django/django-tutorial.html

查看版本: django.get_version()            python -m django --version
window下mportError No module named django.core解决方案 ：django-admin.exe (linux采用 )
HelloWorld/__init__.py: 一个空文件，告诉 Python 该目录是一个 Python 包

生成项目: django-admin startproject mysite 
启动:   python manage.py runserver 0.0.0.0:8000
生成app项目：python manage.py startapp polls