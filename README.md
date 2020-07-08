PyLadies Election Repository

This repository contains the tools and documentation for the way the PyLadies Elections are presently operated. This document, inspired by the Python Software Foundation elections procedure, will cover the tools in the repository and the processes that the Election Administrator is responsible for.

## A New Election

At least once every 2 years, the PyLadies runs an election. We (at the time of this writing) use the hosted version of [Helios](https://vote.heliosvoting.org/). This is open source software that is freely available on [GitHub](https://github.com/benadida/helios-server). Election administrators should log into Helios using one of the options on the front page.

Once signed into Helios, a button should appear labeled "Create Election". Click it, and then the administrator will be prompted for the following information:

- "Short Name" - Includes the name and year e.g. pyladies-council-2020
- "Name" - a descriptive display title, e.g., "PyLadies Global Council 2020 Election"
- "Description" - in the past, administrators used this to explain Approval voting and the importance of the election
- "Type" - there are two options here, use "Election"
- "Use voter aliases" - in the past this option was used but it's not apparent if it's 100% necessary
- "Randomize answer order" - this should help avoid bias in the ordering of candidate names, use this option
- "Private?" - PyLadies elections are not open to the public to vote on, so check this box
- "Help Email Address" - decide on who should be fielding emails in the event that someone needs help with the election, in the past this has been the administrator's email but even with that people will email PSF staff instead
- "Voting starts at" - this is the date and time that the election starts
- "Voting ends at" - the date and time that the election ends. Use Anywhere on Earth (AoE) to determine the end of the election. For example, if the election runs from 1 June until 10 June AoE, then the datetime for "Voting ends at" should be "11 June <year> 12:00 UTC".

Once these are complete, begin filling out the questions for the election.

## Adding a Question

Click "Questions (#)" (where # will be the number of questions already there) and add the first question.
Here, fill out the question(s) on the ballot. For example in a PyLadies Global Council Election, fill out the name and link to their candidacy statement on the PyLadies Elections website. If there are 10 candidates, voters should be able to pick between 0 and 10 answers. The election should be an absolute election.
If there are more questions on the ballot, use judgement to determine how to add them.

## Setting up Voters

The Election Adminitsrator (EA) will receive a file with the first name, last name, and email address of a voter. In order to add voters to an election, Helios requires they be uploaded in a CSV file in the format:
voterid,email address,full name

For example:
`ddf82bd56adb423eac5e720f1687e705,PyLady 1,pylady@gmail.com`

The format of the file the election administrator typically gets is of the format: `FirstName,LastName,Email`

So to generate a random voter ID for each voter and to format it appropriately, there is a tool in this repository to handle it called `convert-exported-csv-for-helios.py`. This will generate the necessary format for Helios from the CSV file the EA receives.

It's important to note here that not all voters have a first or last name and some don't have either.

## Running an Election

Running an election begins once the election starts. While an election is in progress, a few things may happen:
-  New voters may need to be added as they were missed when the original voter list was generated
- Some voters may need you to send/re-send their ballot notification email
- The following sub-sections will cover these in detail.

## Adding Missed Voters

Helios allows us to add new voters after an election has begun although the other details of the election or questions cannot be altered. The process for adding new voters is the same as is detailed above in "Setting up Voters". The EA will receive a new file full of the details of the voters who were missed. Using that, the EA can generate a new CSV to upload to Helios using the same tooling.

## Re-sending Ballot Emails

In the event that the voter had a name in their original voter rolls that the EA was sent, the EA can search in Helios by the voter's name. If they didn't, it is possible to use the generate-reminder-urls.py script to search by email address and generate the URL for the web-page that the EA can use to re-send ballot emails.
For example, let's say our election ID is in progress and three users have notified the EA that they didn't receive their ballot, in that case the EA can do:

```
python3 generate-reminder-urls.py \
    --election=c01c9b04-68cc-11e8-b432-fe01b9cec94e \
    --voter-file=path-to-helios-csv-file.csv \
    voter0@example.com voter1@example.com voter2@example.com
```

And if those email addresses are in the CSV file, it will print out URLs like this:
- https://vote.heliosvoting.org/helios/elections/c01c9b04-68cc-11e8-b432-fe01b9cec94e/voters/email?voter_id=166872007e7141a5a23a2e2db3d3116d
- https://vote.heliosvoting.org/helios/elections/c01c9b04-68cc-11e8-b432-fe01b9cec94e/voters/email?voter_id=995bbc4e5ece4b74b4f3d5937f0a0e9e
- https://vote.heliosvoting.org/helios/elections/c01c9b04-68cc-11e8-b432-fe01b9cec94e/voters/email?voter_id=90935c305d9446d3bda059eacb266630

Clicking those links will allow the EA to resend the ballots.

If, however, one of the email addresses isn't in the file, it will print something like this:
https://vote.heliosvoting.org/helios/elections/c01c9b04-68cc-11e8-b432-fe01b9cec94e/voters/email?voter_id=166872007e7141a5a23a2e2db3d3116d

No voter with email "voter1@example.com"
https://vote.heliosvoting.org/helios/elections/c01c9b04-68cc-11e8-b432-fe01b9cec94e/voters/email?voter_id=90935c305d9446d3bda059eacb266630

## Closing an Election

The end of an election requires the EA to verify the results of the election with Helios and then present the results to the PyLadies Global Council. The Global Council will then announce the results to the community.

For the inaugural 2020 election, the results will be presented to the interim PyLadies Global workgroup. 

## Achieving Quorum

The PyLadies rules stipulate that that one-third (1/3) of the members eligible to vote will constitute a quorum.
For example, if there are 999 voters in an election, there must be 333 votes cast at least in order to declare quorum.

## In the Event of a Tie

In the case of a PyLadies Global Council election, the top N candidates are accepted based on the number of open seats in that Election provided that, with the top N candidates, no more than 33% of the Council is from the same country of residence. If there is a tie for the Nth seat, then there is a script that may be used to break the tie called break-ties.py.

Let's say that there is a 4 way tie between "Candidate 0 of Country A", "Candidate 3 of Country A", "Candidate 8 of Country A", and "Candidate 12 of Country A" and we need 2 more people to round out the total, then we would do:

```
python3 break-ties.py -n 2 \
    -c "Candidate 0" \
    -c "Candidate 3" \
    -c "Candidate 8" \
    -c "Candidate 12"
```

And it would print the winning candidates. Re-running this script will always result in the same candidates winning the tie breaker. This is by design so that the tie breaker results may be verified by someone other than the EA.

Now let’s say that there is a 4 way tie between "Candidate 0 of Country A", "Candidate 3 of Country B", "Candidate 8 of Country B", and "Candidate 12 of Country C" and we need 2 more people to round out the total. Country A holds 2 seats on the Council, Country B holds 3 seats, Country C 0 seats. The `break-ties.py` script is not required as Country B has already fulfilled its max seat allotment. The 2 open seats would be awarded to Candidate 0 and Candidate 12 accordingly.
