# vibe-wichtel
Vibe coded wichtel app

## What it does

The app shows a simple user interface. The input is a list of people (name and e-mail).
After the list of people was entered, we start the shuffling - each person gets the name of another person.
The tool should send an e-mail to each person with the name of the target gift person.

## Promt

* Create one or more Wichtel groups.
* Input list of participants (name + email).
* Optional exclusions (e.g., spouses not gifting to each other).
* Shuffle to produce a Secret Santa assignment (a derangement: no one gets themselves).
* Send individualized emails: “You will gift to: {receiverName}”.
Prevent:
* Duplicate assignments

## Technology

* Very basic and simple
* No persistence needed
* Use a public mail sender tool that does not require to log in (if that exists).
* Create it in java 25 and use gradle as build system
