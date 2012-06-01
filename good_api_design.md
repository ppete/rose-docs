API Design
==========

* [Google: "How to Design a Good API and Why it Matters" by Joshua Bloch](http://lcsd05.cs.tamu.edu/slides/keynote.pdf)

---

#### Characteristics of a Good API

* Easy to learn
* Easy to use, even without documentation
* Hard to misuse
* Easy to read and maintain code that uses it
* Sufficiently powerful to satisfy requirements
* Easy to extend
* Appropriate to audience

#### The Process of API Design:

* Gather true requirements in the form of **use-cases**
* Start with a short 1-page specification
   * Agility trumps completeness
   * Collect a lot of feedback
* Use your API early and often
   * [Test-Driven Development (TDD)](http://en.wikipedia.org/wiki/Test-driven_development)

      > [T]he repetition of a very short development cycle: first the developer writes a failing automated test case that defines a desired improvement or new function, then produces code to pass that test and finally refactors the new code to acceptable standards.
   * Doubles as examples/tutorials and unit tests
* Maintain realistic expectations
   * You won't be able to please everyone... aim to displease everyone equally
   * Expect to evolve API; mistakes happen; real-world usage is necessary

#### General Principles
* **Names matter**
   * Largely self-explanatory (avoid cryptic abbreviations)
   * Be consistent (e.g. same word means same thing)
   * Strive for symmetry
   * Should read like [prose](http://en.wikipedia.org/wiki/Prose)

      > [T]he most typical form of language, applying ordinary grammatical structure and natural flow of speech rather than rhythmic structure (as in traditional poetry).

      ``` C++
        if (car.speed() > 2 * SPEED_LIMIT)
            generateAlert("Watch out for cops!");
      ```

* **When in doubt, leave it out**. You can always add, but you can never remove.
* [Power-to-weight ratio](http://en.wikipedia.org/wiki/Power-to-weight_ratio)

   > [A] measurement of actual performance [power / weight]
* Minimize accessibility of everything (**information hiding**)
* **Implementation should not impact API**: don't let implementation details "leak" into API
* Do not warp API to gain performance
* Effects of API design decisions on performance are real and permanent:
  * `Component.getSize()` returns `Dimension`
  * `Dimension` is mutable
  * Each `getSize` call must allocate `Dimension`
  * Causes *millions* of needless object allocations
* **Documentation Matters**
   * Class: what an instance represents
   * Method: contract between method and calling client (preconditions, postconditions, and side-effects)
   * Parameter: indicate units, form, ownership
* API must coexist peacefully with platform
  * Do what is customary (standard)
  * Avoid obsolete parameter and return types
  * Mimic patterns in core APIs and language
  * Take advantage of API-friendly features: generics, varargs, enums, default arguments
* Don't make the client do anything the module could do
  * Reduce need for **boilterplate code**
* Don't violate the [Principle of Least Astonishment](http://en.wikipedia.org/wiki/Principle_of_least_astonishment)

   > The design should match the user's experience, expectations, and mental models...aims to exploit users' pre-existing knowledge as a way to minimize the learning curve
* **Fail fast**: report errors ASAP
  * Compile time: static typing, generics
  * Run time: error on first bad method invocation (should be **failure-atomic**)
* Provide programmatic access to all data available in string form => no client string parsing necessary
* Overload with care: ambiguous overloadings; "just because you can doesn't mean you should"

**Input Parameters**
* **interface types over classes**: flexibility, performance
* **most specific possible type**: moves error from runtime to compile time
* use `double` (64 bits) rather than `float` (32 bits): precision loss is real, performance loss negligible
* **consistent ordering**:

    ``` C++
      #include <string.h>
      char *strcpy (char *dest, char *src);
      void bcopy   (void *src,  void *dst, int n); // bad!
    ```
* **short parameter lists**: 3 or fewer; more and users will have to refer to docs; identically typed params harmful
   * Two techniques for shortening: 1) break up method, 2) create helper class to hold parameters

**Return Values**
* avoid values that demand exceptional processing: return zero-length array or empty collection, not `null`

**Exceptions**
* ROSE rule of thumb: an error in a library should fail hard (ROSE_ASSERT(false)); Only when the function is specifically written for a 
  client (i.e., GUI) exceptions can be considered.
* don't force client to use exceptions for control flow
* don't fail silently
* favor unchecked exceptions
* include failure-capture diagnostic information
