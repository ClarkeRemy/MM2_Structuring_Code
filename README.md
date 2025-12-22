# MORK/MM2 : Structuring Code

The tutorial requires a `mork` binary from the kernel crate in the [__MORK__](https://github.com/trueagi-io/MORK).

It requires a nighly compiler of Rust.

An important dependancy is [__PathMap__](https://github.com/Adam-Vandervorst/PathMap/tree/master). (in [_crates.io_](https://crates.io/crates/pathmap))



Consider reading these pages from the MORK [wiki](https://github.com/trueagi-io/MORK/wiki) (it's okay to look them up as they get referred to in the tutorial):
- [Getting started](https://github.com/trueagi-io/MORK/wiki/Getting-started)
  - _this will be referenced in [`structuring_code_01_Setup.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_01_Setup.md)_
- [Data in MORK](https://github.com/trueagi-io/MORK/wiki/Data-in-MORK)
  - _this will be referenced in [`structuring_code_02_Basics.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_02_Basics.md)_

----

The following is a mirror of file `structuring_code/structuring_code_00_Intro_and_Contents.md`

----

This tutorial aims to walk through how a program can be constructed in MM2, focusing on small examples and building up to a non trivial program. The program is then further modified to scale to larger more involved programming.

# Tutorial Files
All the files referenced in the tutorial are to be found in the folder [`structuring_code/`](https://github.com/ClarkeRemy/MM2_Structuring_Code/tree/main/structuring_code).

The tutorial is expected to be read in the order written in this file.

If one wants to jump right in, head to [`structuring_code_00_Intro_and_Contents.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_00_Intro_and_Contents.md)

# Files in `structuring_code/`
- ## `.md` tutorial text files
  - | [`structuring_code_00_Intro_and_Contents.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_00_Intro_and_Contents.md)  
    - This markdown file
  - | [`structuring_code_01_Setup.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_01_Setup.md)  
    - Setting up an environment to run MM2 with the MORK CLI.
  - | [`structuring_code_02_Basics.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_02_Basics.md)  
    - Predication
    - Exec Syntax
    - Priorities
    - Sources/Sinks
  - | [`structuring_code_03_Set_Ops.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_03_Set_Ops.md)  
    - Design of generic execs definitions
    - unification
    - Set operation examples
  - | [`structuring_code_04_Control.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_04_Control.md)  
    - Control flow from Structured Programming.
    - Sequence (Priority | Exec chaining)
    - Selection
    - Iteration
    - Halting loops
  - | [`structuring_code_05_Going_Wide.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_05_Going_Wide.md)  
    - Tables as finite functions
    - Modifying data representation for pattern matching
    - Paths as disjoint predications
    - recursive execs
    - terminating execs
    - Fork/Join
    - making a main loop
  - | [`structuring_code_06_Going_Wide_Macros.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_06_Going_Wide_Macros.md)  
    - generalizing fork/join
    - specializing at startup with macro expansion
  - | [`structuring_code_07_Going_Wide_Larger_Programs.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_07_Going_Wide_Larger_Programs.md)  
    - adding multiple inputs and output locations
    - running multiple instances of the same process type
    - clearing data at runtime to reduce memory overhead
  - | [`structuring_code_08_Going_Wide_Two_Programs.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_08_Going_Wide_Two_Programs.md)  
    - define another recursive fold with another finite function
    - reusing the generic macro code for the new fold
    - modify the inputs to include the new data to evaluate in to the main loop
  - | [`structuring_code_09_In_Closing.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_09_In_Closing.md)  
    - take stock of the subjects that were covered
    - ideas to consider
  - | [`structuring_code_10_Rust_Extra.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_10_Rust_Extra.md)  
    - introduction to a Rust template to experiment with the internals


- ## `.mm2` programs in `structuring_code/mm2_programs`  
  - Setup 
    - | `Setup_Hello_World.mm2`  
      - A basic file to see if the tutorial environment is set up.

  - Basics
    - | [`Basics_01_file1.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_01_file1.mm2)  
      | [`Basics_02_file2.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_02_file2.mm2)  
      | [`Basics_03_file1_file2.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_03_file1_file2.mm2)  
      | [`Basics_04_file1_file2_predicated.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_04_file1_file2_predicated.mm2)  
      | [`Basics_05_file1_file2_project.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_05_file1_file2_project.mm2)  
      - Examples showing the behavior of loading a file, and motivating predication.
    - | [`Basics_06_Priority_0_1.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_06_Priority_0_1.mm2)  
      | [`Basics_06_Priority_1_00.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_06_Priority_1_00.mm2)  
      | [`Basics_06_Priority_00_01.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_06_Priority_00_01.mm2)  
      | [`Basics_06_Priority_0_(0_0).mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_06_Priority_0_(0_0).mm2)  
      | [`Basics_06_Priority_(1)_(0_0).mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_06_Priority_(1)_(0_0).mm2)  
      | [`Basics_06_Priority_(0_0)_(0_1).mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_06_Priority_(0_0)_(0_1).mm2)  
      - Examples to see what order execs run in.
    - | [`Basics_07_Sources_Sinks.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_07_Sources_Sinks.mm2)  
      | [`Basics_08_Sink_Removal.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Basics_08_Sink_Removal.mm2)  
      - A basic examples to showcase pattern sources and template sinks.


  - Set Operations
    - | [`Set_Ops_01_Hardcoded_Locations.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Set_Ops_01_Hardcoded_Locations.mm2)
      - Runs an exec as a function where the argument and return locations are hardcoded.
    - | [`Set_Ops_02_Parameterized_Locations.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Set_Ops_02_Parameterized_Locations.mm2)
      - The initial exec uses a "definition" in the database to construct a function with the right argument and return locations.
    - | [`Set_Ops_03_Union.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Set_Ops_03_Union.mm2)  
      | [`Set_Ops_04_Intersection.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Set_Ops_04_Intersection.mm2)  
      | [`Set_Ops_05_Difference.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Set_Ops_05_Difference.mm2)  
      | [`Set_Ops_06_Symmetric_Difference.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Set_Ops_06_Symmetric_Difference.mm2)
      - Examples that used the parameterized location pattern to implement set operations.
  
  - Control
    - | [`Control_01_Priority_Seq.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_01_Priority_Seq.mm2)
      - A collection of execs that run in the order of the priority from 0 to 3 (inclusive)
    - | [`Control_02_Exec_Chaining_Seq.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_02_Exec_Chaining_Seq.mm2)
      - Execs that run one after another by spawning the next to run.
    - | [`Control_03_Exec_Chaining_Fail_Seq.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_03_Exec_Chaining_Fail_Seq.mm2)
      - An exec chain that fails to match after the first step.
    - | [`Control_04_Select_b_c.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_04_Select_b_c.mm2)
      - Runs execs only execs that match the space.
    - | [`Control_05_Select_First_Data.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_05_Select_First_Data.mm2)
      - Runs one exec, removing the data that would cause other execs to match.
    - | [`Control_06_Select_First_Exec.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_06_Select_First_Exec.mm2)
      - Runs one exec, removing other execs that would match the data.
    - | [`Control_07_Recursive.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_07_Recursive.mm2)
      - The most basic exec that constructs itself.
    - | [`Control_08_Halts_on_fail.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_08_Halts_on_fail.mm2)
      - A recursive exec that eventually fails when decrementing a Peano number to match, ending the recursion.
    - | [`Control_09_Halts_on_success.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Control_09_Halts_on_success.mm2)
      - A loop that terminates when one of the execs it spawns finally succeeds to match

  - Going Wide
    - [`Going_Wide_01_Finite_Function.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Going_Wide_01_Finite_Function.mm2)
      - Testing the truth tables for our `eval` of booleans.
    - | [`Going_Wide_02.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Going_Wide_02.mm2)
      - A program that takes a recursive expression, splits it into paths, then evaluates with by joining results with a finite function
    - | [`Going_Wide_11_Macros.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Going_Wide_11_Macros.mm2)
      - A modified [`Going_Wide_02.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Going_Wide_02.mm2) by using using partial evaluation to allow the forks and joins to be generic, but specialize them before the main loop.
    - | [`Going_Wide_21_Larger_Programs.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Going_Wide_21_Larger_Programs.mm2)
      - A modified [`Going_Wide_11_Macros.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Going_Wide_11_Macros.mm2) to support multiple inputs and to use less memory.
    - | [`Going_Wide_31_Two_Programs.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Going_Wide_31_Two_Programs.mm2)
      - A modified [`Going_Wide_21_Larger_Programs.mm2`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/mm2_programs/Going_Wide_21_Larger_Programs.mm2) to support running a second process type using the same fork/join logic.

- ## [`mork_playground/`](https://github.com/ClarkeRemy/MM2_Structuring_Code/tree/main/structuring_code/mork_playground)
  - A rust binary crate program that serves as a template for basic file IO with an embedded MORK instance.  
  mentioned in [`structuring_code_10_Rust_Extra.md`](https://github.com/ClarkeRemy/MM2_Structuring_Code/blob/main/structuring_code/structuring_code_10_Rust_Extra.md)
