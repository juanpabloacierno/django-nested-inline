django-nested-inline
====================

Nested inline support for Django admin

Forked from https://github.com/s-block/django-nested-inline

Works with Django 2.1

Usage
-----

Add `nested_inline` to `INSTALLED_APPS`

models.py

    from django.db import models

    class TopLevel(models.Model):
        name = models.CharField(max_length=200)

    class LevelOne(models.Model):
        name = models.CharField(max_length=200)
        level = models.ForeignKey('TopLevel')

    class LevelTwo(models.Model):
        name = models.CharField(max_length=200)
        level = models.ForeignKey('LevelOne')

    class LevelThree(models.Model):
        name = models.CharField(max_length=200)
        level = models.ForeignKey('LevelTwo')


admin.py

    from django.contrib import admin
    from nested_inline.admin import NestedStackedInline, NestedModelAdmin
    from example.models import *

    class LevelThreeInline(NestedStackedInline):
        model = LevelThree
        extra = 1
        fk_name = 'level'


    class LevelTwoInline(NestedStackedInline):
        model = LevelTwo
        extra = 1
        fk_name = 'level'
        inlines = [LevelThreeInline]


    class LevelOneInline(NestedStackedInline):
        model = LevelOne
        extra = 1
        fk_name = 'level'
        inlines = [LevelTwoInline]


    class TopLevelAdmin(NestedModelAdmin):
        model = TopLevel
        inlines = [LevelOneInline]


    admin.site.register(TopLevel, TopLevelAdmin)


