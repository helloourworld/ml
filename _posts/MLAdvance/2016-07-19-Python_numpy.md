---
layout: keynote
title: hackerrank_numpy
category: MLAdvance
description: become an hacker need to parctice.
tags:
    - Python
    - maths
---

## Arrays

A NumPy array is a grid of values. They are similar to lists, except that every element of an array must be the same type.

    `import numpy
    a = numpy.array([1, 2, 3, 4, 5])
    b = numpy.array([1, 2, 3, 4, 5], float)
    `

reverse a np.array

    `import numpy as np
    array1 = np.array(map(float,raw_input().split(' ')), float)
    array2 = array1[::-1]
    print array2
    `

## Shape and Reshape

The shape tool gives a tuple of array dimensions and can be used to change the dimensions of an array.

* 1 Using shape to get array dimensions



    `import numpy
    my_1D_array = numpy.array([1, 2, 3, 4, 5])
    print my_1D_array.shape
    my_2D_array = numpy.array([[1, 2],[3, 4],[6,5]])
    print my_2D_array.shape
    `

* 2 Using shape to change array dimensions



    `import numpy
    change_array = numpy.array([1,2,3,4,5,6])
    change_array.shape = (3, 2)
    print change_array
    `

* 3 reshape

The reshape tool gives a new shape to an array without changing its data. It creates a new array and does not modify the original array itself.

    `import numpy
    my_array = numpy.array([1, 2, 3, 4, 5, 6])
    print my_array.reshape(my_array, (3,2))
    `

## Transpose and Flatten

* 1 transpose

we can generate the transpostion of an array using the tool numpy.transpose.

It will not affect the original array, but it will create a new array.

    `import numpy as np
    my_array = np.array([[1, 2, 3],
                                    [4, 5, 6]])
    print np.transpose(my_array)
    `

* 2 Flatten

The tool flatten creates a copy of the input array flattened to one dimension.

    `import numpy as np
    my_array = np.array([[1, 2, 3], [4, 5, 6]])
    print my_array.flatten()
    `

* 3 Hack



    `import numpy as np
    shape1 = np.array(map(int, raw_input().split()))
    array1 = np.array([raw_input().split() for _ in range(shape1[0])], int)
    `

another method

    `import numpy
    shape1 = numpy.array(map(int, raw_input().split(' ')))
    array1 = numpy.array(map(int, raw_input().split(' ')))
    print numpy.transpose(array1.reshape(array1, shape1))
    print array1.flatten()
    `

## Concatenate

Two or more arrays can be concatenated together using the concatenate function with a tuple of the arrays to be joined:

    `import numpy

    array_1 = numpy.array([1,2,3])
    array_2 = numpy.array([4,5,6])
    array_3 = numpy.array([7,8,9])

    print numpy.concatenate((array_1, array_2, array_3))

    #Output
    [1 2 3 4 5 6 7 8 9]
    `

If an array has more than one dimension, it is possible to specify the axis along which multiple arrays are concatenated. By default, it is along the first dimension.

    `
    import numpy

    array_1 = numpy.array([[1,2,3],[0,0,0]])
    array_2 = numpy.array([[0,0,0],[7,8,9]])

    print numpy.concatenate((array_1, array_2), axis = 1)

    #Output
    [[1 2 3 0 0 0]
     [0 0 0 7 8 9]]
    `

    `import numpy
    [n,m,p] = map(int, raw_input().split())
    array1 = numpy.array([raw_input().split() for _ in range(n)], int)
    array2 = numpy.array([raw_input().split() for _ in range(m)], int)
    print numpy.concatenate((array1, array2))
    `

## Zeros and ones

The zeros tool returns a new array with a given shape and type filled with 's.

    `import numpy

    print numpy.zeros((1,2))                    #Default type is float
    #Output : [[ 0.  0.]]

    print numpy.zeros((1,2), dtype = numpy.int) #Type changes to int
    #Output : [[0 0]]
    `

ones

The ones tool returns a new array with a given shape and type filled with 's.

    `import numpy

    print numpy.ones((1,2))                    #Default type is float
    #Output : [[ 1.  1.]]

    print numpy.ones((1,2), dtype = numpy.int) #Type changes to int
    #Output : [[1 1]]
    `


identity

The identity tool returns an identity array. An identity array is a square matrix with all the main diagonal elements as  and the rest as . The default type of elements is float.

    `import numpy
    print numpy.identity(3) #3 is for  dimension 3 X 3

    #Output
    [[ 1.  0.  0.]
    [ 0.  1.  0.]
    [ 0.  0.  1.]]
    `

eye

The eye tool returns a 2-D array with 's as the diagonal and 's elsewhere. The diagonal can be main, upper or lower depending on the optional parameter . A positive  is for the upper diagonal, a negative  is for the lower, and a   (default) is for the main diagonal.

    `import numpy
    print numpy.eye(8, 7, k = 1)    # 8 X 7 Dimensional array with first upper diagonal 1.

    #Output
    [[ 0.  1.  0.  0.  0.  0.  0.]
     [ 0.  0.  1.  0.  0.  0.  0.]
     [ 0.  0.  0.  1.  0.  0.  0.]
     [ 0.  0.  0.  0.  1.  0.  0.]
     [ 0.  0.  0.  0.  0.  1.  0.]
     [ 0.  0.  0.  0.  0.  0.  1.]
     [ 0.  0.  0.  0.  0.  0.  0.]
     [ 0.  0.  0.  0.  0.  0.  0.]]

    print numpy.eye(8, 7, k = -2)   # 8 X 7 Dimensional array with second lower diagonal 1.
    `

## Array Mathematics

Basic mathematical functions operate element-wise on arrays. They are available both as operator overloads and as functions in the NumPy module.

    `import numpy

    a = numpy.array([1,2,3,4], float)
    b = numpy.array([5,6,7,8], float)

    print a + b                     #[  6.   8.  10.  12.]
    print numpy.add(a, b)           #[  6.   8.  10.  12.]

    print a - b                     #[-4. -4. -4. -4.]
    print numpy.subtract(a, b)      #[-4. -4. -4. -4.]

    print a * b                     #[  5.  12.  21.  32.]
    print numpy.multiply(a, b)      #[  5.  12.  21.  32.]

    print a / b                     #[ 0.2         0.33333333  0.42857143  0.5       ]
    print numpy.divide(a, b)        #[ 0.2         0.33333333  0.42857143  0.5       ]

    print a % b                     #[ 1.  2.  3.  4.]
    print numpy.mod(a, b)           #[ 1.  2.  3.  4.]

    print a**b                      #[  1.00000000e+00   6.40000000e+01   2.18700000e+03   6.55360000e+04]
    print numpy.power(a, b)         #[  1.00000000e+00   6.40000000e+01   2.18700000e+03   6.55360000e+04]
    `

## Floor, Ceil and Rint

* 1 floor

The tool floor returns the floor of the input element-wise.
The floor of x is the largest integer i where i <= x

    `import numpy

    my_array = numpy.array([1.1, 2.2, 3.3, 4.4, 5.5, 6.6, 7.7, 8.8, 9.9])
    print numpy.floor(my_array)         #[ 1.  2.  3.  4.  5.  6.  7.  8.  9.]
    `


* 2 ceil

The tool ceil returns the ceiling of the input element-wise.
The ceiling of x is the smallest integer i where i >= x

    `import numpy

    my_array = numpy.array([1.1, 2.2, 3.3, 4.4, 5.5, 6.6, 7.7, 8.8, 9.9])
    print numpy.ceil(my_array)         #[ 2.  3.  4.  5.  6.  7.  8.  9.  10.]
    `

* 3 rint

The rint tool rounds to the nearest integer of input element-wise.

    `import numpy

    my_array = numpy.array([1.1, 2.2, 3.3, 4.4, 5.5, 6.6, 7.7, 8.8, 9.9])
    print numpy.rint(my_array)         #[1.  2.  3.  4.  6.  7.  8.  9.  10.]
    `

## Sum and Prod

* 1 Sum

The sum tool returns the sum of array elements over a given axis.

    `import numpy

    my_array = numpy.array([ [1, 2], [3, 4] ])

    print numpy.sum(my_array, axis = 0)         #Output : [4 6]
    print numpy.sum(my_array, axis = 1)         #Output : [3 7]
    print numpy.sum(my_array, axis = None)      #Output : 10
    print numpy.sum(my_array)                   #Output : 10

    `

By default, the axis value is None. Therefore, it performs a sum over all the dimensions of the input array.

* 2 Prod

The prod tool returns the product of array elements over a given axis.

    `import numpy

    my_array = numpy.array([ [1, 2], [3, 4] ])

    print numpy.prod(my_array, axis = 0)            #Output : [3 8]
    print numpy.prod(my_array, axis = 1)            #Output : [ 2 12]
    print numpy.prod(my_array, axis = None)         #Output : 24
    print numpy.prod(my_array)                      #Output : 24
    `

By default, the axis value is None. Therefore, it performs the product over all the dimensions of the input array.


* 3 hack

hacking

    `import numpy

    N = map(int, raw_input().split())
    array1 = numpy.array([raw_input().split() for _ in range(N[0])], int)
    s = numpy.sum(array1, axis = 0)
    print numpy.prod(s)
    `

## Min and Max

* 1 Min

The tool min returns the minimum value along a given axis.

    `import numpy

    my_array = numpy.array([[2, 5],
                            [3, 7],
                            [1, 3],
                            [4, 0]])

    print numpy.min(my_array, axis = 0)         #Output : [1 0]
    print numpy.min(my_array, axis = 1)         #Output : [2 3 1 0]
    print numpy.min(my_array, axis = None)      #Output : 0
    print numpy.min(my_array)                   #Output : 0
    `

By default, the axis value is None. Therefore, it finds the minimum over all the dimensions of the input array.

* 2 Max

 vice versa.


 ## Mean, Var, and Std

* 1 Mean

The mean tool computes the arithmetic mean along the specified axis.

vice versa.

* 2 Var

The var tool computes the arithmetic variance along the specified axis.

vice versa.

* 3 Std

The std tool computes the arithmetic standard deviation along the specified axis.

vice versa.


## Dot and Cross

* 1 Dot

The dot tool returns the [dot product](https://en.wikipedia.org/wiki/Dot_product) of two arrays.

    `import numpy

    A = numpy.array([ 1, 2 ])
    B = numpy.array([ 3, 4 ])

    print numpy.dot(A, B)       #Output : 11
    `

* 2 Cross

The cross tool returns the [cross product](https://en.wikipedia.org/wiki/Cross_product) of two arrays.

    `import numpy

    A = numpy.array([ 1, 2 ])
    B = numpy.array([ 3, 4 ])

    print numpy.cross(A, B)     #Output : -2
    `

## Inner and Outer

* 1 Inner

The inner tool returns the [inner product](https://en.wikipedia.org/wiki/Inner_product_space) of two arrays.

    `import numpy

    A = numpy.array([0, 1])
    B = numpy.array([3, 4])

    print numpy.inner(A, B)     #Output : 4
    `

* 2 Outer

The outer tool returns the [outer product](https://en.wikipedia.org/wiki/Outer_product) of two arrays.

    `import numpy

    A = numpy.array([0, 1])
    B = numpy.array([3, 4])

    print numpy.outer(A, B)     #Output : [[0 0]
                                #          [3 4]]
    `

## Polynomials

* 1 [Poly](http://docs.scipy.org/doc/numpy/reference/generated/numpy.poly.html)

The poly tool returns the coefficients of a polynomial with the given sequence of roots.

    `print numpy.poly([-1, 1, 1, 10])        #Output : [  1 -11   9  11 -10]`

* 2 [roots](http://docs.scipy.org/doc/numpy/reference/generated/numpy.roots.html)

The roots tool returns the roots of a polynomial with the given coefficients.

    `print numpy.roots([1, 0, -1])           #Output : [-1.  1.]`

* 3 [polyint](http://docs.scipy.org/doc/numpy/reference/generated/numpy.polyint.html)

The polyint tool returns an antiderivative (indefinite integral) of a polynomial.

    `print numpy.polyint([1, 1, 1])          #Output : [ 0.33333333  0.5         1.          0.        ]`

* 4 [polyder](http://docs.scipy.org/doc/numpy/reference/generated/numpy.polyder.html#numpy.polyder)

The polyder tool returns the derivative of the specified order of a polynomial.

    `print numpy.polyder([1, 1, 1, 1])       #Output : [3 2 1]`

* 5 [polyval](http://docs.scipy.org/doc/numpy/reference/generated/numpy.polyval.html#numpy.polyval)

The polyval tool evaluates the polynomial at specific value.

    `print numpy.polyval([1, -2, 0, 2], 4)   #Output : 34`

* 6 [polyfit](http://docs.scipy.org/doc/numpy/reference/generated/numpy.polyfit.html)

The polyfit tool fits a polynomial of a specified order to a set of data using a least-squares approach.

    `print numpy.polyfit([0,1,-1, 2, -2], [0,1,1, 4, 4], 2)
    #Output : [  1.00000000e+00   0.00000000e+00  -3.97205465e-16]
    `

多项式加减乘除
The functions [polyadd](http://docs.scipy.org/doc/numpy/reference/generated/numpy.polyadd.html#numpy.polyadd), [polysub](http://docs.scipy.org/doc/numpy/reference/generated/numpy.polysub.html#numpy.polysub), [polymul](http://docs.scipy.org/doc/numpy/reference/generated/numpy.polymul.html), and [polydiv](http://docs.scipy.org/doc/numpy/reference/generated/numpy.polydiv.html#numpy.polydiv) also handle proper addition, subtraction, multiplication, and division of polynomial coefficients, respectively.

## Linear Algebra

The NumPy module also comes with a number of built-in routines for linear algebra calculations. These can be found in the sub-module linalg.

* 1 [linalg.det](http://docs.scipy.org/doc/numpy/reference/generated/numpy.linalg.det.html)

The linalg.det tool computes the determinant of an array.

    `print numpy.linalg.det([[1 , 2], [2, 1]])       #Output : -3.0`

* 2 [linalg.eig](http://docs.scipy.org/doc/numpy/reference/generated/numpy.linalg.eig.html)

numpy.linalg.eig(a)[source]
Compute the eigenvalues and right eigenvectors of a square array.

    `from numpy import linalg as LA
    w, v = LA.eig(np.diag((1, 2, 3)))`

* 3 [linalg.inv](http://docs.scipy.org/doc/numpy/reference/generated/numpy.linalg.inv.html)

The linalg.inv tool computes the (multiplicative) inverse of a matrix.

    `print numpy.linalg.inv([[1 , 2], [2, 1]])       #Output : [[-0.33333333  0.66666667]
                                                #          [ 0.66666667 -0.33333333]]
                                                `

Other routines can be found [here](http://docs.scipy.org/doc/numpy/reference/routines.linalg.html)

That's all. For more please refer to [Hackerrank](https://www.hackerrank.com/challenges/np-zeros-and-ones?h_r=next-challenge&h_v=zen).

