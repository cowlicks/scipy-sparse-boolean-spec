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


Abstract
========

This document describes how the sparse package handles boolean data typed
sparse matrices. 


Rationale
=========

General
-------


General Binary Operations
-------------------------
The goal is to have functional parity with numpy's ndarray and matrix
objects.

With Scalars or vectors
'''''''''''''''''''''''

You can perform any boolean operation between a sparce matrix and any 
type you would be able to with numpy. If the sparse matrix is of size
NxM (row major) you can compare it with things that are vector like a 
list, tuple, 1D vector all of length M, or a 2D matrix or array of size
1xM, as well as scalars.

When a sparse matrix is compared with one of these vector like things,
the vector is broadcast such that each column of the matrix is compared
with the corresponding element in the vector.

With numpy ndarrays or matrices
'''''''''''''''''''''''''''''''

When comparing sparce matricies with numpy arrays or matrices the 
sparse matrix is converted to a dense array or matrix and compared. So
a dense array or matrix is returned. 


Boolean Operations
------------------

For sparse matrices A, B and sparse matrix type spmatrix, we have for all
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



References
==========

.. [1] Pauli Virtanen, Sparse boolean specification, Scipy-dev mailing list
    (http://article.gmane.org/gmane.comp.python.scientific.devel/17605)

Copyright
=========

This document has been placed in the public domain.



..
   Local Variables:
   mode: indented-text
   indent-tabs-mode: nil
   sentence-end-double-space: t
   fill-column: 70
   coding: utf-8
   End:
