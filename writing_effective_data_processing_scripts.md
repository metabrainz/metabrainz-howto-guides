# Writing effective data processing scripts

Writing data processing scripts that output a pile of data can be challenging to write.
Providing the right amount of data and not overwhelming the user is an art form that
you can learn from experience. Many of these concepts are not immediately obvious, 
so this document aims to lay out some of the basic considerations for writing
data processing scripts that are good citizens and produce effective output.


## Technical considerations

Often times scripts are called from cron jobs and other automated processes and not from humans. A human
can see that a script has failed and take the correct action, but if a script calls another script,
the calling script needs a clear signal that the script completed successfully or had some error occur.

Each program running in Linux can produce an 8 bit _exit code_. Exiting with exit code 0, means that 
no errors occured. Anything other than a 0 exit code indicates an error -- exactly which error will
depend on the environment and project in which your code is running. If the project has no conventions
for exit code, one should be set up.

Next, some programming languages print nice and concise error messages when a script crashes. This is great
for engineers trying to debug programs, but anyone else who is not famaliar with the program will have a hard
time making sense of a stack-trace output. Especially when that person doesn't understand the language that
the script is in or if they are not programmers at all. Make sure that the top level portion of your data
processing script catches all the relevant exceptions and prints useful error messages that non-engineers can
use.


## Engineering/Scientific considerations

One of the core principles of science and engineering is to make a process repeatable and understandable. For
data processing many of the same concepts apply, otherwise you get confused with what work has been done in the
past and what should be done next. A good data processing script will give the user a good understanding of what
happened and how the run went -- the user should be able to follow the process along.

One of the most important priciples for repeatable engineering is to identify which script and data produced
the output of a program. You need to know the version id of the script or perhaps the githhub commit hash of
the script to associate output with the code that produced the output. This also applies to the data sets that are processed 
by a script. What are their version numbers or dates? Was the whole data used or just a subset? If a subset, how was the subset
produced? Conderider printing the query that produced that data that went into a particular script! 

For any script that may be run multiple times in succession and the output saved for future consumption, it must be clear
what data/inputs changed between the subsequent runs.  Also, which machine/cluster was the script run on? A small data set 
running in 7.3s on a personal laptop is very different from a full dataset on a full cluster in 7.3s -- they are not comparable 
at all, so your script needs to note this as well. Also print out the date and the time that the script was started on, when it 
finished and how much wall-time elapsed.

When you print numbers that have units, always include the units. When printing floating point numbers always
print the right number of significant figures. For instance, don't output `in 3.45923945778734`, instead output `in 3.4s`. But
if a script takes a few thousand seconds to complete, there is no need to print any decimal points -- they are not relevant.


## Look and Feel considerations

Finally, there are a number of considerations on how data should be output to be useful to the user of the script.  Do not 
print dicts, hashes or raw JSON, especially unformatted JSON. These outputs are fine for programmers to read, but unformatted
data structures are painful to look at. And if they are painful to look at your script will not be effective and could be a waste
of time.

Another important concept is to make a script useful without having to lookup more data to interpret the output of the script.
If the user needs to copy and paste an MBID into a browser to find relevant information the script is not very useful at all. Always 
attempt to print human readable strings that are connected to the data and avoid printing raw MBIDs, unless they are really needed.
If the relevant human readable data is not available, consider querying for this data, if possible.  This isn't always going to be possible, 
but when the data is to hand, it should be displayed.

Dates should always be printed in ISO-8601 format (e.g. `2019-04-08`) and you should never print out unix epoch timestamps --
they are very unfriendly to consume.


## Applying common sense

The final lessons is: _Do what is right, not what this guide says!_

You'll need to carefully choose relevant information to display -- it is an art form to display just enough information
without overwhelming the user. And sometimes you'll simply not be able to print the data as useful as it should be --
sometimes that will not be pratical to do. Start simple and then try and understand the output of the script -- if it doesn't make sense, ask yourself what data is missing and would make the script more helpful. Add that data if you can!

Also, remember the world was not built in a day. Sometimes is it a good idea to write a script and have it produce some output;
set it aside for a day and come back to it (or give it to someone else to read/interpret). Does the output still make sense?
If not, you have more work to do. 

But, don't blindly follow this guide if it doesn't make sense in your context!

