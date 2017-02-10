# Transpose
 ```
 [ 1 2 ]         [ 1 3 5 ]
 [ 3 4 ]   ==>   [ 2 4 6 ]
 [ 5 6 ]
 ```
 
# Multiplication
 ```
 [ a b c ]   [ 1 ]    [ a*1 b*1 c*1 ]
 [ d e f ] * [ 2 ]  = [ d*2 e*2 f*2 ]
 [ g h i ]   [ 3 ]    [ g*3 h*3 i*3 ]

   m x n     n x p         m x p
   3 x 3     3 x 1         3 x 3
 ```
 
# Matrix Properties
* **Addition**
  * A + B == B + A
  * A + (B + C) == (A + B) + C
* **Multiplication**
  * A(BC) == (AB)C
  * (A + B)C == AC + BC
  * C(A + B) == CA + CB
  * A<sup>p</sup>A<sup>q</sup> == A<sup>p + q</sup>
  * (A<sup>p</sup>)<sup>q</sup> == A<sup>pq</sup>
  * (AB)<sup>p</sup> **!=** A<sup>p</sup>B<sup>p</sup>
    * unless AB == BA
* **Scalar**
  * r(sA) == (rs)A
  * (r + s)A == rA + sA
  * r(A + B) == rA + rB
  * A(rB) == r(AB) == (rA)B
* **Transpose**
  * (A<sup>T</sup>)<sup>T</sup> == A
  * (A + B)<sup>T</sup> == A<sup>T</sup> + B<sup>T</sup>
  * (AB)<sup>T</sup> == B<sup>T</sup>A<sup>T</sup>
  * (rA)<sup>T</sup> == rA<sup>T</sup>
* **Identity**
  * AI == A == IA
  * A<sup>0</sup> == I
  * AA<sup>-1</sup> == A<sup>-1</sup>A == I
  * **[ A | I ]**  ==  **[ I | A<sup>-1</sup>]** 
