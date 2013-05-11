.. title: Boolean Specification
.. slug: bool-spec
.. date: 2013/04/21 23:39:43
.. tages:
.. link:
.. description:

    SciPEP: ?
    Title: Boolean specification
    Version: $Revision$
    Last-Modified: $Date$
    Author: Blake Griffith <blake.a.griffith@gmail.com>
    Status: Active
    Type: Draft
    Content-Type: text/x-rst
    Created: 21-Apr-2013
    Post-History: 21-Apr-2013

Boolean Specification
=====================

Abstract
--------

This document describes how the sparse package boolean operations  
handles boolean data typed sparse matrices.


Boolean Operations
------------------

For sparse matrices `A` and `B`, of any type, of the same size, we have for all
boolean operations::

     boolean_op(A, B)

is fully equivalent to::

    spmatrix(boolean_op(A.todense(), B.todense()))

This has the drawback of making `==`, `<=`, `>=` useless in many cases
because a matrix mostly populated with `True` values is returned.
[1]_

However this can be avoided by using the inverse of the typical
operation. For example if A and B are thought to be mostly equal
instead of checking with `A == B`, you can apply the inverse and
check that it is empty `A != B`.

For sparse matreces you will expect::

    mostly True:        mostly False:
    A == B              A != B
    A >= B              A > B
    A <= B              A < B

You should try to only use mostly `False` operations.


Sparse and Dense Mixing
'''''''''''''''''''''''

When comparing mixed sparse dense matrices, a dense matrix is returned.
For exapmle if `A` is sparse and `D` is dense, `boolean_op(A, B)` is 
fully equivalent to::

    boolean_op(A.todense(), D)

However returning a dense matrix is not always optimal. Like in cases 
where `D` is not the same size as `A` and broadcasting_ occurs. In
cases like these a sparse matrix could be efficiently returned.

.. _broadcasting: http://docs.scipy.org/doc/numpy/user/basics.broadcasting.html

Broadcasting
''''''''''''

Boolean operations broadcast in the same way as NumPy. Info can be
found in the `NumPy docs`_.

.. _`NumPy docs`: http://docs.scipy.org/doc/numpy/user/basics.broadcasting.html

dtype=bool spmatrices
---------------------

Sparse matrices can have `dtype=bool`. So each element is represented
either `True` or `False`. 


References
''''''''''

.. [1] Pauli Virtanen, Sparse boolean specification, Scipy-dev mailing list
    (http://article.gmane.org/gmane.comp.python.scientific.devel/17605)

Copyright
'''''''''

This document has been placed in the public domain.



..
   Local Variables:
   mode: indented-text
   indent-tabs-mode: nil
   sentence-end-double-space: t
   fill-column: 70
   coding: utf-8
   End:
