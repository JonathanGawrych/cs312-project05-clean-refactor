# cs312-project05-clean-refactor

This is a refactor of the Project 5 framework code for BYU CS 312.

It contains the following key differences:
* There are now two types of objects: problems and solvers. Each problem contains a solver.
* Each solver implements the Solver interface.
* Each solver implementation has its own file. 
 * The Default implementation is found in DefaultSolver.cs.
 * The Branch and Bound implementation is found in BranchAndBoundSolver.cs.
 * The Greedy implementation is found in GreedySolver.cs.
 * Your own implementation, which you will have to write for the group project, is now called the Fancy Solver. It is found in FancySolver.cs.
* All the methods that pertain to drawing are now contained in the Form1.cs file (where they should be).
* A lot of methods with duplicated code have been condensed to use helper methods.

The interface is different in the following ways:
* The New Problem button was renamed to Generate (which makes more sense).
* Now contains a Reset to Defaults button

If you think you can make this framework code even better, you are more than welcome to submit a pull request.

Also, if something in the code doesn't make sense, feel free to leave a comment and someone will change it.



# A note to the user: THE DOTS WILL LOOK DIFFERENT, BUT STILL ARE VALID

Here's why:

In the old code, there was a lot of duplication. One of the functions that was unnecessarily called more times than it needed to be was the ProblemAndSolver.resetData() function. This function make most of the calls to the random generator, which in turn generated the coordinates for the points.

Look at the old reset() function (Form1.cs):

    private void reset()
        {
            this.SetSeed(); // The random seed is initialized here
                            // The ProblemSolver.resetData() function is called as part of this
                            // which randomly generates coordinates for n points to be displayed 
                            // (where n is the problem size that the user specified in the box on the GUI)

            int size = getProblemSize();
            int timelimit = getTimeLimit();
            HardMode.Modes mode = getMode();
            
            tbCostOfTour.Text = " --";
            tbElapsedTime.Text = " --";
            tbNumSolutions.Text = " --";

            CityData.GenerateProblem ( size, mode, timelimit );  // In GenerateProblem(), the resetData() function is called again
                                                                 // without resetting the random generator seed. This means that the
                                                                 // generator will continue generating random numbers from where it
                                                                 // stopped when it was called before. This will cause the points
                                                                 // that are displayed to be different than the points that would
                                                                 // have been displayed if the random generator was only called once.
        }

This means that the points displayed on the screen are dependent upon the GUI consistently calling the random generator twice as many times as necessary.

In the new reset() function, the generator is called only once (Form1.cs):

    private void reset(int seed, int problemSize, int timeLimit) {
            this.toolStrip1.Focus();  // Not sure why this is here; leftover from previous code
            HardMode.Modes mode = getMode();

            CityData = new Problem(seed, problemSize, timeLimit, mode);  // Creating a new problem calls the resetData() function
                                                                         // ONCE ONLY. This means that the random generator is also
                                                                         // called ONCE ONLY.

            // Set the values of the text boxes in the GUI
            tbSeed.Text = seed.ToString();
            tbProblemSize.Text = problemSize.ToString();
            tbTimeLimit.Text = timeLimit.ToString();
            tbCostOfTour.Text = " --";
            tbElapsedTime.Text = " --";
            tbNumSolutions.Text = " --";              // re-blanking the text boxes that may have been modified by a solver
            this.Invalidate();
        }

This implementation calls the random generator only once. This means that the random generator is called half as many times as the old implementation did to print dots to the screen. Consequently, the pattern and spread of the dots are different than the old ones were. 

*THIS DOES NOT MEAN, however, that the dots are invalid*; they are still good points with which you can create your project. It just means that they are in a different location than points generated by the same seed using the old GUI.

