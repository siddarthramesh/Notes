# Linear Algebra with Matrices

> NOTE: All "row" terms can be flipped for column unless specified otherwise

## Row Echelon Form
* Has to follow 3 properties
1. All zero rows, if there are any, appear at the bottom of the matrix.
2. The first nonzero entry from left of a nonzero row is a 1. This entry is called a **leading one** of its row
3. For each nonzero row, the leading one appears to the right and below any leading one in preceding rows.
* can become a **reduced row echelon form**
4. If a column contains a leading one, then all other entries in that column are zero

```
 [ 1 0 0 2 1 ]    Example of reduced row echelon
 [ 0 1 0 1 2 ]
 [ 0 0 1 0 2 ]
 [ 0 0 0 0 0 ]
```

* A<sub>NxM</sub> in R-RE form != *I<sub>N</sub>*

## Elementary Row Operation
1. Interchange any two rows
   * r<sub>i</sub> <---> r<sub>j</sub> 
2. Multiply a row by a nonzero number
   * *k*r<sub>i</sub> --> r<sub>i</sub>
3. Add a multiple of one row to another
   * *k*r<sub>i</sub> + r<sub>j</sub> --> r<sub>j</sub>

* A<sub>NxM</sub> == B<sub>NxM</sub> if can match with finite elementary row operations
* row echelon form is **not unique**


