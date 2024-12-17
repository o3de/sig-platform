# Discovery of Community Objects
We need a clear and easy way for community objects to be discoverable to the users of O3DE. We need an easy, decentralized way to help our users find community objects. 

# Why do we need to help the user find other community objects?
The goal of O3DE is to be the most flexible, modular, extensible and distributed open source engine in the world. In order to achieve the distributed aspect of the engine we need a simple and straight forward way to encourage community members to share their objects with everyone else in the community. Currently, we have a way for the user to link to remote objects, however we have no way of telling the user where these distributed community object can be found. So we need to have a way for a community member to communicate the existence of their objects to other users. We need to do this in a safe way, for us and them. We do not want any liability either, like a search engine.

# How can we implement this?
The best place for this type of discovery is the Project Manager. It is the place where the user goes to configure their engines, projects, etc. so it is used often by the user. It is also where we currently have a gui that allows users to add remote objects. Therefore, I would like to see a simple list of community objects just below the section where I am asked to type in the remote uri such that if I click on one of the entries it is filled in for me. Once added an new entry in the o3de_manifest.json should appear. Once added I should see an aggregation of all the remote objects read from all local O3DE objects registered.

## How do remote objects work?
The engine has only declares a couple canonical remotes, one being the o3de-extras repo. The engine could simply directly link to o3de-extras repo by adding this remote repo link to the engine.json:
### added to engine.json
```json
"remote": {
    "repos": {
        "https://github.com/o3de/o3de-extras.git"
    }
}
```
This is declaring to the project manager that this engine "knows about" a remote repo object, and we would like that object (and its children objects and all their remotes) to be shown to the user in the project manager. This is true fro any O3DE object type not just repos. So if there were remote gems or templates, etc. that the engine "knows about" that could be added here as well.
```json
"remote": {
    "repos": {
        "https://github.com/o3de/o3de-extras.git"
    },
    "gems": {
        "https://gitlab.com/Jess356/space.git",
        "https://github.com/byrcolin/marine.git"
    },
    "templates": {
        "https://some.url.com/o3de/templates/cooltemplate.git"
    }
}
```
### The remote link format:
To make this easy for the most situations these remote links should be able to handle direct links to object json or zip file on an http or ftp server or a source control link to a git repo. These remote links can one of a few kinds of links should be explicit but may be inferred.
<details>

1. An explicit link to a object json file on a web server.
```json
"remote": {
    "gems": {
        "https://some.webserver.com/somegem/gem.json"
    },
```
Or an inferred link to a object json file on a web server.
```json
"remote": {
    "gems": {
        "https://some.webserver.com/somegem"
    },
```
Since this link is in the "gems" list and gem.json is not explicitly stated, it will infer that "/gem.json" to be appended to the link.

2. An explicit link to a object json file on a ftp server:
```json
"remote": {
    "gems": {
        "ftps://some.webserver.com/somegem/gem.json"
    },
```
Or an inferred link to a object json file on a ftp server:
```json
"remote": {
    "gems": {
        "ftps://some.webserver.com/somegem"
    },
```
Since this link is in the "gems" list and gem.json is not explicitly stated, it will infer that "/gem.json" to be appended to the link.

3. An explicit link to a zip file on a web server:
```json
"remote": {
    "gems": {
        "https://some.webserver.com/somegem.zip"
    },
```
This zip file contains all the files for this gem, and therefore the gem.json is in the zip. The zip file is downloaded and the gem.json is extracted. Zip file names can not inferred.

4. An explicit link to a zip file on a ftp server:
```json
"remote": {
    "gems": {
        "ftps://some.webserver.com/somegem.zip"
    },
```
This zip file contains all the files for this gem, and therefore the gem.json is in the zip. The zip file is downloaded and the gem.json is extracted. Zip file names can not inferred.

5. An explicit link to a git repo:
```json
"remote": {
    "gems": {
        "https://some.gitserver.com/somegem.git"
    },
```
This git repo contains all the files for this gem, and therefore the repo will shallow clone the gem.json (if possible, if not full clone). Git repos names can not inferred.

6. An explicit link to a local object json file on a windows filesystem:
```json
"remote": {
    "gems": {
        "C:/path/to/somegem/gem.json"
    },
```
Or an inferred link to a local object json file on the filesystem:
```json
"remote": {
    "gems": {
        "C:/path/to/somegem"
    },
```
Since this link is in the "gems" list and gem.json is not explicitly stated, it will infer "/gem.json" to be added onto the link.

An explicit link to a local object json file on a unix filesystem:
```json
"remote": {
    "gems": {
        "/path/to/somegem/gem.json"
    },
```
Or an inferred link to a local object json file on a unix filesystem:
```json
"remote": {
    "gems": {
        "/path/to/somegem"
    },
```
Since this link is in the "gems" list and gem.json is not explicitly stated, it will infer "/gem.json" to be added onto the link.

Also uri notation for a local file will also work:
```json
"remote": {
    "gems": {
        "file://WorkStation/c:/byrcolin/somegem/gem.json"
    },
```
```json
"remote": {
    "gems": {
        "file://WorkStation/c:/byrcolin/somegem"
    },
```
Since this link is in the "gems" list and gem.json is not explicitly stated, it will infer "/gem.json" to be added onto the link.

Or omitting the domain infers 'localhost':
```json
"remote": {
    "gems": {
        "file:///c:/byrcolin/somegem/gem.json"
    },
```
```json
"remote": {
    "gems": {
        "file:///c:/byrcolin/somegem"
    },
```
Since this link is in the "gems" list and gem.json is not explicitly stated, it will infer "/gem.json" to be added onto the link.

These link types can be mixed and matched:
```json
"remote": {
    "repos": {
        "https://github.com/o3de/o3de-extras.git",
        "file:///c:/myrepo"
    },
    "gems": {
        "https://some.webserver.com/byrcolin/plane/gem.json",
        "https://some.webserver.com/Jess356/space",
        "https://github.com/byrcolin/marine.git",
        "https://some.webserver.com/tank.zip",
        "ftps://some.ftpserver.com/soldier.zip",
        "ftps://some.ftpserver.com/boat"
    },
    "templates": {
        "ftps://some.ftpserver.com/cooltemplate.zip",
        "https://github.com/apmg/character.git"
    },
    "projects": {
        "C:/path/to/project"
    }
}
```
</details>

## Indirection: linking to a repo.json
Instead of the engine.json directly linking to the extras repos, we can indirectly link to the extras repo. By having the engine link to a different repo.json on a web server somewhere, and then have that repo.json list the repos, which is what the engine currently does. That server is https://canonical.o3de.org. So the engine json would add this instead:
### added to engine.json
```json
"remote": {
    "repos": {
        "https://canonical.o3de.org/repo.json"
    }
}
```
And then that repo.json would look like this:
### canonical.o3de.org/repo.json
```json
{
    "$schema": "https://overlo3de.com/o3de-repo-2.0.0.json",
    "$schemaVersion": "2.0.0",
    "repo_name": "canonical.o3de.org",
    "origin": {
        "name": "O3DE",
        "uri": "https://canonical.o3de.org"
    },
    "summary": "The Open 3D Engine's canonical repos.",
    "remote": {
        "repos": {
            "https://github.com/o3de/o3de-extras.git"
        }
    }
}
```
We do this indirection because we may need to update this list often i.e. at a higher frequency than the O3DE engine object release. Since the engine.json is part of a release it would take a minor release to update it. But since the engine indirectly links, the repo.json it links to can be updated at any time, is not dependent on any release, and no one would have to update anything on their side for the change to propagate.

## So what does this have to do with discovery of community objects? 
The point here is that O3DE wants all users to know about all canonical O3DE objects. Users did not have to "discover" these objects because we added this link to the engine object for them. It is important to understand that this remote link to "https://canonical.o3de.org/repo.json" is the only reason why these objects are present in the project manager currently. So, if we just put all the links in this file then everyone would see all the objects, but the user may not want to see ALL of the objects. So we need a way to suggest other uris to the user without cramming them all into this file.

## How exactly does this work?
The initial goal here is to download and cache the object json file, so if it is linked directly to an object json, we just download it directly into the cache. This is the fastest, most efficient way. If it is not directly linked then the object json is only available indirectly, meaning we have to download/clone something else and extract the object json from it. This could be a zip file download, then we extract the object json file out of it. Or it could be a shallow clone of a git repo just to get the object json. Some git providers offer raw file access via a web api, which can be used, but is somewhat unreliable because the web apis are almost always throttled. (i.e. "https://raw.githubusercontent.com/o3de/o3de/refs/heads/development/engine.json") Which ever way we get the object json, once the object json is cached, it should be read and any tertiary files mentioned in that object json that program manager would show the user should also then be downloaded and cached, such as the icon file for a gem. If the object json revels this object has children then those object jsons should also be processed. Then the remotes declared in each of these objects should be downloaded, cached, read and so on until all objects have been processed.

## How can we implement link suggestions?
We can created two repo.json files, one which lists curated objects (objects that hve a higher bar for being maintained) and another which lists uncurated objects (this is the wild west, low bar, unvetted, potentialy dangerous, just has to actually work). This file will not be linked to by the engine but will be read by project manager to populate link suggestions to the user.

canonical.o3de.org/src/curated/repo.json -> https://o3de.org/curated/repo.json
```json
{
    "$schema": "https://canonical.o3de.org/o3de-repo-2.0.0.json",
    "$schemaVersion": "2.0.0",
    "repo_name": "Universal Curated Community Objects",
    "version": "1.0.123",
    "remotes": {
        "engines": [
            "https://raw.githubusercontent.com/o3de/o3de/refs/heads/development/engine.json",
            "https://github.com/byrcolin/expermientalengine.git"
        ],
        "projects": [
            "https://raw.githubusercontent.com/joe/game/refs/heads/development/project.json",
            "https://github.com/joe/example-project.git"
        ],
        "gems": [
            "https://gitlab.com/neva.ay13/pipe/-/raw/main/gem.json",
            "https://gitlab.com/apmg/Marine/-/raw/main/gem.json",
            "https://git.overlo3de.com/apmg/marine/-/raw/main/gem.json",
            "https://git.nvidia.com/linvee/wolf/gem.json",
            "https://raw.githubusercontent.com/vantec/robot/refs/heads/main/gem.json"
        ],
        "templates": [
            "https://raw.githubusercontent.com/byrcolin/template/refs/heads/development/template.json"
        ],
        "repos": [
            "https://overlo3de.com/apmg/repo.json",
            "https://raw.githubusercontent.com/aws/aws-o3de/refs/heads/development/repo.json",
            "https://overlo3de.com/john34er/airplanes/repo.json",
            "https://git.overlo3de.com/avid/controllers/repo.json",
            "https://git.overlo3de.com/rick/rendering/repo.json"
        ],
        "extensions": [
        ]
    }
}
```
canonical.o3de.org/src/uncurated/repo.json -> https://o3de.org/uncurated/repo.json.
```json
    "$schema": "https://canonical.o3de.org/o3de-repo-2.0.0.json",
    "$schemaVersion": "2.0.0",
    "repo_name": "Universal Uncurated Community Objects",
    "version": "1.0.123",
    "remotes": {
        "engines": [
            "https://github.com/jammy/o3deengine.git"
        ],
        "projects": [
            "https://github.com/darin/space.git",
            "https://git.overlo3de.com/jill/example.git"
        ],
        "gems": [
            "https://git.overlo3de.com/byrcolin/marine.git",
            "https://github.com/c75543767/hero.git",
            "https://github.com/jt_studios/sensors.git"
        ],
        "templates": [
            "https://github.com/byrcolin/shiptemplate.git"
        ],
        "repos": [
            "https://bitbucket.org/the_best/awesome_repo/repo.json",
            "https://overlo3de.com/Jess37465/o3de.git"
        ],
        "extensions": [
        ]
    }
}
```
Keeping these in separate files is better as we can put restrictions on who can update them:
* The curated remotes are updated via the PR process. Ideally, an approval from the sig maintainer the content would fall under should happen BEFORE the O3DE director reviews the object(s). As these objects are essentially recommended by O3DE, so the directors approval in consultation with Linux Foundation Legal is required. Curated objects have a high bar for inclusion and are subject to intense ongoing content review. Linux Foundation reserves the right to add or remove any and all uri(s), at any time, with or without notice, for any reason, including no reason. Considerations for inclusion on the curated remotes:
    1. The uri(s) must work. The uri(s) link to a real object(s) for this kind of O3DE object(s). So if the PR says they would like their uri(s) to be added to the curated "gems" remote list, we check that this is indeed a real gem, meaning it has a valid gem.json in the root, further the gem is indeed what its supposed to be, as the name would imply.
    2. We make a judgement that the naming of this object(s) is appropriate.
    3. The SIG(s) this object(s) falls under should also sign off on the content, as they are best able to tell the director that the code seems valid, doesn't look like any funny business is going on.
    4. We check the license(s) is valid.
    5. We make sure the icon(s), (if any) is appropriate.
    6. We make sure rest of the uris (if any) in the object json work (like documentation uri links). 
    7. We review the contents of the object(s) and evaluate that it does not break any known laws.
    8. We must deem this object(s) are useful to O3DE users.
    9. We will require that the object(s) be maintained by the owner and must work on the latest version of O3DE, given a window of time after a release.
    10. Our determination is based on the point in time at which the PR is reviewed and can be accepted in it current form. Since these are all live objects, no can continually check all content. However, since curated objects will be few, on occasion, perhaps once a year, or for a release, we may choose to review the current state of these objects.
    11. After accepting a curated object(s) we will have a process in place to accept community driven complaints about the object(s). If anyone finds any known malware, virus content or illegal content, we will remove any such uri(s) asap. If we receive a claim(s) of ownership such as DMCA takedowns, court orders, sanctions, etc. we default to immediate takedown, while we review the claim. After review, if we do not find the claim credible the uri(s) may be restored. If this is a repeat of a previous claim that was not deemed not credible in the past, the uri(s), at Linux Foundations discretion, may stay live while under review of the new claim.
* The uncurated remotes are updated via normal PR process but only needs a maintainer to approve. This is a very low bar. We make nearly NO determination on good or bad, useful or useless. For a maintainer to accept an addition:
    1. The uri(s) must work. The uri(s) link to a real object(s) for this kind of O3DE object(s). So if the PR says they would like their uri(s) to be added to the uncurated "gems" remote list, we check that this is indeed a real gem, meaning it has a valid gem.json in the root, we DO NOT determine that the gem is indeed what its supposed to be as the name would imply.
    2. We DO NOT make a judgement on that the naming of this object(s) are appropriate for what they do, however we DO look for obvious frauds, like the objects name implying it belongs to someone other than the owner, such as John Doe user naming his object 'com.microsoft.blah' which might lead someone to the conclusion microsoft made/owns this object.
    3. We DO check the license(s) is valid.
    3. We DO make sure the icon(s), (if any) is not objectionable, NOT that is appropriate.
    4. We DO NOT have to make sure rest of the uris (if any) in the object json work (like documentation uri links). 
    5. We DO a minimal/cursory review the contents of the object(s) and evaluate that it does not blatantly break any known laws.
    6. We DO NOT make any determination that this object(s) is useful to O3DE users.
    7. We DO NOT require that the object(s) must work on the latest version of O3DE.
    8. Our determination is based on the point in time at which the PR is reviewed and can be accepted in it current form. Since these are all live objects, no can continually check all content. However, since uncurated objects will be many, we WILL NOT review the current state of these objects.
    9. After accepting an uncurated object we will rely on community driven complaints about the object(s). If the claim is about known malware, virus content, illegal content, a claims of ownership such as DMCA takedowns, court orders, sanctions, etc. WE ALWAYS REMOVE THE URI(s) IMMEDIATELY, no further determination is required. If the owner of the object is not ok with the takedown, they should submit the objects for curated approval.
    10. Any object may be resubmitted. 

These two main universal community files are for every country. But there is the consideration that we do not want to violate any local laws. Since laws vary by country, we may want to consider localized community files. The country file would have a repo link to the universal repo so that simply including your country you get your countries repos plus the universal ones. i.e. canonical.o3de.org/src/curated/US/repo.json -> https://o3de.org/curated/US/repo.json
```json
{
    "$schema": "https://canonical.o3de.org/o3de-repo-2.0.0.json",
    "$schemaVersion": "2.0.0",
    "repo_name": "US Curated Community Objects",
    "version": "1.0.123",
    "remotes": {
        "engines": [
        ],
        "projects": [
            "https://github.com/US-DOD/USMC.git",
        ],
        "gems": [
            "https://git.overlo3de.com/byrcolin/marine.git",
            "https://github.com/byrcolin/flightcontroller.git"
        ],
        "templates": [
        ],
        "repos": [
            "https://o3de.org.com/curated"    <---- by default includes universal curated
        ],
        "extensions": [
        ]
    }
}
```
canonical.o3de.org/src/uncurated/US/repo.json -> https://o3de.org/uncurated/US/repo.json.
```json
    "$schema": "https://canonical.o3de.org/o3de-repo-2.0.0.json",
    "$schemaVersion": "2.0.0",
    "repo_name": "US Uncurated Community Objects",
    "version": "1.0.123",
    "remotes": {
        "engines": [
        ],
        "projects": [
        ],
        "gems": [
            "https://git.overlo3de.com/byrcolin/marine.git"
        ],
        "templates": [
        ],
        "repos": [
            "https://o3de.org.com/curated"  <---- by default includes universal uncurated
        ],
        "extensions": [
        ]
    }
}
```
canonical.o3de.org/src/curated/CHN/repo.json -> https://o3de.org/curated/CHN/repo.json
```json
{
    "$schema": "https://canonical.o3de.org/o3de-repo-2.0.0.json",
    "$schemaVersion": "2.0.0",
    "repo_name": "China Curated Community Objects",
    "version": "1.0.123",
    "remotes": {
        "engines": [
        ],
        "projects": [
        ],
        "gems": [
            "https://git.overlo3de.com/Shejo/PRC-soldier.git"
        ],
        "templates": [
        ],
        "repos": [
            "https://o3de.org.com/curated"    <---- by default includes universal curated
        ],
        "extensions": [
        ]
    }
}
```
canonical.o3de.org/src/uncurated/CHN/repo.json -> https://o3de.org/uncurated/CHN/repo.json.
```json
    "$schema": "https://canonical.o3de.org/o3de-repo-2.0.0.json",
    "$schemaVersion": "2.0.0",
    "repo_name": "China Uncurated Community Objects",
    "version": "1.0.123",
    "remotes": {
        "engines": [
        ],
        "projects": [
        ],
        "gems": [
        ],
        "templates": [
        ],
        "repos": [
            "https://o3de.org.com/uncurated"  <---- by default includes universal uncurated
        ],
        "extensions": [
        ]
    }
}
```
We would then detect and set the country default on o3de_manifest.json. As with anything in the o3de_manifest.json the user can change the country to their choice. This would make it viable to accept a repo that is legal in one country that would be illegal in a another country. It could also be a grouping of countries that have the same/similar laws US-CAN-UK or CHN-CBA-NK to minimize duplication of data.

These are repo.json files, so the user COULD just add this repo link and they would see ALL the objects, however that is not the purpose of these files. These files will be read by the project manager and its links presented to the user. The Mock up below is how the user will see the suggestions.

## Mock up
This is a mock up of what community repos UI would look like: (note the "Community Repos" here would be "Curated Repos" and an identical box below this one for "Uncurated Repos" would be present.
![image](https://github.com/user-attachments/assets/33db7c93-f1be-4992-af70-f224d24829c2)

The user presses the entry and the URI is copied into the dialogs Repository Path for the user.
![image](https://github.com/user-attachments/assets/c62040f0-9ee0-40ec-aa13-96c6b151c10a)

They then can press "Add"

The same can be done for project discovery:  (note the "Community Projects" would be "Curated Projects" and an identical box below it for "Uncurated Projects" would be present.
![image](https://github.com/user-attachments/assets/6d35a014-742c-4932-8ea0-6f5827dd30be)
![projects-discovery](https://github.com/user-attachments/assets/907a65ad-4469-4d74-b447-21a5d4cd5160)


They select the one they want on the list:
![projects-discovery2](https://github.com/user-attachments/assets/ed2e2adf-bbfa-4953-8e89-ad3b7c9464c1)

Eventually we can add support for engine objects and template objects and extension objects, etc. in new screens when we get to them.