Platform SIG meeting - AUG-12-21

Facilitator: byrcolin[Amazon]

Meeting Notes: AMZN_kinglawr

review previous meeting notes:
no new introductions
no opinion on frequency
no opinion on the charter

agenda for this meeting:
The development branch has been updated to fix a crash on Linux/Ubuntu that prevented the Editor from starting up. (Related to command queues and swap chains not being ready for them) The fix is a work around, and an issue has been created to look into it deeper.

byrcolin - Does this mean the editor is running on Ubuntu?
spham - Yes, running on 20.04. It is rendering materials; Atom is rendering.

Other support such as shader compiling on Linux (with Vulkan) was updated previously, so we have a starting point to look deeper and uncover other issues with Linux

Other questions from Huawei about build/compile issues for Android 
spham - no clear communication whether android is supported
spham - been testing android , could not reproduce any of the issues they've seen. They were mentioning commands they never seen before
SJ - looks like they were using build scripts
Colin - encourage hwawei to create a github issue for us to track; next follow-up, we will ask them to cut us a ticket

Contribution:
another person from huawei how they want to contribute to linux, needs contact info
JT from SCB (from the blender world) works heavily in linux and wants to come contribute in 2-3 weeks time
JT - Prefers Arch Linux for distro, wants to be bleeding edge and works with certain video cards they use
LK - Asked JT to write a RFC for Arch Linux distro support

Other:
any issue added to github, goes through sig-release, triage and then assign the appropriate sig label
the sig group then needs to triage the issues and assign them to the correct developers
we need a better mechanism for triaging issues; when does the triaging session occur for sig-platform?
sig-platform triage meeting, weekly or increased cadence to go over: https://lists.o3de.org/g/o3de-calendar/calendar for the triage cadence

action item:
https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Asig%2Fplatform
temp measure: if the number goes >= 10, schedule a 30 minute timeboxed triage meeting

would this be an automated method? manual triage?
