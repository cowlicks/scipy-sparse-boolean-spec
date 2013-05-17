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

=====================
Boolean Specification
=====================

Abstract
========

This document describes how the sparse package handles boolean operations and
boolean data typed sparse matrices.


Boolean Operations
==================

For sparse matrices `A` and `B`, of any type, of the same size, we have for all
boolean operations something like::

     def boolean_op(A, B):
        ...
        if issparse(B):
            if A.shape == B.shape:
                # Sparse matrix is returned.
                return sparse_boolean_op(A, B)
            elif broadcastable(A, B):
                # Broad cast the operation elementwise onto the sparse
                # matrix, and return the sparse matrix.
                return sparse_broadcast_boolean_op(A, B)
            else:
                return False
        ...

Tl;dr this is fully equivalent to::

    spmatrix(boolean_op(A.todense(), B.todense()))

This has the drawback of making `==`, `<=`, `>=` useless in many cases
because a matrix mostly populated with `True` values is returned.
[1]_

However this can be avoided by using the inverse of the typical
operation. For example if `A` and `B` are thought to be mostly equal
instead of checking with `A == B`, you can apply the inverse (`A != B`)
and check that it is empty.

For sparse matrices you will expect::

    mostly True:        mostly False:
    A == B              A != B
    A >= B              A > B
    A <= B              A < B

You should try to only use mostly `False` operations.


Mixing sparse matrices with other types, including dense matrices
-----------------------------------------------------------------

When applying some boolean operation (`boolean_op`) to a sparse matrix
(`A`) and some arbitrary variable (`B`) what basically happens is:: 

    def boolean_op(A, B):
        ...
        if isdense(B):
            if A.shape == B.shape:
                # A can probably be represented efficiently as a sparse
                # matrix, since B can be.
                return boolean_op(A.todense(), B)
            elif broadcastable(A, B):
                # Broadcast, and return the sparse matrix.
                return sparse_broadcast_boolean_op(A, spmatrix(B))
            else:
                return False
        # Comparing two dissimilar things.
        else:
            return False
        ...

So when operating on sparse and dense matrices simultaneously three
things could possibly be returned: A sparse matrix, dense matrix, or
`False`.  This depends on the shapes of the matrices being compared.

Broadcasting
------------

Boolean operations broadcast in the same way as NumPy. Info can be
found in the `NumPy docs`_.

.. _`NumPy docs`: http://docs.scipy.org/doc/numpy/user/basics.broadcasting.html

`dtype=bool` spmatrices
=======================

A sparse matrices with `dtype=bool` has each element represented as 
either `True` or `False`. Sparse matrices consider `True` as nonzero.  

Tests
=====

Testing boolean operations works by taking some data, in the form of
several sparse and dense matrices then trying every permutation of two
of them with every boolean operation.

Testing `dtype=bool` matrices is tacked on to existing tests, this is
because there is currently no way to input general data into the test
suite. This is done by changing previously existing tests, for example::
    
    def test_something(self):
        ...
        mat = self.spmatrix(data)
        ...
to::
    
    def check_something(self, dtype):
        mat = self.spmatrix(data, dtype=dtype)
        ...
    def test_something(self):
        for dtype in supported_dtypes:
            yield self.check_something, dtype
        ...

As suggested by the parametric testing section of the NumPy docs [2]_
This isn't done for every test, just where it makes sense that boolean
data should be tested.

References
----------

.. [1] Pauli Virtanen, Sparse boolean specification, Scipy-dev mailing list
    (http://article.gmane.org/gmane.comp.python.scientific.devel/17605)

.. [2] NumPy docs, Testing Guidelines, 
    (https://github.com/numpy/numpy/blob/master/doc/TESTS.rst.txt#id6)

Copyright
---------

This document has been placed in the public domain.



..
   Local Variables:
   mode: indented-text
   indent-tabs-mode: nil
   sentence-end-double-space: t
   fill-column: 70
   coding: utf-8
   End:
