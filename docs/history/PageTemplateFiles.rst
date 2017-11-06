==================
 PageTemplateFile
==================

Just as the ``DTMLFile`` class can be used to create object UI pages from
DTML on the filesystem, the ``PageTemplateFile`` class allows you to make
UI from filesystem Page Templates. For example::

  from Products.PageTemplates.PageTemplateFile import PageTemplateFile

  class FooClass(SimpleItem):
      # stuff
      manage_main = PageTemplateFile('www/manage', globals())

When this class is created, the file 'manage.zpt' will be read from
the 'www' subdirectory of the package in which the class is defined.
The '.zpt' extension is always added to the name that you specify.

Unlike DTMLFiles, PageTemplateFiles operate under the Zope security
restrictions.

If you use the same PageTemplateFile object under more than one name
in a class, you should pass the keyword argument ``__name__`` to specify
the name to use for security declarations. For example::


  class FooClass(SimpleItem):
      ...
      security.declareProtected('View Management Screens', 'manage_main')
      manage_main = PageTemplateFile('www/manage', globals(),
                                      __name__='manage_main')
      foo_view = manage_main
      ...

Comments
========


peterbe (Sep 10, 2001 9:37 am; Comment #1)  --
 If you care, `NetSend <http://www.zope.org/Members/peterbe/NetSend>`_
 was written with PageTemplates in an python product. Fun to look at
 perhaps if you want to see some examples. It's easy and clear.
