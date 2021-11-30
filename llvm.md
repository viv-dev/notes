# llvm

Defines something called an IR (intermediate representation) with the extension `.ll` that is platform generic (i.e. independent of x86, ARM, PCIE etc). This allows code optimisations to be done at the IR level instead of having to have different optimisers for every CPU architecture.

The optimised IR is then converted into the appropriate Assembly for the target platform.

Swift, Rust, Julia and the Clang C/C++ compler all use LLVM.
