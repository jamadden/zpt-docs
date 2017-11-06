=====================
 Other Uses of Tales
=====================

.. highlight:: xml

TALES can be used outside of Zope Page Templates. Zope 3 uses TALES
expressions embedded in ZCML in a few instances where some sort of
filter expression is needed. One example is the 'filter' attribute of
the 'browser:menuItem' element::

  <menuItem
      title="Show details"
      action="show_details.html"
      permission="zope.View"
      filter="not:context/metadata_is_missing"
      />

.. highlight:: python

Ulrick Eck has written code to use TALES expressions in his own
Products. Here is sample Zope 2 code that he contributed to the
mailing list::

  import Globals
  from Globals import Persistent
  from Acquisition import aq_inner, aq_parent
  from AccessControl import getSecurityManager, ClassSecurityInfo

  from Products.PageTemplates.Expressions import getEngine, _SecureModuleImporter

  SecureModuleImporter = _SecureModuleImporter()

  class Expression (Persistent):
      method_name = ''
      _expr = None

      security = ClassSecurityInfo()

      def __init__(self, method_name):
          self.method_name = method_name
          e = getEngine()
          self._expr = e.compile(method_name)

      security.declarePrivate('validate')
      def validate(self, inst, parent, name, value, md):
          # Zope 2.3.x
          return getSecurityManager().validate(inst, parent, name, value)

      def __call__(self, context):
         e = getEngine()
         if self._expr is None:
               self._expr = e.compile(self.method_name)
         c = e.getContext(context)
         return c.evaluate(self._expr)

  Globals.InitializeClass(Expression)


  def exprNamespace(client,form, field=None, value=None, object=None):
      root = client.getPhysicalRoot()
      c = {'form': form,
           'field': field,
           'value': value,
           'here': object,
           'nothing': None,
           'options': {},
           'root': root,
           'request': getattr(root, 'REQUEST', None),
           'modules': SecureModuleImporter,
           }
      return c

Use it like this::

  myExpr = Expression('string:this is a text')
  ns = exprNamespace(...)
  result = myExpr(ns)
