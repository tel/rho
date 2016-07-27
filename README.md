
# Rho. An experimental language.

*Rho* is a highly-experimental language that tries to bring together a few modern ideas for nice functional programming languages.

- HM type inference
- Strictness
- Higher-rank types
- Modules
- Row types
- Lenses and prisms
- Symbol types
- Type holes
- Good type errors
- Modular type classes (https://www.mpi-sws.org/~dreyer/papers/mtc/main-long.pdf)
- Multi-types
- Newtype deriving

```
sig Example {
  sym True False
  type Bool = Sum (True, False)
  true : Bool
  false : Bool

  type Point
  point : Double -> Double -> Point

  x : Lens Point Double
  y : Lens Point Double
}

module Example : Example {
  
  -- Fresh symbols can be generated within a module. They are locally unique.
  sym True False

  -- The sum type symbol constructs an anonymous sum from a row. A row is a
  -- disjoint set of symbols mapped to other types. If no mapping is given, 
  -- Unit is assumed
  type Bool = Sum (True, False)

  -- Definitions and type ascriptions. If `a : A` the `a@X` syntax produces a 
  -- value of type `Sum (X: A <> e)`. The type ascription fixes the value of 
  -- `e`. (Anonymous sum)
  true : Bool
  true = @True

  false : Bool
  false = @False

  sym X Y

  type Point = Product (X : Double, Y : Double)

  -- If `a:  A` then `{X = a}` produces a value of type 
  -- `Product (X : A <> e)`. (Anonymous product)
  point: Double -> Double -> Point
  point x y = {X = x, Y = y}

  x : Lens Point Double
  x = .X

  y : Lens Point Double
  y = .Y

}
```
