# Platform SIG meeting - MAR-25-22

### Facilitator:
byrcolin[Amazon]

### Meeting Notes:
RoddieKieley [Red Hat]

## Agenda
See [Proposed SIG-Platform meeting agenda for March-25-22](https://github.com/o3de/sig-platform/issues/53)

## SIG Updates
None

## Meeting Agenda

Discuss [RFC: Python Upgrade to 3.9](https://github.com/o3de/sig-platform/issues/54)
- Steve Pham, i.e. [spham-amzn](https://github.com/spham-amzn) who opened the above RFC gave a quick overview of its purpose and rationale for raising the issue.
- This was followed by four minutes for each participant to review the RFC prior to discussion.
- Discussion of reasons for opening RFC for upgrade now
  - EOL date of 3.7, June 27th 2023, or +1.5 years from now vs. 3.9, October 5th 2025, or 3.5 years from now
  - CVE response and the need to be proactive in the upgrade to have adequate time
- Discussion of impact of the upgrade when it occurs
  - upgrading to 3.9 versus 3.10 with known issues against 3.10 atm and 3.9 being less bleeding edge
  - 3.9 at 3.5 years of support left has adequate time for CVE support left
  - not required to involve TSC as this appears clearly in sig-platform purview
  - significant part of testing infra is based on python so this is a concern
    - may have cross-sig dependency and need sig-build assistance
  - as mentioned in the RFC itself there are two dependent 3rd Party Libraries, Pyside2 and PyBind11
- Motion to adopt the RFC passed
  - decided to leave the RFC open so that community feedback, if any, could be received
  - timeframe to be decided, however [spham-amzn](https://github.com/spham-amzn) agreed to action the RFC when appropriate
  - when appropriate; estimated time to completion was two months; however next python CVE likely the trigger for the work to start


## General discussion

- No other open PRs or discussions to be addressed atm.

### Office Hours

- During this week's TSC meeting there was a discussion of having dedicated office hours where a given SIG's channel on Discord would be "manned" for Q&A
- It was discussed that it might be a good plan to do meetings one week and office hours the next.
- Actions were taken to plan an alternating schedule of meetings and office hours as well as see if any coordination across SIG's was desired by the TSC

### Triage

- None this week, but discussed to be aligned with office hours.

### Actions
- byrcolin[Amazon] to update calendar for bi-weekly meeting, with alternating office hours in Discord
- RoddieKieley [Red Hat] to follow-up with TSC to see if any coordination on office hours is required
