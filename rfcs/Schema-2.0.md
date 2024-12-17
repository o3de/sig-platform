# The Case for Schema 2.0.0 for the O3DE object system

## Background information: 
### What is the O3DE Object system and what is a schema?
O3DE is intended to be a distributed system of O3DE "Objects", for extensibility. An O3DE object is a folder with a file that labels what that data is and its properties. This file is called the object json. The name of this file depends on the type of O3DE object.
* Engine = engine.json
* Project = project.json
* Gem = gem.json
* Template = template.json
* Repo = repo.json
* Extension = extension.json

All of these object json files have one thing in common; a name. An engine has a engine_name field, a project has a project_name field, gem has a gem_name field and so on. The name is the most important field as it how this object identifies itself to O3DE. The name is the primary field used by the registration and build systems. One of the biggest things this enables for the O3DE is the ability to locate objects anywhere on the local file system and be able to build. Registration is how we tell O3DE where an object is on the file system. The build system then uses this location information in the configuration process.

c:/gems/test/gem.json
```json
{
    "gem_name": "test"
}
```
We could go further and claim this is "schema 0.0.0" by adding a "$schemaVersion" field. If it doesn't have a "$schemaVersion" field 0.0.0 is implied.
```json
{
    "$schemaVersion": "0.0.0",
    "gem_name": "test"
}
```
If we actually had a schema json for 0.0.0, we would put it the O3DE web server in the root and call it o3de-gem-0.0.0.json. It would look like this:
```json
{
    "$schema": "http://json-schema.org/draft-07/schema#",
    "$id": "https://canonical.o3de.org/o3de-gem-0.0.0.json",
    "title": "O3DE Gem Schema 0.0.0",
    "definitions": {
        "$schemaVersion": {
            "type": "string",
            "description": "The version of the O3DE schema this O3DE object is using. i.e. '0.0.0'",
            "default": "0.0.0",
            "pattern": "0.0.0"
        },
        "gem_name": {
            "type": "string",
        }
    }
}
```
Then the gem json would reference this schema by adding "$schema" field to the gem.json:
```json
{
    "$schema": "https://canonical.o3de.org/o3de-gem-0.0.0.json",
    "$schemaVersion": "0.0.0",
    "gem_name": "test"
}
```

Schemas can then be used by the IDE to inform the creator of a gem.json which fields are required or are optional, what the expected value looks like and give descriptions of what the fields are for as its being created.

This document primarily discusses a formal schema like this for O3DE objects and suggests ways in which these changes could be implemented.

## Why do we need an object json schema? 
Adhering to any schema for a JSON promotes consistency by ensuring that all data follows a predefined structure. This reduces the likelihood of errors and makes it easier to validate and parse the data. Additionally, it enhances interoperability between different systems and components, as they can rely on the schema to understand the data format. Schemas also improve maintainability by providing clear documentation of the expected data structure, making it easier for developers to understand and work with the data. Overall, using a schema for JSON contributes to more robust, reliable, and maintainable code. It also serves as a source of hints and descriptions for the IDE creating a json so that the user will not have to go to the documentation nearly as much as the IDE will be helping them write valid json.

## Don't we already have a schema?
No. O3DE has not yet had a true schema, and even though we previously referred to documentation as a schema, this effort aims to create an actual concrete schema json that can be validated. A proper schema will address the shortcomings of the more simplistic conventions used so far. Any new schema will have to carry along the needed parts of what we will call "schema 0.0.0" and "schema 1.0.0" and remove the parts that caused confusion/conflation and codify them into a true json schema that IDE's can use to help the user create valid object json.

## What is Schema 0.0.0?
Basically we say its schema 0.0.0 if it doesn't explicitly say $schemaVersion.
It is very basic, it has a name, and that is about all that is required.

## What is Schema 1.0.0?
Was an attempt at a more informant and consistent way of representing the objects, however it has some serious flaws. If the object json has a "$schemaVerson": "1.0.0" in it, we say it is schema 1.0.0. While schema 1.0.0 went a fair way to standardize what is expected in an object json, it was not actually a schema and was not applied to all objects. The $schemaVerson field is used in many scripts to differentiate the meanings and if certain new fields like xxx_data could be present. Since there was no actual schema json created to validate json written against it, it was more of an informal schema. Schema 1.0.0 introduced many new concepts like "compatible_engines" and "version_data" which we will keep in 2.0.0. Schema 1.0.0 also introduced the notion of "child _data" entries. This is the duplication of all child object json data into the parent objects json data. Now duplicated, there are two sources of truth, and both must be updated in sync, which in practice rarely ever happened. This was done to speed up the downloading/parsing of many files and thereby make the extraction the needed information for project manager easier/faster. However this approach does not scale, goes against all object oriented principles of self description, against data duplication, proved to be unmaintainable as any change to child/unrelated object needs to now be duplicated and copied into the parent. This has already bit us, for very little to no "speed" benefit as the caching system makes it fast enough. There is a good argument to remove this functionality even from the schema 1.0.0 backward compatibility as it is so unmaintainable and complicates the scripts necessarily.

## Why not Schema 0, 1 and 2? or schema 0.0, 1.0 or 2.0? Why use 0.0.0, 1.0.0 and 2.0.0?
Part of the standardization we are want to do here is to standardize version strings across O3DE. All version strings going forward with schema 2.0.0 will use major.minor.patch format, therefore to keep it all consistent, schema 0.0.0, schema 1.0.0 and schema 2.0.0. We need to go to a new major version because of the inconsistencies in the application of schema 0.0.0 and 1.0.0, to correct the misuse of common terms and to maintain backward compatibility.

## How will schema 2.0.0 be different/better?
Schema 2.0.0 will take the needed parts of schema 0.0.0 and schema 1.0.0, will not carry forward the schema 1.0.0 child _data copying into the parent, standardize and group similar root fields and actually implement a schema that will standardize the meanings of fields across all object types. Then we will actually upgrade/fix all O3DE object json to 2.0.0.

## Schema 2.0.0 will:
1. Have a formal schema for every kind of O3DE object posted to canonical.o3de.org in src folder so that it is posted at the root of the web server.
    * https://canonical.o3de.org/o3de-patterns-2.0.0.json will contain common definitions used across all O3DE schemas for 2.0.0
    * https://canonical.o3de.org/o3de-engine-2.0.0.json will contain O3DE schema for an engine 2.0.0
    * https://canonical.o3de.org/o3de-project-2.0.0.json will contain O3DE schema for a project 2.0.0
    * https://canonical.o3de.org/o3de-gem-2.0.0.json will contain O3DE schema for a gem 2.0.0
    * https://canonical.o3de.org/o3de-template-2.0.0.json will contain O3DE schema for a template 2.0.0
    * https://canonical.o3de.org/o3de-repo-2.0.0.json will contain O3DE schema for a repo 2.0.0
    * https://canonical.o3de.org/o3de-extension-2.0.0.json will contain O3DE schema for an extension 2.0.0

    Once available on the internet, all user object json will carry a schema reference that IDE's use to validate/hint the user to create valid json. All official O3DE jsons will explicitly reference the schema and state "$schemaVersion": "2.0.0". We will make sure validation, hinting, etc. works on VS Code.

2. Will no longer support schema 1.0.0 style child _data entries being copied into the parent object json.

3. Will group similar data. Currently o3de has gotten into the bad habit of polluting the root with all fields.

4. Will formalize the naming convention of objects to be the same as the Java package naming convention:
    * Lowercase Letters: Package names should be written in all lowercase letters. 
    * Reverse Domain Name: Start with your organization's domain name in reverse order to ensure uniqueness. 
    * Sub-packages: Use dots to separate different levels of the package hierarchy. 
    * Descriptive Names: Use meaningful and descriptive names for packages to indicate their functionality or purpose. 
    * Avoid Reserved Words: Do not use c++, python, java reserved keywords in package names. 
    By following these conventions, you ensure that your package names are unique, descriptive, and maintainable.

    Examples:
    * The engine is currently called "o3de" => "org.o3de.o3de" 
    * The extras repo is currently called "o3de-extras" => "org.o3de.o3de-extras" 
    * The gem called "OpenXRVK" in the extras repo => "org.o3de.openxrvk"
    * My gem for the marine is currently called "apmg.marine" => "com.accesspointmg.marine" or "com.overlo3de.apmg.marine"

    This naming convention should make it easier for people to see just by the name which object we are talking about, and if someone wants to make a change to an o3de gem, they can copy it and change the name i.e. "com.apmg.openxrvk", this will disambiguate similar but different objects being used. 

    Arguments against encoding more information into the name:
    * adding the object type into the name: "OpenXRVK" => "org.o3de.openxrvk.gem" for the OpenXRVk gem. But this is not needed, because they are different types, and they can have the same name and the system will never confuse them. So incorporating the type is just data duplication. 
    * adding the hierarchy to the name: "OpenXRVK" => "org.o3de.o3de-extras.openxrvk" because this gem is in the o3de-extras repo. This too is not needed and counter productive as the name of where the object lives is now encoded into the name and if the object was to move to to a differnt location then the name would no longer really make sense but you wouldn't want to change it because it would break peoples code. 

5. Will standardize the meaning of "version" across all O3DE objects to consist of major.minor.patch. This field is going to become the second most important field, behind name, as we are going to start to enforce incrementing the versions of all objects on changes. O3DE needs to start to be fanatical about our versioning if this project is going to stand the test of time. We need to also always use version qualification on the object dependencies. For instance, when a project says it needs "OpenXRVK" gem it would add "org.o3de.openxrvk>=1.2.0<=1.3.5" to its dependencies. The project manager can then use this information to select the best objects for the user. Without this we are really crossing our fingers that it all works. We have been able to get away with it so far because we ship all the gems in the core repo every release, but this will soon no longer happen as the direction of the repo is to move these objects out of the core repo.

    Rules for changing version that will be enforced on all check-ins:
    * 0 major means this is pre-release software i.e. "0.1.92"
    * If it is change that does not break API compatibility the lowest number will be increased "1.0.0" -> "1.0.1" 
    * If the change alters the API, adds/removes functionality of the object, then the minor number must be increased and patch reset to zero i.e "1.2.35" -> "1.3.0" 
    * If this object is no longer pre-release, or pretty much completely rewritten, such that it will never be compatible in any way with the previous version, it gets a major increase and reset of minor and patch to 0. This means that there should be no expectation of compatibility at all or feature comparability i.e. "1.34.23" -> "2.0.0"

    ```json
    "version": {
        "type": "string",
        "description": "The version: major.minor.patch i.e. '2.1.0'",
        "pattern": "^[0-9]+\\.[0-9]+\\.[0-9]+$"
    }
    ```
    
6. Will group all origin fields and standardize the meaning of "origin" to be the originator (creator/author/owner) of this object.
    * map: origin => origin.name: the name of the originator i.e. 'The Linux Foundation' or 'John Doe' 
    * map: origin_uri => origin.uri: the website for the originator i.e. 'https://www.mydomain.com/'
    ```json
    "origin": {
        "type": "object",
        "description": "All thing related to objects originator (creator/author) goes here.",
        "properties": {
            "name": {
                "type": "string",
                "description": "The name of the originator (creator/author) of this object. i.e. '<Company Name>' or 'John Doe'"
            },
            "email": {
                "description": "The email of the originator (creator/author) of this object. i.e. 'support@mycomany.com' or 'bo@gus.com'",
                "ref": "#/definitions/email"
            },
            "uri": {
                "description": "The uri (website/ftp) for the originator (creator/author) of this object. i.e. 'https://www.mydomain.com'",
                "$ref": "#/definitions/httpsftpsUri"
            }
        }
    }
    ```

7. Will group all download fields
    * map: download_source_uri => download.uris.source_zip_uri to be the uri of the source archive to download. i.e. 'https://github.com/o3de/o3de/archive/refs/tags/2409.1.zip' we will change this to an array of uri objects that can accommodate the lfs entries 
    * add: download.uris.lfs_zip_uri to be the uri of the lfs archive to download. i.e. 'https://github.com/o3de/o3de/releases/download/2409.1/o3de-2409.1-lfs.zip' 
    * map: sha256 => download.source_zip_sha256 to be the sha256 of the source zip file 
    * add: download.lfs_zip_sha256 to be the sha256 of the lfs zip file
    ```json
    "download": {
        "type": "object",
        "description": "If this object is available as a zip download. It can have a source and lfs portion.",
        "properties": {
            "uris": {
                "type": "object",
                "description": "uri that you can download a zip snapshot of this object.",
                "properties": {
                    "source_zip_uri": {
                        "description": "The uri of the source archive to download. i.e. 'https://github.com/o3de/o3de-extras/releases/download/2310.1/o3de-2310.1.zip'.",
                        "$ref": "#/definitions/zipUri"
                    },
                    "lfs_zip_uri": {
                        "description": "The uri of the lfs portion of the source archive to download. i.e. 'https://github.com/o3de/o3de/releases/download/2310.1/o3de-2310.1-lfs.zip'",
                        "$ref": "#/definitions/zipUri"
                    }
                }
            },
            "source_zip_sha256": {
                "description": "The sha256 hash of the source_zip_uri. i.e. '45c906788ed9a83b8ff74fd1b893a54f79e4d3e0674139839afb807260efa0e6'",
                "$ref": "#/definitions/sha256"
            },
            "lfs_zip_sha256": {
                "description": "The sha256 hash of the lfs_zip_uri. i.e. '45c906788ed9a83b8ff74fd1b893a54f79e4d3e0674139839afb807260efa0e6'",
                "$ref": "#/definitions/sha256"
            }
        }
    }
    ```

8. Will group all the source control fields
    * map: source_control_uri => source_control.uri to be the uri of the source control repo. i.e. 'https://github.com/XYZ/MyRepo.git' 
    * map: source_control_path => source_control.relative_path to optional be if this object is not in the root of the source repo, then this holds relative path to this objects root. i.e. 'Gems/MyGem' 
    * map: source_control_branch => source_control.branch to optionally be if you want people to pull this object from a branch other than the default branch. i.e. 'development' 
    * map: source_control_ref => source_control.tag to optionally be if you want this object to be pulled at a specific tag/ref instead of the head revision. i.e. 'v_1.2'
    ```json
    "source_control": {
        "type": "object",
        "description": "If this object is available via source control, like git, that goes here.",
        "properties": {
            "uri": {
                "description": "The uri of the source repo. i.e. 'https://github.com/XYZ/MyRepo.git'",
                "$ref": "#/definitions/gitUri"
            },
            "relative_path": {
                "description": "If this object is not in the root of the source repo, then this holds relative path to this objects root. i.e. 'Gems/MyGem'",
                "$ref": "#/definitions/relativePath"
            },
            "branch": {
                "type": "string",
                "description": "If you want people to pull this object from a branch other than the default branch. i.e. 'development'"
            },
            "tag": {
                "type": "string",
                "description": "If you want this object to be pulled at a specific tag instead of the head revision. i.e. 'v_1.2'"
            }
        }
    }
    ```

9. Will group all the icon fields
    * map: icon_path => icon.relative_path to be if your repo contains an icon to used by the Project Manager when the repo is local, then this is the relative path to the local icon file from this object's root. i.e. 'icons/preview.png'. If not supplied Project Manager will use the default icon when local. 
    * map icon_uri => icon.uri to be the uri to the icon file that is used by the Project Manager when the object is remote. i.e. 'https://www.mydomain.com/object/preview.png'. If not supplied Project Manager will use the default icon when remote.
    ```json
    "icon": {
        "type": "object",
        "description": "All things related to icons.",
        "properties": {
            "relative_path": {
                "description": "If your repo contains a icon to used by the Project Manager when the repo is local, then this is the relative path to the local icon file from this object's root. i.e. 'icons/preview.png'. If not supplied Project Manager will use the default icon when local.",
                "$ref": "#/definitions/relativePath"
            },
            "uri": {
                "description": "The uri to the icon file that is used by the Project Manager when the object is remote. i.e. 'https://www.mydomain.com/object/preview.png'. If not supplied Project Manager will use the default icon when remote.",
                "$ref": "#/definitions/httpsftpsUri"
            }
        }
    }
    ```

10. Will group the documentation fields
    * map: documentation_path => documentation.relative_path to be if your repo contains documentation, then this is the relative path to the documentation file from this object's root. i.e. 'docs/README.md' 
    * map: documentation_uri => documentation.uri to be if your documentation is online, then this is the uri of the documentation. i.e. 'https://www.mydomain.com/MyObject/documentation/index.html'
    ```json
    "documentation": {
        "type": "object",
        "description": "All things related to documentation.",
        "properties": {
            "relative_path": {
                "description": "If your repo contains documentation, then this is the relative path to the documentation file from this object's root. i.e. 'docs/README.md'",
                "$ref": "#/definitions/relativePath"
            },
            "uri": {
                "description": "If your documentation is online, then this is the uri of the documentation. i.e. 'https://www.mydomain.com/MyObject/documentation/index.html'",
                "$ref": "#/definitions/httpsftpsUri"
            }
        }
    }
    ```

11. Will standardize the meaning of "canonical_tags" to be a tag defined by O3DE, that Project Manager uses to sort, search and filter objects. enumerated as 'Engine', 'Project', 'Gem', 'Template', 'Repo', 'Restricted' (Note about Restricted, I want to change this to a more general "Extension" in another RFC about changes to the PAL)
```json
"canonical_tag": {
    "description": "Enumerated by O3DE as 'Engine', 'Project', 'Gem', 'Template', 'Repo', 'Extension'.",
    "type": "string",
    "enum": [
        "Engine",
        "Project",
        "Gem",
        "Template",
        "Repo",
        "Extension"
    ]
},
"canonical_tags": {
    "description": "Canonical tags are used to sort, search and filter objects.",
    "type": "array",
    "items": {
        "$ref": "#/definitions/canonical_tag"
    }
}
```

12. Will standardize the meaning of "user_tags" to be all non-canonical tags defined by the originator (creator), exclusive of the canonical tags, that Project Manager uses to search, sort and filter objects. These can be anything you want to tag your object with i.e. 'AI' or 'Space' or 'Human' etc.
```json
"user_tag": {
    "description": "Enumerated by the originator (creator/author), exclusive of the canoncial_tags. These can be anything you want to tag your object with i.e. 'AI' or 'Space' or 'Human' etc.",
    "type": "string",
    "not": {
        "enum": [
            "Engine",
            "Project",
            "Gem",
            "Template",
            "Repo",
            "Restricted"
        ]
    }
},
"user_tags": {
    "description": "User tags are used to sort, search and filter objects.",
    "type": "array",
    "items": {
        "$ref": "#/definitions/user_tag"
    }
}
```

13. Will add new array "platforms" which will be an optional list of platforms explicitly supported by this object. If you want to specify the platforms supported by this object. enumerated as 'Windows', 'Linux', 'MacOS', 'iOS', 'Android' or a custom string for other platforms
```json
"platform": {
    "description": "What platforms this object is compatible with.",
    "type": "string",
    "oneOf": [
        {
            "enum": [
                "Windows",
                "Linux",
                "MacOS", 
                "iOS",
                "Android"
            ]
        },
        {
            "pattern": "^[a-zA-Z][a-zA-Z0-9_-]*$"
        }
    ]
},
"platforms": {
    "type": "array",
    "items": {
        "$ref": "#/definitions/platform"
    }
}
```

15. Will standardize "requirements" to be if you have extra instructions for the user, beyond the normal O3DE setup, on how to setup/use this object. i.e. 'You must install XYZ SDK 1.2 to compile this object.', or 'Read Setup.md before compiling.'

16. Will group all license fields into an array of license objects to deal with complex licensing. This structure will match the field names found in the official SPDX json (https://raw.githubusercontent.com/spdx/license-list-data/refs/heads/main/json/licenses.json). Each License entry will have:
    * map license => licenses.licenseId which will be required and defined as preferably, the SPDX license identifier. i.e. 'Apache-2.0'. Or if not using a common SPDX license then name your own homemade license identifier 'XYZ license'. 
    * map: license_path => licenses.relative_path which is defined as if you have included a license file in your repo, this is the relative path to the local license file. i.e. 'licenses/LICENSE_APACHE2.txt'. 
    * map license_uri => licenses.reference which is defined as the uri if you didn't include a copy of the license in the repo, then you may link to a website of the license i.e. 'https://opensource.org/licenses/Apache-2.0'. 
    We will make license required and you must either have a "license_path" or "license_uri" or both.
    * add: licenses.scope which clearly defines the limits and what this license pertains to: 'Applies to all code in the X folder.' 
    * add: licenses.is_deprecated to be defined to match the SPDX field. If this license is deprecated, then set this to true. i.e. 'true'" 
    * add: licenses.details_url to be defined to match the SPDX field. If you didn't include a copy of the license in the repo, then you may link to a json file of the license i.e. 'https://spdx.org/licenses/Apache-2.0.json' 
    * add: licenses.is_osi_approved to be defines to match the SPDX field. If this license is OSI approved, then set this to true. i.e. 'true'" 
    * add: licenses.see_also as an array of uris to match the SPDX array. This links to other interesting information about the license. 
    This will provide a better way to deal with complex licensing: Currently in schema 1.0.0, license is somewhat inadequate to express complex licensing as it can only show one license line, so users have resorted to jamming all the different license information on one line of text, which can make understanding the licenses difficult. Schema 2.0.0 will allow for multiple license entries, this should be adequate for simple and complex licensing. This does not preclude single complex SPDX either as each licenseId could be a SPDX expression i.e. 'Apache-2.0 OR MIT'
    The schema should be easily updatable by scripting a transformation of the official SPDX json list into the schema. This will allows easy updating of the SPDX data into a format digestible for an IDE that will validate and give the user hints as to the fields and the field descriptions.
    ```json
    "license": {
        "type": "object",
        "properties": {
            "relative_path": {
                "definition": "If you include a copy of the license in the repo, then this is the relative path to the license file from this object's root. i.e. 'licenses/mylicense.txt'",
                "$ref": "#definitions/relativePath"
            },
            "scope": {
                "type": "string",
                "description": "If this license is limited to a specific portion of this object state that here. i.e. 'Applies to all code in the X folder.'"
            },                
            "name": {
                "type": "string",
                "description": "The name of the license. i.e. 'Apache License 2.0'"
            },
            "reference": {
                "description": "If you didn't include a copy of the license in the repo, then you may link to a website of the license i.e. 'https://opensource.org/licenses/Apache-2.0'",
                "$ref": "#/definitions/httpsftpsUri"
            },
            "is_deprecated": {
                "type": "boolean",
                "description": "If this license is deprecated, then set this to true. i.e. 'true'"
            },
            "details_url": {
                "description": "If you didn't include a copy of the license in the repo, then you may link to a json file of the license i.e. 'https://spdx.org/licenses/Apache-2.0.json'",
                "$ref": "#/definitions/httpsftpsUri"
            },
            "is_osi_approved": {
                "type": "boolean",
                "description": "If this license is OSI approved, then set this to true. i.e. 'true'"
            },
            "see_also": {
                "type": "array",
                "items": {
                    "$ref": "#/definitions/httpsftpsUri"
                }
            },
            "licenseId": {
                "type": "string",
                "description": "The identifier of the license, usually a SPDX identifier, but can be your own custom identifier. i.e. 'Apache-2.0'",
                "oneOf": [
                    {
                        "enum": [
                            "0BSD",
                            "3D-Slicer-1.0",
                            "AAL",
                            ... ALL THE SPDX Identifiers here
                        ]
                    },
                    {
                        "pattern": "^[a-zA-Z][a-zA-Z0-9_.-]*$"
                    }
                ]
            },
            "oneOf": [
                {
                    "required": [
                        "licenseId",
                        "reference"
                    ]
                },
                {
                    "required": [
                        "licenseId",
                        "relative_path"
                    ]
                }
            ],
            "allOf": [
                {
                    "if": {
                        "properties": {
                            "licenseId": {
                                "const": "0BSD"
                            }
                        }
                    },
                    "then": {
                        "properties": {
                            "name": {
                                "const": "BSD Zero Clause License"
                            },
                            "reference": {
                                "const": "https://spdx.org/licenses/0BSD.html"
                            },
                            "is_deprecated": {
                                "const": false
                            },
                            "details_url": {
                                "const": "https://spdx.org/licenses/0BSD.json"
                            },
                            "is_osi_approved": {
                                "const": true
                            },
                            "see_also": {
                                "type": "array",
                                "items": {
                                    "enum": [
                                        "http://landley.net/toybox/license.html",
                                        "https://opensource.org/licenses/0BSD"
                                    ]
                                }
                            }
                        }
                    }
                },
                ... one of these if block for each SPDX
        }
    }
    ```

17. Will group all dependencies
    * map: gem_names => dependecies.gems It will List all O3DE objects that this object depends on. A dependency on another O3DE object means if this object is included in the build and these other dependent objects are not present this object will not build/function correctly. Project Manager will warn if this objects is selected and these dependencies are not available. The group will be of a list of each type of object enumerated as "engines", "projects", "gems", "templates", "repos", "extensions". If you want Project Manager to warn the user that this object will not function without one of these particular O3DE objects being downloaded and registered then specify the O3DE objects on which this object depends. List the name's with qualifying versions i.e. 'com.overlo3de.apmg.blahgem>=0.0.0','com.github.marco.spacegem>=1.2.0'
    ```json
    "dependecies": {
        "type": "object",
            "properties": {
                "engines": {
                    "description": "Engine objects i.e. 'org.o3de.myengine or org.o3de.myengine>=1.2.0 or org.o3de.myengine>=1.2.0<=1.3.0'",
                    "$ref": "#/definitions/objectNameAndVersions"
                },
                "projects": {
                    "description": "Project objects. i.e. 'org.o3de.myproject or org.o3de.myproject>=1.2.0 or org.o3de.myproject>=1.2.0<=1.3.0'",
                    "$ref": "#/definitions/objectNameAndVersions"
                },
                "gems": {
                    "description": "Gem objects. i.e. 'org.o3de.mygem or org.o3de.mygem>=1.2.0 or org.o3de.mygem>=1.2.0<=1.3.0'",
                    "$ref": "#/definitions/objectNameAndVersions"
                },
                "templates": {
                    "description": "Template objects. i.e. 'org.o3de.mytemplate or org.o3de.mytemplate>=1.2.0 or org.o3de.mytemplate>=1.2.0<=1.3.0'",
                    "$ref": "#/definitions/objectNameAndVersions"
                },
                "repos": {
                    "description": "Repo objects. i.e. 'org.o3de.myrepo or org.o3de.myrepo>=1.2.0 or org.o3de.myrepo>=1.2.0<=1.3.0'",
                    "$ref": "#/definitions/objectNameAndVersions"
                },
                "extensions": {
                    "description": "Extension objects. i.e. 'org.o3de.myextension or org.o3de.myextension>=1.2.0 or org.o3de.myextension>=1.2.0<=1.3.0'",
                    "$ref": "#/definitions/objectNameAndVersions"
                }
            }
        }
    }
    ``` 

18. Will add a new grouping compatibilities
    * map engine => compatibilies.engines It will List all O3DE objects that this object known compatible with. Just because a compatibility is declared will not stop this object from being used, it is there to alert the user that this object was tested as working with specific other objects. The group will be of a list of each type of object enumerated as "engines", "projects", "gems", "templates", "repos", "extensions". If you want Project Manager to warn that this object has only been tested as working with a certain O3DE objects then specify the O3DE objects which this object was specifically tested as working with. List the name's with qualifying versions i.e. 'com.overlo3de.apmg.blahgem>=0.0.0','com.github.marco.spacegem>=1.2.0'
    ```json
    The same as Dependencies
    ```

19. Will standardize a new grouping "releases", much like "version_data" in schema 1.0.0, to deal with o3de object releases. A release is defined as a version string and a download and/or source control.
    It will list all available releases of this object. A release can be an archive zip file snapshot of the object or a source control structure. This is the destination of the root version, download and source_control fields. When a new release is made essentially these root fields are copied into a new release.
    ```json
    "release": {
        "type": "object",
        "properties": {
            "version": {
                "$ref": "#/definitions/version"
            },
            "download": {
                "ref": "#/definitions/download"
            },
            "source_control": {
                "ref": "#/definitions/source_control"
            }
        },
        "required": [
            "version"
        ],
        "anyOf": [
            {
                "required": [
                    "download"
                ]
            },
            {
                "required": [
                    "source_control"
                ]
            }
        ]
    },
    "releases": {
        "type": "array",
        "items": {
            "$ref": "#/definitions/release"
        }
    }
    ```

20. All objects MUST carry their own license information and may not rely on the parent object license. This is done so that if these objects are ever separated in the future they do not rely on being in the engine repo to have license information attached to them.

21. Will also standardize on the "uri" instead of the older and less accurate "url" for links.
```
some_url => some_uri
```

22. Will separate "external_subdirectories" but maintain the concept from "gems", currently they are used confusingly/interchangeably in different parts of the engine. They are not the same thing "external_subdirectories" are places we want CMake to recurse into during the build process. And since we want to recurse into (most) gems they were just put into "external_subdirectories". While this will technically work, it is confusing. We will keep the notion of "external_subdirectories" but they will be separate from gems: Any folder we want to recurse into that is not an O3DE object like a gem should go in this list. We can then eliminate the code in the engine which scans all "external sub directories" and then removes any of them that don’t have a gem.json in order for them to be identified as gems, which is very annoying and a consequence of this conflation.

23. A new grouping of children
    * child objects in schema 1.0.0 were in the root, schema 2.0.0 will group children in a new group called children. This should disambiguate dependencies from children and from remotes. This will be a list of relative paths as children are always relative to the parent. The group will be of a list of each type of object enumerated as "engines", "projects", "gems", "templates", "repos", "extensions".
    ```json
    "children": {
        "type": "object",
        "properties": {
            "engines": {
                "description": "Relative path to a child engine. i.e. 'Engines/MyEngine'",
                "$ref": "#/definitions/relativePaths"
            },
            "projects": {
                "description": "Relative path to a child project. i.e. 'Projects/MyProject'",
                "$ref": "#/definitions/relativePaths"
            },
            "gems": {
                "description": "Relative path to a child gem. i.e. 'Gems/MyGem'",
                "$ref": "#/definitions/relativePaths"
            },
            "templates": {
                "description": "Relative path to a child template. i.e. 'Templates/MyTemplate'",
                "$ref": "#/definitions/relativePaths"
            },
            "repos": {
                "description": "Relative path to a child repos. i.e. 'Repos/MyRepo'",
                "$ref": "#/definitions/relativePaths"
            },
            "extensions": {
                "description": "Relative path to a child extension. i.e. 'Extensions/MyExtension'",
                "$ref": "#/definitions/relativePaths"
            }
        }
    }
    ```

24. A new group of remote objects
    * repo remote objects have been conflated with children of a repo object, remotes will disambiguate this conflation. Currently only engines and repo objects can declare remotes, i.e. an advertisement of an object that can be shown to the user in the o3de gui. The group will be of a list of each type of object enumerated as "engines", "projects", "gems", "templates", "repos", "extensions" and will be lists of json uri. i.e. "remote"{ "gems" {"[https://overlo3de.com/apmg/marine/gem.json"}}](https://overlo3de.com/apmg/marine/gem.json%22%7D%7D) this is an advertisement of a remote gem object. The json pointed to is a copy of the object json placed somewhere accessible to the internet, such as on a web server or ftp server. This could also be the "raw" link available from some git providers to access a single file inside a git repo without downloading the whole repo. i.e. "https://raw.githubusercontent.com/apmg/marine/refs/heads/development/gem.json" Note that "raw" files are usually throttled by git provider and web server copy should be preferred.
    ```json
    "remote": {
        "type": "object",
        "description": "A remote is URI to an o3de object json. Usually this is just a copy of the object's <object>.json file put somewhere others can retrieve it. Usually it is copied to a web/ftp server or even a local/network drive. The o3de object json should contain all information needed by the Project Manager to show the user that the object can be downloaded and become an entry in the 'local' list in your o3de_manifest.json.",
        "properties": {
            "engines": {
                "description": "URI of a remote engine.json. i.e. 'https://apmg.com/Engines/MyEngine/engine.json' or 'ftps://apmg.com/Engines/MyEngine/engine.json' or 'C:/APMG/Engines/MyEngine/engine.json'",
                "$ref": "#/definitions/engineJsonPaths"
            },
            "projects": {
                "description": "URI of a remote project.json. i.e. 'https://apmg.com/Projects/MyProject/project.json' or 'ftps://apmg.com/Projects/MyProject/project.json' or 'C:/APMG/Projects/MyProject/project.json'",
                "$ref": "#/definitions/projectJsonPaths"
            },
            "gems": {
                "description": "URI of a remote gem.json. i.e. 'https://apmg.com/Gems/MyGem/gem.json' or 'ftps://apmg.com/Gems/MyGem/gem.json' or 'C:/APMG/Gems/MyGem/gem.json'",
                "$ref": "#/definitions/gemJsonPaths"
            },
            "templates": {
                "description": "URI of a remote template.json. i.e. 'https://apmg.com/Templates/MyTemplates/template.json' or 'ftps://apmg.com/Templates/MyTemplates/template.json' or 'C:/APMG/Templates/MyTemplates/template.json'",
                "$ref": "#/definitions/templateJsonPaths"
            },
            "repos": {
                "description": "URI of a remote repo.json. i.e. 'https://apmg.com/Repos/MyRepo/repo.json' or 'ftps://apmg.com/Repos/MyRepo/repo.json' or 'C:/APMG/Repos/MyRepo/repo.json'",
                "$ref": "#/definitions/repoJsonPaths"
            },
            "extension": {
                "description": "URI of a remote extension.json. i.e. 'https://apmg.com/Extension/MyExtension/extension.json' or 'ftps://apmg.com/Extension/MyExtension/extension.json' or 'C:/APMG/Extension/MyExtension/extension.json'",
                "$ref": "#/definitions/extensionJsonPaths"
            }
        }
    }
    ```

25. Will add schema reference to all O3DE json
    * "$schema": "https://o3de.org/o3de-engine-2.0.0.json", to all engines
    * "$schema": "https://o3de.org/o3de-project-2.0.0.json", to all projects
    * "$schema": "https://o3de.org/o3de-gem-2.0.0.json", to all gems
    * "$schema": "https://o3de.org/o3de-template-2.0.0.json", to all templates
    * "$schema": "https://o3de.org/o3de-repo-2.0.0.json", to all repos
    * "$schema": "https://o3de.org/o3de-extension-2.0.0.json", to all extensions

26. Will create a script to automatically update schema 0.0.0 and 1.0.0 to 2.0.0 which will add "$schemaVersion": "2.0.0", to all O3DE json
    * This will be done by writing upgrade_schema.py and adding it functionality to o3de cli. 
    * i.e. c:/o3de/scripts> o3de upgrade_schema --object-json "c:/o3de-extras/Gems/ROS2/gem.json" --to-1.0.0 "c:/o3de-extras/Gems/ROS2/gem-1.0.0.json" 
    * i.e. c:/o3de/scripts> o3de upgrade_schema --object-json "c:/o3de-extras/Gems/ROS2/gem-1.0.0.json" --to-2.0.0 "c:/o3de-extras/Gems/ROS2/gem-2.0.0.json" 
    * i.e. c:/o3de/scripts> o3de upgrade_schema --object-json "c:/o3de-extras/Gems/ROS2/gem.json" will upgrade it in place 
    This will be run against and automatically upgrade every O3DE object in all O3DE repos. Unfortunately since we didn't have a schema that we consistently followed, people used the fields differently object to object and thus no script can guarantee the upgrade path, this can only be a best effort so these will have to manually checked for any errors. Also since schema 1.0.0 has multiple sources of truth and they are not in sync, mostly parent "version_data" may have to be manually copied back into the base objects before upgrading.
    
