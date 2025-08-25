# GSoC FINAL REPORT 2025:-Enhancing the Beam, Column, and Structure2D Modules in SymPy’s Continuum Mechanics
**Enhancing the Beam, Column, and Structure2D Modules in SymPy’s Continuum Mechanics**  
*— Sai Udayagiri*

| Role              | Name & Affiliation                                                                 |
|-------------------|-------------------------------------------------------------------------------------|
| **Main Mentor**   | [T. van Woudenberg](https://github.com/Tom-van-Woudenberg)                          |
| **Backup Mentors**| [Francesco Bonazzi](https://github.com/Upabjojr), [Jason Moore](https://github.com/moorepants) |
| **Reviewers**     | [Oscar Benjamin](https://github.com/oscarbenjamin), [T. van Woudenberg](https://github.com/Tom-van-Woudenberg) |
| **Organization**  | [SymPy](https://github.com/sympy/sympy)                                            |
| **Project Duration** | May 2025 – Aug 2025                                                             |
 

---

<center>

## 1.Acknowledgments & Motivation

This final report summarizes my Google Summer of Code (GSoC) project, in which I worked to enhance SymPy’s `continuum_mechanics` module by resolving issues in the Beam module, refining and reviewing Lucas's Column module, and improving the `Structure2d` module by integrating comprehensive horizontal (axial-force) analysis.

I extend my deepest gratitude to my **main mentor**, **[T. van Woudenberg](https://github.com/Tom-van-Woudenberg)**, for guiding me throughout the project and dedicating significant time to code reviews and refinements. My thanks also go to **[Francesco Bonazzi](https://github.com/Upabjojr)** and **[Jason Moore](https://github.com/moorepants)** for their support as backup mentors.

Special thanks to **[Oscar Benjamin](https://github.com/oscarbenjamin)** for his critical review of my implementation and invaluable suggestions for improving code quality.

I am also grateful to **[Borek Saheli](https://github.com/BorekSaheli)**, who was consistently available for help and support, aiding in refining tasks, enhancing the meaningfulness of my contributions, and contributing greatly to building a robust `Structure2d` module capable of solving 2D problems in Sympy's continuum_mechanics module as part of Bachelor’s thesis.[Borek-thesis](https://oit.tudelft.nl/Macaulays-method/theses/borek.html) [PR #27130](https://github.com/sympy/sympy/pull/27130)

A heartfelt thank-you to **[Lucas Verlaan](https://github.com/lfverlaan)**, whose Bachelor’s thesis *Implementation of Axial Force Analysis via Macaulay’s Method Using Python* laid the foundation for the Column module which was very useful for my project.[lucas-thesis](https://oit.tudelft.nl/Macaulays-method/theses/lucas.html) [PR #28138](https://github.com/sympy/sympy/pull/28138)

I also appreciate the work of **[Alex Baudoin](https://github.com/AJDBaudoin)**, whose thesis on  extended Macaulay’s method for complex *twodimensional structures* geometries, on which  my development in the `Structure2d` module included.[Alex-thesis](https://oit.tudelft.nl/Macaulays-method/theses/alex.html)

This project deepened my understanding of open-source contributions and GitHub workflows and significantly enhanced my skill set.



</center>

## 2.Project Goals – Brief Summary

The goal of this project was to enhance the Beam, Column, and Structure2D modules in SymPy’s `continuum_mechanics` package, improving both usability and analytical power. During the early stages, I identified several critical issues. The Beam module, though a capable one-dimensional solver, lacked validation checks. Specifically, the `apply_supports` method, when given an incorrect support type, internally mistook it for another type—resulting in an obscure index error rather than a clear exception. Meanwhile, `solve_for_reaction_loads` would raise an uninformative key index error instead of providing a helpful message when users omitted or misassigned reaction symbols.

The `point_cflexure` method incorrectly returned all algebraic roots instead of only those indicating actual contraflexure points, and it failed entirely in constant-zero regions. The maximum-finding methods—`max_bending_moment`, `max_shear`, and `max_deflection`—would enter infinite loops under zero constant region conditions and fail to report multiple points or intervals occuring maxima, thus undermining their usefulness for analysis purposes.

The newly introduced Column module offers axial loading capabilities, but it lacks essential methods like `max_axial_force()` and `max_extension()`, limiting its analytical utility. Structure2D, intended to reduce code duplication by building upon the Beam module, currently supports only vertical analysis and lacks integration with the Column module for for full horizontal (axial) behavior. It also suffers from minimal documentation, only supports plotting of unwrapped 1D structures (instead of full 2D visuals), and its summary method only reports vertical metrics and 1D x-coordinates. Additionally, while Alex’s algorithm from his thesis is used, it has not been rewritten for clarity and test coverage is not wide to test all.

Through out my project, I worked on several enhancements: I refined Column module that supports integration into Structure2D; improved error handling and user feedback within the Beam module; and extended Structure2D to support horizontal analysis, added methods for plotting deflection, axial force, shear, bending moment, and deformation on the full 2D structure; introduced `apply_rotation_hinges`; enhanced the summary method to show 2D coordinates along with axial and bending data at critical points, and refactored Alex’s algorithm for improved readability and code quality.



## Contents

1. **Acknowledgments**

2. **Project Goals**

3. **Project Work**

    3.1 Beam Module Enhancements

        3.1.1 Validation checks for `apply_support()` and `join()` methods

        3.1.2 Improved `solve_for_reaction_loads` error handling to be more user-friendly

        3.1.3 Corrected `point_cflexure` to identify true contraflexure points

        3.1.4 Fixed infinite loops in `max_bending_moment` and `max_shear_force``

        3.1.5 Reports multiple points or intervals where maxima occur

        3.1.6 Broad TDD-based test coverage for the above enhancements

    3.2 Column Module Enhancements

        3.2.1 Renamed `deflection` method to `extension` for clarity

        3.2.2 Added `max_axial_force()` and `max_extension()` methods

        3.2.3 Improved validation in `apply_supports` for consistency with Beam

        3.2.4 Extended TDD-based test coverage

    3.3 Structure2D Module Enhancements

        3.3.1 Streamlined `solve_reaction_loads`—no longer requires manual symbol input

        3.3.2 Improved reaction symbol naming for readability

        3.3.3 Integrated horizontal (axial) analysis via Column module

            3.3.3.1 Extended `apply_supports` to handle columns

            3.3.3.2 Adapted `apply_loads` for hinge and column support

        3.3.4 Added `apply_rotation_hinge` support

        3.3.5 Added methods: `deflection()`, `extension()`, and `axial_force()`

        3.3.6 Plots for unwrapped 1D structures: `plot_deflection()`, `plot_extension()`, `plot_axial_force()` with doc examples

        3.3.7 Added 2D structural plots: `plot_axial_force_on_structure()`, `plot_shear_on_structure()`, `plot_bending_on_structure()`

        3.3.8 Added `plot_deformation()` to visualize structural shifts

        3.3.9 Updated summary to include axial force and mid-point bending data

        3.3.10 Enhanced summary to show actual 2D coordinates, not just unwrapped 1D points

4. **Future Scope for Extensions**

5. **Personal Experience**

6. **Conclusion**

7. **References**

## 3.Project work
### 3.1.1 Validation checks for `apply_support()` and `join()` methods

When calling `Beam.apply_support()` with an incorrect support type (e.g., "pen" instead of "pin") while solving using beam module , the prior implementation silently defaulted to a fixed support, resulting in a `KeyIndexError` during `solve_for_reaction_loads()`. This obscured the root cause and complicated debugging. The `join()` method similarly lacked validation for incorrect or unsupported inputs, leading to unpredictable behavior without informative error messages.

**Changes Made**:
- Implemented input validation in `apply_support()` to verify valid support types (e.g., "pin", "fixed", "roller") and raise a clear `ValueError` with a descriptive message for invalid inputs.
- Added equivalent validation in the `join()` method to ensure only supported values are processed, providing user-friendly error messages for invalid inputs.
- Added test cases to validate .
- These changes resolve issue [#28192](https://github.com/sympy/sympy/issues/28192).

**Merged Pull Request**:
- [#28194](https://github.com/sympy/sympy/pull/28194)

### 3.1.2 Improved `solve_for_reaction_loads` error handling

The `solve_for_reaction_loads` method in the Beam module previously raised a generic `IndexError: tuple index out of range` for various incorrect usages, missing reaction symbols, or incorrect reaction inputs. This uninformative error, stemming from the line `solution = list((linsolve([shear_curve, moment_curve] + slope_eqs + deflection_eqs, (C3, C4) + reactions).args)[0])`, made it difficult for users to identify the specific issue, as the same error was produced regardless of the cause (e.g., no reactions provided, wrong reactions given).

**Changes Made**:
- Introduced `applied_support_symbols` to internally track reaction symbols generated by `apply_support()`, similar to `rotation_hinge_symbols` and `sliding_hinge_symbols`, while preserving the ability for users to pass supports as loads not to break the current implementation.
- Modified `solve_for_reaction_loads` to utilize `applied_support_symbols` alongside user-provided reactions for enhanced validation and changed the algorithm to handle users symbols and internal symbols to handle duplicates.
  - Added test cases to validate improved error handling and ensure backward compatibility.
- These changes resolve issue [#26604](https://github.com/sympy/sympy/issues/26604).

**Merged Pull Request**:
- [#28263](https://github.com/sympy/sympy/pull/28263)

### 3.1.3 Corrected `point_cflexure` to identify true contraflexure points

The `point_cflexure` method is intended to return points where the bending moment is zero and changes sign (true contraflexure points). However, the prior implementation simply returned all algebraic roots of the bending moment expression without verifying sign changes. Additionally, it failed to handle cases where an entire region of the bending moment curve is zero, raising a `NotImplementedError` instead of identifying valid points or intervals.

**Changes Made**:
- Updated the method to use `solveset` on each piecewise segment of the bending moment expression to find isolated roots or intervals where the moment is zero.
- Added checks to verify sign changes at roots, ensuring only true contraflexure points are returned and jumps with sign change also included as contraflexure points.
- Implemented logic to skip  flat-zero regions before solving, preventing errors and providing accurate results.
- Introduced a two-pointer algorithm to verify sign changes at each root by checking the bending moment at midpoints on either side of the root. A point is considered a contraflexure point if the bending moment at the left midpoint and right midpoint has opposite signs, ensuring no zero crossing occurs until the next adjacent root.
- Added vast test cases combining different loads, different suppeorts, joints, constant zero region cases at different places 
- These changes resolve issue [#26634](https://github.com/sympy/sympy/issues/26634).

**Merged Pull Request**:
- [#28143](https://github.com/sympy/sympy/pull/28143)

### 3.1.4 Tried fixing infinite loops in `max_bending_moment`and `max_shear_force`.

The previous implementations of `max_bending_moment` and `max_shear_force`  methods in the Beam module entered infinite loops when encountering constant zero regions in their respective curves (shear force and  bending moment(potentially deflection too). This issue was demonstrated in scenarios with specific zero region cases , leading to KeyErrors related to assumptions.

**Changes Made**:
- Modified the `max_shear_force` and `max_bending_moment` method to detect and skip flat-zero regions, preventing infinite loops by checking if shear values across intervals and mid points are consistently zero.
- Updated logic to handle constant slope or zero segments without repetitive evaluations.
- These changes resolve issue for monotonic cases but fails for non monotonic cases [#28210](https://github.com/sympy/sympy/issues/28210), [#28211](https://github.com/sympy/sympy/issues/28211)
- Added wide range of testcases for both max_shear_force() and max_bending_moment() an better algorithm could make this meaningful contributions for these two pull requests

**Open Pull Requests**:
- [#28237](https://github.com/sympy/sympy/pull/28237)
- [#28249](https://github.com/sympy/sympy/pull/28249)

### 3.1.5 Reports multiple points or intervals where maxima occur

The `max_bending_moment`, `max_shear_force` , and `max_deflection` methods in the Beam module previously returned the maximum value along with only a single point or interval of occurrence, even when multiple distinct points or intervals shared the same maximum value. This limitation reduced the methods' analytical utility, as users could not easily identify all locations where the maximum occurred in beam systems.

**Changes Made**:
- Updated the logic in `max_shear_force` to identify and collect all points and intervals where the shear force reaches its maximum value, rather than reporting only one.
- Extended similar enhancements to `max_bending_moment` to ensure they report multiple points or intervals with the same maximum.
- Implemented checks to handle cases with multiple maxima, improving the comprehensiveness of the results for better study of beam behavior.
- These changes address the limitations in handling multiple maxima no issues were created for this identification but the below pull requests which had changes for max_shear_force, max_bending_moment has these changes too.

**Open Pull Requests**:
- [#28237](https://github.com/sympy/sympy/pull/28237)
- [#28249](https://github.com/sympy/sympy/pull/28249)

### 3.1.6 Broad TDD-based test coverage for the above enhancements

Following a test-driven development (TDD) workflow as recommended by my mentor, comprehensive test cases were implemented for each enhancement made to the Beam module, including `apply_support()`, `join()`, `solve_for_reaction_loads`, `point_cflexure`, `max_bending_moment`, `max_shear_force`, and `max_deflection`. These tests validated the correctness of input handling, error messages, sign change detection, zero-region handling, and multiple maxima reporting. This rigorous TDD approach significantly improved code quality, reliability, and maintainability.

### 3.2.1 Renamed `deflection` method to `extension` for clarity

The `deflection` method in the Column module was misnamed, as it calculated axial displacement rather than vertical deflection, causing confusion with the Beam module’s `deflection()` method, which correctly refers to vertical deflection. This misleading terminology made it difficult for users to understand the method’s purpose.

**Changes Made**:
- Renamed `Column.deflection()` to `Column.extension()` to accurately reflect its role in computing axial displacement.
- Updated method docstrings and boundary condition references to use “extension” instead of “deflection” for consistency and clarity.
- Modified test cases to reflect the renamed method and verified no functional changes occurred beyond the naming update.
- These changes resolve issue [#28303](https://github.com/sympy/sympy/issues/28303).

**Merged Pull Request**:
- [#28304](https://github.com/sympy/sympy/pull/28304)

### 3.2.2 Added `max_axial_force()` and `max_extension()` methods

The Column module previously allowed computation of axial force and extension expressions along with their visualizations but lacked methods to determine the maximum values of these quantities or their locations. This limited its analytical capabilities compared to the Beam module, which provides methods like `max_bmoment()` to identify maximum values and their positions.

**Changes Made**:
- Added `max_axial_force()` method to compute the location and value of the maximum axial force in the Column module, analogous to the Beam module’s functionality.
- Added `max_extension()` method to calculate the location and value of the maximum axial displacement (extension), enhancing the module’s utility for structural analysis.
- Implemented logic to evaluate axial force and extension at critical points, such as load application points and supports, to ensure accurate identification of maxima.
- Added test cases to validate the correctness of `max_axial_force()` and `max_extension()` under various loading and support conditions.
- These changes resolve issue [#28165](https://github.com/sympy/sympy/issues/28165). but still lacks the proper algorithm as same as beam the better algorithm could add benefit to these two methods

**Open Pull Request**:
- [#28165](https://github.com/sympy/sympy/pull/28165)

### 3.2.3 Improved validation in `apply_supports` for consistency with Beam

The Column module previously supported only the `apply_support()` method for adding supports, lacking the ability to handle manual support application (via `apply_load()` with manually defined boundary conditions). This created inconsistency with the Beam module, which supports both workflows, and caused integration issues in the Structure2D module, where beam components supported manual application but column components did not, leading to inconsistent behavior.

**Changes Made**:
- Enhanced the Column module to support manual support application by allowing users to add reaction symbols via `apply_load()` and manually specify boundary conditions.
- Improved support-handling logic to recognize and process both `apply_support()` and manual support workflows consistently.
- Added internal validation checks to ensure manually defined supports integrate correctly with reaction-solving processes.
- Updated docstrings to document both `apply_support()` and manual support application methods.
- Refactored related methods to ensure seamless handling of both workflows.
- Added test cases to validate the manual support application workflow and ensure consistency with the Beam module.
- These changes resolve issue [#28306](https://github.com/sympy/sympy/issues/28306).

**Merged Pull Request**:
- [#28305](https://github.com/sympy/sympy/pull/28305)

### 3.2.4 Extended TDD-based test coverage

Following a test-driven development (TDD) workflow as recommended by my mentor, comprehensive test cases were implemented for all enhancements made to the Column module, including the  `max_axial_force()`, `max_extension()`, and improved `apply_supports` functionality. These tests validated the correctness of method  maximum value calculations, manual support application, and consistency with the Beam module. This rigorous TDD approach significantly enhanced code quality, reliability, and maintainability.

### 3.3.1 Streamlined `solve_reaction_loads`

The `solve_reaction_loads` method in the Structure2D module previously required users to manually input reaction symbols, which was prone to errors and confusing, The user needs to assign variables to apply_support() method number of variables depends on the type of support or user should guess and pass symbols.

**Changes Made**:
- Modified `apply_support` to automatically track and manage reaction symbols internally, eliminating the need for manual symbol input by users.
  Modified `solve_reaction_loads` to handle those reaction symbols internally rather than depending on user
-  Updated test cases to reflect the simplified workflow.
- Updated documentation to reflect the simplified workflow.

### 3.3.2 Improved reaction symbol naming for readability

The `apply_support` method in the Structure2D module previously generated reaction symbols with less intuitive naming, such as `Rh = Symbol(f"R_h__{round(x,2)},__{round(y,2)}")`, `Rv = Symbol(f"R_v__{round(x,2)},__{round(y,2)}")`, and `T = Symbol(f"T__{round(x,2)},__{round(y,2)}")`. These names were not descriptive enough, making it challenging for users to interpret reaction forces and moments 2D structures.

**Changes Made**:
- Updated the reaction symbol naming convention to use clearer, more descriptive formats: `Rh = Symbol(f"R_h (x={round(x,2)},y={round(y,2)})")`, `Rv = Symbol(f"R_v (x={round(x,2)},y={round(y,2)})")`, and `T = Symbol(f"T (x={round(x,2)},y={round(y,2)})")`, explicitly indicating the reaction type and coordinates.
- Ensured consistent and intuitive naming across Structure2D for betterness.
- Updated documentation to detail the new naming convention.
- Updated test cases with the new reaction symbol names .

### 3.3.3 Integrated horizontal (axial) analysis via Column module

The Structure2D module previously lacked support for horizontal (axial) analysis and it simply returned one horizontal reaction load using newton third law, limiting its ability for handling axial forces and displacements. This integration was essential to enable comprehensive 2D structural analysis combining both Beam and Column functionalities.

**Changes Made**:
- Imported and initiated column instance for usage throughout the module
- added self.variables like self.load_qx for column loads which are horizontal


#### 3.3.3.1 Extended `apply_supports` with column module

The `apply_supports` method in Structure2D previously supported only Beam-related boundary conditions, lacking integration with the Column module for axial constraints.

**Changes Made**:
- Extended `apply_supports` to apply boundary conditions for columns by appending axial constraints (e.g., `column._bc_extension.append(unwarap_x)`) for pin,  and fixed supports.
- Added horizontal reaction loads with angle 0 for supports as similar as vertcal loads with angle 90
- Ensured consistency in support application across Beam and Column components, with appropriate boundary conditions for each support type (pin, roller, fixed).

#### 3.3.3.2 Adapted `apply_loads` for hinge and column support

The `apply_loads` method in Structure2D was not fully equipped to handle loads associated with column supports or hinges, limiting its ability to apply axial loads correctly.

**Changes Made**:
- Adapted `apply_loads` to support axial load application for column components and updated alex algorithm to handle horizontal loads using column and all projections ,signs are inspired and written using alex algorith.
- Updated the method apply load for hinge application using alex algorith and added an addition if loop for handling hinge conditions.

### 3.3.4 Added `apply_rotation_hinge` support

The Structure2D module previously lacked the ability to apply rotational hinges, which are critical for modeling 2D structures with points that allow rotation without moment resistance. This limited the module’s capability to handle complex structural configurations involving hinges.

**Changes Made**:
- Implemented the `apply_rotation_hinge` method to add a rotational hinge at specified coordinates `(x, y)` in the Structure2D module.
- Generated a unique symbol for the hinge (`T = Symbol(f"T__{round(x,2)},__{round(y,2)}")`) and applied it as a load with `order=-3` to represent the hinge’s rotational behavior.
- Added the hinge symbol to `rotation_jumps` for tracking and set the bending moment to zero at the hinge’s unwrapped position (`beam.bc_bending_moment.append((unwarap_x,0))`).
- Updated documentation to describe the new method and its application in 2D structures.
- Added test cases to validate the correctness of the rotational hinge problems for structural analysis.

### 3.3.5 Added methods: `deflection()`, `extension()`, and `axial_force()`

The Structure2D module  lacked dedicated methods to compute deflection, extension, and axial force as column was missing, limiting its ability to provide comprehensive 2D structural analysis. These functionalities were critical for analyzing both vertical and horizontal behavior in structures.

**Changes Made**:
- Implemented `deflection()` to calculate vertical deflection at specified coordinates or return the deflection equation for the entire structure.
- Added `extension()` to compute axial displacement at specified coordinates or provide the extension equation.
- Introduced `axial_force()` to calculate the axial force at specified coordinates or return the axial force equation, enhancing horizontal analysis.
- Ensured all methods support both specific point queries (using x, y coordinates or unwrapped positions) and general equation outputs.



### 3.3.6 Plots for unwrapped 1D structures: `plot_deflection()`, `plot_extension()`, `plot_axial_force()`

The Structure2D module lacked plotting methods to visualize deflection, extension, and axial force distributions for unwrapped 1D representations of 2D structures, which are essential for analysis.

**Changes Made**:
- Implemented `plot_deflection()` to generate a plot of vertical deflection along the unwrapped structure, with clear titles, labels, and a distinct color (red).
- Added `plot_extension()` to visualize axial displacement along the unwrapped structure, using a magenta color for clarity.
- Introduced `plot_axial_force()` to plot the axial force distribution along the unwrapped structure, with a cyan color for distinction.
- Added comprehensive docstrings for each method, including examples demonstrating usage with sample structures, loads, and supports.
- Ensured all plots require `solve_for_reaction_loads()` to be called first, raising a `RuntimeError` if not, to prevent invalid outputs.

### 3.3.7 Added 2D structural plots: `plot_axial_force_on_structure()`, `plot_shear_on_structure()`, `plot_bending_on_structure()`

The Structure2D module previously lacked visualization methods to display axial force, shear force, and bending moment distributions directly on the 2D structure geometry it just had plots for the unwrapped 1d structures, limiting the ability to analyze these quantities in their actual spatial context.

**Changes Made**:
- Implemented `plot_axial_force_on_structure()` to visualize axial force distribution on the 2D structure, drawing ticks perpendicular to each member with lengths proportional to `N/factor` (default factor=75.0) and optional value labels at member ends.
- Added `plot_shear_on_structure()` to plot shear force distribution on the 2D structure, using ticks proportional to `V/factor` (default factor=75.0) with a distinct green color and optional value labels.
- Introduced `plot_bending_on_structure()` to display bending moment distribution on the 2D structure, with ticks proportional to `M/factor` (default factor=150.0) in blue and optional value labels.
- Developed a helper method `_plot_expression_on_structure()` to reduce code duplication, handling tick plotting, filled polygons, and value annotations for all three plots.
- Ensured plots include semi-transparent filled polygons between the structure and force/moment lines for better visualization, with equal aspect ratios and gridlines.
- Added comprehensive docstrings with examples for each method, demonstrating usage with sample structures, loads, and supports.
-The factor is an option to visualize the plot for better when the values of the quantities are very small or large making the plot hard to analyze the factor can be passed so that the magnitude of the expression lines perpendicular are controlled

### 3.3.8 Added `plot_deformation_on_structure()`

The Structure2D module previously lacked a method to visualize the deformed shape of the 2D structure, which is essential for understanding the combined effects of axial and vertical displacements under applied loads and supports.

**Changes Made**:
- Implemented `plot_deformation_on_structure()` to visualize the deformed geometry of the 2D structure, plotting both the original and deformed configurations.
- Used symbolic geometry and displacement functions (`uz` for deflection, `ux` for extension) to compute deformed coordinates, scaled by a user-defined factor (default `1/10000.0`) for visual clarity.
- Generated plots with solid black lines for the original structure and distinct lines for the deformed structure, including a legend and equal aspect ratio for accurate representation.
- Added comprehensive docstrings with an example demonstrating usage with a sample structure, loads, and supports, requiring `solve_for_reaction_loads()` to be called first.



### 3.3.9 Updated summary to include axial force and mid-point bending data

The `summary` method in the Structure2D module previously reported only reaction loads, bending moments, and shear forces at points of interest, omitting axial force data and mid-point bending moments, which limited its utility for comprehensive 2D structural analysis.

**Changes Made**:
- Enhanced the `summary` method to include axial force data at points of interest, such as load application points and supports, using both positive and negative sides of unwrapped positions.
- Added mid-point bending moment calculations for each member, computed at the midpoint of bending moment segments to provide more detailed structural insights.
- Updated the output format to display axial force values and mid-point bending moments alongside existing metrics, using consistent 2D coordinate notation (e.g., `(x=0.00,y=0.00)`).
- Improved the `_print_points_of_interest` method to handle axial force and mid-point bending data, ensuring accurate coordinate mapping and rounding as specified by the user.



### 3.3.10 Enhanced summary to show actual 2D coordinates, not just unwrapped 1D points

The `summary` method previously reported points of interest using only unwrapped 1D coordinates, which obscured the actual 2D geometry of the structure and made it difficult for users to relate results to the physical structure.

**Changes Made**:
- Modified the `summary` method to display actual 2D coordinates (e.g., `(x=0.00,y=0.00)`) alongside unwrapped 1D positions for reaction loads, bending moments, shear forces, and axial forces.
- Updated `_print_reaction_loads` and `_print_points_of_interest` to map unwrapped positions to their corresponding 2D coordinates using `_find_unwrapped_position`, ensuring accurate representation of the structure’s geometry.
- Ensured consistent formatting with 2D coordinates for all points of interest, including mid-points of members for bending moment calculations.


