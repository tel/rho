
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
signature Boolean {
  type T

  val true : Branch T
  val false : Branch T

  val cata : T -> a -> a -> a
}

module Boolean : Boolean {  
  sym True False

  type T = +(True, False)

  val True = prism 'True
  val False = prism 'False

  -- This is possible because True and False are defined as Prisms.
  val cata t thn els = case cata of
    True -> thn
    False -> els
}

signature Point {
  type T

  val mk : Double -> Double -> t

  val X : T ~> Double
  val Y : T ~> Double
}

module Point : Point {
  sym X Y

  type T = *(X : Double, Y : Double)

  val mk x y = {X = x, Y = y}

  val X = lens 'X
  val Y = lens 'Y
}

signature Tuple {
  type T a b

  val mk : a -> b -> T a b

  /*
   Multiple type signatures can be given. Of the set of given type signatures, there must be a single most general signature which unifies with all others. Ultimately, this most general signature must check against the value.
  */

  val Fst : T a b ~> a
  val Fst : Lens (T a b) (T x b) a x

  val Snd : T a b ~> b
  val Snd : Lens (T a b) (T a x) b x
}

module Tuple {
  sym Fst Snd

  type T a b = *(Fst : a, Snd : b)

  val mk a b = {Fst = a, Snd = b}

  val Fst = lens 'Fst
  val Snd = lens 'Snd
}
```
