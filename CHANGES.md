# Version 0.4

* Fixed a long-standing soundness issue (#30) in compositional
  verification of LLVM programs. Previously, a specification for a
  function that neglected to mention an effect that the function in fact
  caused could be successfully verified. When verifying a caller of that
  function, only the effects mentioned in the specification would be
  used. The fix for this issue may break some proof scripts: any pointer
  mentioned using `crucible_points_to` in the initial state of a
  specification but not in the final state will be assigned a final
  value of "invalid", and any subsequent reads from the pointer will
  fail. To fix this issue, make sure that every specification you use
  provides a final value for every pointer it touches (which in many
  cases will be the same as its initial value).

* Added an experimental command, `llvm_boilerplate`, that emits skeleton
  function specifications for every function defined in an LLVM module.
  The additional `crucible_llvm_array_size_profile` command can be used
  to refine the results of `llvm_boilerplate` based on the array sizes
  used by calls that arise from a call to the named top-level function.

* Added support for using the symbolic execution profiler available in
  Crucible. The `enable_crucible_profiling` command causes profiling
  information to be written to the given directory. This can then be
  visualized using the rendering code available
  [here](https://github.com/GaloisInc/sympro-ui).

* Added proof tactics to use Yices (`w4_unint_yices`) and CVC4
  (`w4_unint_cvc4`) through the What4 backend instead of SBV.

* Modified the messages emitted for failed points-to assertions to be in
  terms of LLVM values.

* Added support for using the SMT array memory model to reason about the
  LLVM heap. The `enable_smt_array_memory_model` command enables it for
  all future proofs.

* LLVM bitcode format support is improved. Versions 3.5 to 9.0
  are known to be mostly well-supported. We consider parsing failures
  with any version newer than 3.5 to be a bug, so please report them on
  [GitHub](https://github.com/GaloisInc/saw-script/issues/new).

* New experimental model counting commands `sharpSAT` and `approxmc`
  bind to the external tools of the same name. These were mistakenly
  listed as included in 0.3.

* Built against Cryptol 2.8.0.

* Improved error messages in general.

* Fixed various additional bugs, including #211, #455, #479, #484, #493,
  #496, #511, #521, #522, #534, #563

# Version 0.3

* Java and LLVM verification has been overhauled to use the new Crucible
  symbolic execution engine. Highlights include:

    * New `crucible_llvm_verify` and `crucible_llvm_extract` commands
      replace `llvm_verify` and `llvm_extract`, with a different
      structure for specification blocks.
    
    * LLVM verification tracks undefined behavior more carefully and has
      a more sophisicated memory model. See the
      [manual](https://github.com/GaloisInc/saw-script/blob/master/doc/manual/manual.md#specification-based-verification)
      for more.
    
    * New, experimental `crucible_jvm_verify` and
      `crucible_java_extract` commands will eventually replace
      `java_verify` and `java_extract`. For the moment, the former two
      are enabled with the `enable_experimental` command and the latter
      two are enabled with `enable_deprecated`.
      
    * More flexible specification language allows convenient description
      of functions that allocate memory, return arbitrary values, expect
      explicit aliasing, work with NULL pointers, cast between pointers
      and integers, or work with opaque pointers.
    
    * Ghost state is supported in LLVM verification, allowing reasoning
      about certain complex or unavailable code.
    
    * Verification of LLVM works for a larger subset of the language,
      which particularly improves support for C++.
    
* LLVM bitcode format support is greatly improved. Versions 3.5 to 7.0
  are known to be mostly well-supported. We consider parsing failures
  with any version newer than 3.5 to be a bug, so please report them on
  [GitHub](https://github.com/GaloisInc/saw-script/issues/new).

* Greatly improved error messages throughout.

* Built against Cryptol 2.7.0.

* New model counting commands `sharpSAT` and `approxmc` bind to the
  external tools of the same name.

* New proof script commands allow multiple goals and related proof
  tactics. See the
  [manual](https://github.com/GaloisInc/saw-script/blob/master/doc/manual/manual.md#multiple-goals).

* Can be built with Docker, and will be available on DockerHub.

* Includes an Emacs mode.

# Version 0.2-dev

* Released under the 3-clause BSD license

* Major improvements to the Java and LLVM verification infrastructure,
  as described in more detail [here](doc/java-llvm/java-llvm.md):
    * Major refactoring and polish to `java_verify` and `java_symexec`
    * Major refactoring and polish to `llvm_verify` and `llvm_symexec`
    * Fixed soundness bug in `llvm_verify` treatment of heap
      modifications
    * Fixed soundness bug related to `java_assert` and `llvm_assert`
    * Support for branch satisfiability checking to be configured
    * Support for some types of allocation in `java_verify`, enabled
      with `java_allow_alloc`
    * Improved support for LLVM structs (including the `llvm_struct`
      type for `llvm_verify`)
    * Support for non-scalar return values in `java_verify` and
      `java_symexec`
    * Support for using `java_ensure_eq` on fields of return value
    * Access to safety conditions in `java_symexec` and `llvm_symexec`
    * New primitives `llvm_assert_eq` and `java_assert_eq`

* Some changes to the SAWScript language:
    * Conditional expressions including the keywords `if`, `then`, and
      `else`, and the new constants `true` and `false`
    * New `eval_int` and `eval_bool` functions to expose Cryptol bit
      vectors and `Bit` values as `Int` and `Bool` values in SAWScript
    * Pattern matching for tuples
    * Improvements to pretty printing, including: `set_base` and
      `set_ascii` commands to control the formatting of values; a `show`
      function to convert a value to a string without printing it; and
      the ability to use `print` or `show` instead of
      `llvm_browse_module` and `java_browse_class`
    * New built-in functions for processing lists

* New proof backends:
    * A new `rme` proof tactic, based on the
      [Reed-Muller Expansion](https://en.wikipedia.org/wiki/Reed%E2%80%93Muller_expansion)
      normal form for propositional formulas. This tactic is
      particularly efficient for dealing with polynomials over Galois
      fields, as used in AES, for instance.

* Linked against the latest Cryptol code, which includes the following
  changes since release 2.3.0:
    * An extended prelude with more Haskell-like functions
    * Better, more portable seeding for `random`
    * Performance improvements for symbolically executing tables of
      constant values
    * Performance improvements for type checking large constants

* Internal improvements:
    * Simplified Cryptol to SAWCore translation
    * Improved performance of Cryptol to SAWCore translation for
      recursive functions
    * Updated bitcode parser to support some of the changes in LLVM 3.7
    * Many bug fixes
    * Many code cleanups
