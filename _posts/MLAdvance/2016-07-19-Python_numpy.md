---
layout: post
title: hackerrank_numpy
category: MLAdvance
description: become an hacker need to parctice.
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

That's all. For more please refer to [Hackerrank](https://www.hackerrank.com/challenges/np-zeros-and-ones?h_r=next-challenge&h_v=zen)
