
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
module Examples {
  sym A B C 

  val trivialRecord : *(A : a | e)
  val trivialRecord a = {A = a}

  val trivialCorecord : +(A : a | e)
  val trivialCorecord a = a @ A
}

signature Boolean {
  /*
    a single type named This may be defined. It has special properties:
      - using the module name M in type context is sugar for M.This
      - Modular type-classes dispatch off of the This value
  */
  type This

  val true : Branch T
  val false : Branch T

  val cata : a -> a -> (T -> a)
}

module Boolean : Boolean {  
  sym True False

  type This = +(True, False)

  val True = prism 'True
  val False = prism 'False

  // This is possible because True and False are defined as Prisms.
  val cata thn els this = case cata of
    True -> thn
    False -> els
}

signature Point {
  type This

  val mk : Double -> Double -> t

  val X : T ~> Double
  val Y : T ~> Double
}

module Point : Point {
  sym X Y

  type This = Sum (X : Double, Y : Double)

  val mk x y = {X = x, Y = y}

  val X = lens 'X
  val Y = lens 'Y
}

signature Tuple {
  type This a b

  val mk : a -> b -> T a b

  /*
   Multiple type signatures can be given. Of the set of given type signatures, there must be a single most general signature which unifies with all others. Ultimately, this most general signature must check against the value.
  */

  val Fst : T a b ~> a
  val Fst : Lens (T a b) (T x b) a x

  val Snd : T a b ~> b
  val Snd : Lens (T a b) (T a x) b x
}

module Pair {
  sym Fst Snd

  type Pair a b = *(Fst : a, Snd : b)

  val mk a b = {Fst = a, Snd = b}

  val Fst = lens 'Fst
  val Snd = lens 'Snd
}

signature List {
  type This a

  val Nil : Branch (T a)
  val Cons : This a +> Pair a (This a)

  val cata : r -> (a -> r -> r) -> (This a -> r)
}

module List : List {
  sym Nil Cons
  type This a = Sum (Nil, Cons :  *(Head : a, Tail : This a))

  val Nil = prism 'Nil
  val Cons = prism 'Cons

  val cata nil cons = go where
    go this = case this of
      Nil -> nil
      // This is possible because Fst and Snd are defined as Lenses
      Cons {Pair.Fst = head, Pair.Snd = tail} -> cons head (go tail)
}
```
