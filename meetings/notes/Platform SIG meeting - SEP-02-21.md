
Notes from meeting:

Attendees:
bosnichd
JT
Roddie
Ronald
Royal
ScottR

[anyone I missed?]


- Roddie
-- no moderator
--- what's plan B?
--- plan C

JT
- pasadena
-- ejectable hd's
-- new build workstation not set up yet
-- moving to SD cards
-- in sync with ubuntu
-- then rhel/centos/fedora
--- will reach out to me [RoddieKieley]


amzn pratik is the moderator
- Ronald

- backup moderator is colin
- 

Roddie Kieley
- workflow for triage

JT
- confirms workflow
- sig-release
-- needs sig

bosnichd 
- issue on macos input - simliar windows issue closed recently

#3759
- ScottR
-- Atom having M1 related issues
-- windows fixed
-- mac needs application

Roddie
- do we remove the needs-triage issue?

Scott
- yes

JT
- verify status what kind of bug / issue
- assign to someone
- remove needs-triage
- 

Ronald
- knows the person for #3758
- tom the right person to talk to
- assigned to tomhh
- minus needs-triage

Ronald
- #3554
-- graphics team owns
-- will update

Ronald
- goes to lumberyard-employee-dm

#3463
Ronald
- maybe explict nodes?

Scott
- content team

Ronald
- assign to carlito tun (sp?) / sig-content
- sig/content
- could be a binding error
- incorrectly bind the world transform
- might be easy fix
- if more complicated baked into script canvas will be bit more work
- should be fine

Ronald
- system team looking at same issue for Android
- good for sig/platform
- will need to find an owner

Ronald
- this team probably the owner
- need to find the actual cause
- more investigation


Ronald
- animiation editor
- 3rd party original
- could be recent changes to integrate with o3de
- eMotionFX

Royal
= animation so most sits in content
- animation systems are in content
toss up

Ronald
- been assigned to chris burel
- he is on loan to help with linux
- platform or content ok

Royal
- Ap closing for mac and linux like windows
- removed needs-triage

bosnichd
- someone already looking into it?
- will see if can dig up the info

JT
- if verified remove needs-sig
- 

Royal
- will share screen
- needs-sig

#3882
Royal
- sig-build?

JT
- sig-build yes

Roddie
- agreed

Royal
- reviews needs-sig label
- triage good
- suggestion
- wikicollection
- where do we start with raw content for the docs folks

Roddie
- AI: make some notes on macOS

JT
- will scan for macOS users and reach out for content

Royal
- Esteban put some great content in
- doesn't have to be pretty

JT
- other platforms
- having people building will generate more interest
- "taking wrong step in the right direction preferable to wrong step in the right direction"
 

Royal
- anything else on the agenda?

JT
- checked last meeting notes
- agreement previously if gets to be 10 or so items on the needs triage
- would schedule weekly triage meeting
- nothing else that needed to be from prior

Royal
- have someone scan the needs-triage

JT
- folks were talking about that

Royal
- see sig-platform for pasted search to find them
- how's linux feeling
- what's the state of the Union [for Linux]

Roddie
- provides fedora update

Royal
- state of the union chart for linux distro's
- spreadsheet of what's what with what distro
- links to specific github issues
- list of distros that we think we want to start with
- take major features and break them down
- gems or components
- distros together first them gems and modules pretty fast
- can we prioritize distros
- AI: what are the distros that we want to start talking about supporting

JT
- taking shape now

Royal
- can work on the spreadsheet
- these work these don't

JT
- going to add
- do we have any CI goals
- affiatied with ASWF
- have a really strong CI WG
- unify CI general online building
-- shared library tools
- is there any push to have CI geared toward the repo manaagment
- agenda line item

Royal
- sig-build manages that

JT
- sig-platform having input?

Royal
- our responsiblitiy to update build scripts for the platforms

Roddie
- vfxplatform.com

JT
- will dig up the spreadsheet [re: vfxplatform details]

Royal
- would be great to get same
- sig-build driven

JT
- AI: will post link to actual spreadsheet to vfx platform
- what project in what state
- could be interesting info


For distro / component information:
wiki page inside sig-platform o3de/wiki/sig-platform then we can mature it out from there

