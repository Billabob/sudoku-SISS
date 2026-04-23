# Sudoku-SISS
Strong inference set based AIC solver & grader

[https://billabob.github.io/sudoku-SISS/SISS.html](https://billabob.github.io/sudoku-SISS/SISS.html)

## How to use:

On the left side is the grid display. It displays the current puzzle, as well as the state of candidates on the board.

On the right side is all the solver buttons & logs. I'll list them off from top to bottom along with their purpose.

### Puzzle Entry Mode

(Not implemented yet)

### Solver mode

Clicking "Solver" takes you back to the main solver.

"Enter puzzle import string:"<br>
Here you can paste in an 81-character puzzle string. Givens are listed in top-down left-right order and empty cells are represented by a 0 or .

"Hint" will find and display the easiest technique currently available in the puzzle, according to the hierarchical solver.

"Step" will find and execute the easiest technique currently available in the puzzle, according to the hierarchical solver.

"Solve" will keep stepping until the puzzle is finally solved.

Solve log:

"Automatically solve naked singles" is an option that does what it says on the tin. It's on by default, turn it off if you want them to be hints.

### Batch mode

Clicking "Batch mode" brings you to the batch solver.

The first box with default text "Paste newline-separated list here" is an input field where you can paste a list containing as many puzzles as you want. Alternatively you can upload a .txt file with the same format.

Mode is currently unused, might come up with a use for it later.

"Save output to file" is self-explanatory, as is "Run" and the Batch output text box.

### Settings

TODO

## Solver

This page implements a hierarchical solver & grading system, similar to the SE rating scale. However, only basics (singles, blr, subsets) are implemented explicitly, everything else is implemented implicitly as a generalised AIC.

Each step of the solver starts with the most basic techniques and climbs the ladder of difficulty until the puzzle finally yields and a candidate is eliminated. After this a new step begins and the solver goes back to the basic techniques and climbs again. This repeats until the puzzle is solved. Puzzles are graded based on the hardest technique required to solve the puzzle, no matter how many times the technique is used.

The technique ratings are as follows:<br>
0:      Naked/Hidden Single<br>
0.0001: Box/Line Reduction (Claiming, Pointing)<br>
0.000N: Size-N Hidden/Naked Subset<br>
1.NNNN: A length-N AIC.<br>
2.NNNN: A length-N Almost-AIC.<br>
3.NNNN: A length-N Almost-Almost-AIC.<br>
And so on.

### AIC

A basic outline of AIC is given in [my article on AIC in Sudoku](https://billerbop.blogspot.com/2025/12/aic.html). The solver will always find the shortest available AIC that gives eliminations.

This implementation is very generalised and relies on the concept of Strong Inference Sets (SIS), which I briefly touch on in [part 2 of my AIC article](https://billerbop.blogspot.com/2025/12/aic-2.html#groups).

The basic gist is that an SIS is a set of premises where at least one must be true. If you divide this set into two smaller subsets which together contain every element of the set, there is a strong inference between these two subsets, i.e. at least one of them must contain a true premise. In order to use these splits in AIC, the subsets require a mutual weak inference, so the only splits considered are those where both subsets have at least one mutual weak inference.

Basic examples of SIS are "all the instances of 1 in row 3", or "all the candidates within r2c2". At least one element of these sets must be true. The solver first collects all SIS in the grid, then it finds all viable splits of these SIS, and then it iteratively combines strong inferences in order to prove new strong inferences, through the mechanism:<br>
A = B - C = D  =>  A = D<br>
If all possible strong inferences have been proven and there are no eliminations, these new strong inferences are leveraged to prove new weak inferences through the mechanism:<br>
A - B = C - D  =>  A - D<br>
After this the process repeats, with these new weak inferences being used to identify new viable splits in the SIS. If there are still no eliminations, the process repeats until no new splits are identified.

Regional & Cellwise strong inference sets are implemented so far, later on I will implement ALS, AHS, Kraken Fish, Avoidable Pattern Guardians, and whatever else I can think of.

Even with all the exotic strong inference sets piled on to the solver, this is still not guaranteed to be capable of solving *every* puzzle, so the ratings should be treated more as a categorisation, i.e. "this puzzle cannot be solved with regional/cellwise AIC", or "this puzzle can be solved with ALS-AIC".

## TODO list:

Solver:<br>
- Almost-AIC through A-B=C-D<br>
- ALS, AHS, Kraken Fish, AP Guardians, etc...<br>
- Better-formatted Eureka strings with all short AIC named properly<br>
- Some options to speed up batch mode (like applying all AIC eliminations at once etc.)<br>
- Rings

Site:<br>
- Settings page with toggles for strategies and SIS types

## Changelog

### v0 - 2026-04-23

Most of the code could be ripped from my KenKen solver so this is fairly complete for a v0, although that solver did not implement Strong Inference Sets, so much of the AIC solver had to be built from scratch. Almost-AIC is not implemented yet, visual hints are implemented, batch mode is implemented, only regional & cellwise SIS are implemented.
