# Exercise - Persistence is key

For this exercise, use the CAN traffic parser you wrote in part 1 of
[section 4.2](../step-2/exercise.md) to parse same
[MPPT traffic log](../../misc/mppt_data.txt) as before—but now adapt your
code to store the CAN traffic in an SQLite database instead of printing to
the screen.

This exercise is very free-form. Choose whatever table structure you think
makes the most sense. Feel free to ignore the MPPT status message it's too
much trouble; certainly don't bother breaking down the error/limit flags
into individual bits.

> ### Why not a regular file?
> You might still be wondering what the point of using an SQL database is.
> After all, we could simply write the decoded CAN frames into a regular
> text file. The answer is that using an SQL database enforces structure
> of the decoded messages and integrates with observability platforms like
> Grafana. Also, databases are *much* more efficient than regular files
> when the amount of data get very large.
