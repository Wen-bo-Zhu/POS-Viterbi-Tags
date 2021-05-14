# POS-Viterbi-Tags

Build and run the file "POS_viterbi_Tags", it would
output "submission.pos" which is the output of the test data.


At first, I used hashmap to create a table for prior probability with
each of POS tag, and I read test files to list all the words
and create a likelihood table for words that is already known.


Next, I wrote a function handle each specific cases
of OOV as much as possible for improving the score. For example,
the word ends with "ble" or "ive" would be identified as adj
which is "JJ" in the tagging process.


Then, I implement the Bi-gram Viterbi algorithm and use
log probability to optimize the performance.
