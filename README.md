# Reading an 824 rejection: which segment, which element, what to fix

An 824 is not an error message. It is a structured rejection with a location, and most small suppliers read it as 'the system is broken'.

A retailer rejects an EDI document and sends back an 824. The supplier sees a wall of segments and concludes the system is broken, then re-sends the same file. The 824 already names the transaction, the segment position, the element position and the kind of syntax error. Reading it takes ten minutes; guessing takes a week.

## The numbers that decide it

- **AK1 / AK2 locate it** — AK1 answers the functional group (which document type, which group control number). AK2 answers the individual transaction set, so you know whether one order or all of them failed.
- **AK3 is the segment** — AK301 names the segment ID, AK302 gives its position in the transaction set, and AK304 carries the syntax error code. This is the first thing to read - it puts you on a line.
- **AK4 is the element** — Inside that segment, AK401 gives the element position and AK403 the error code: missing mandatory element, too long, too short, invalid character, invalid code value, exclusion condition violated. That is the actual fix instruction.
- **AK5 / AK9 are the verdict** — AK5 says whether that transaction set was accepted, accepted with errors, or rejected; AK9 gives the same verdict for the whole group with counts of included, received and accepted sets. Two transactions failing out of forty is a mapping bug; all forty is a transport or envelope problem.
- **The fix order matters** — Fix one segment, re-send one document. Batch-fixing five segments and re-sending the file is how a supplier loses three days and cannot tell which change worked.

## How to do it, in order

1. **Read AK9 first.** One transaction rejected or all of them? That single answer decides whether you are debugging a map or a connection.
2. **Then AK3 and AK4.** Segment position plus element position is the line and the field. Write those two numbers down before touching anything.
3. **Compare against the retailer's guide, not the X12 standard.** A field that is optional in the standard is often required in the guide for that sales channel. The guide wins; that is what you are being validated against.
4. **Change one thing and re-send one document.** Keep a note of segment/element/code for each attempt - after three rounds you will see the pattern, and the pattern is usually the same missing qualifier.
5. **Fix the label last.** If the 856 was accepted but the load did not update, the problem is usually the physical label or the pack hierarchy, not the EDI file.

## Questions

**What does an 824 mean in plain English?**

The retailer received your document and is telling you what it did with it, field by field. It is an application-level response, not a transport failure - the file arrived.

**Why does the same file work at one retailer and fail at another?**

The X12 standard is the floor, not the contract. Each retailer's guide adds mandatory segments and restricts code values per sales channel.

**Do I need a VAN to read an 824?**

No. The 824 sits in the same mailbox/AS2 path as the rest of the traffic; reading it is a matter of having the file, not of which provider carried it.

## If you would rather not do this by hand

There is a free 824 decoder that turns a pasted 824 into 'segment, element, error, what to change' with no account, plus a service that reads a retailer's mandate letter and returns the document set, deadline and one fixed monthly price.

→ **EDI Rescue** — https://edirescue.com/edi-824-rejection?utm_source=github&utm_medium=repo&utm_campaign=edi-824-rejection-codes

Written by the person who built EDI Rescue. The decoder is free; the onboarding service is paid.

---

EDI Rescue · retailer EDI onboarding and 824 triage · edirescue.com
