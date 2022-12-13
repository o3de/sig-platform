# RFC - Linux ARM64 Support

## Summary
This document will propose the expansion of support for Linux to include ARM64 (also known as aarch64) processors in addition to the current Intel x86 support.

## What is the relevance of this feature?

O3DE only supports the Intel x86 based CPU architecture for its host platforms (Windows, Mac, and Linux). This feature will expand O3DE’s compatibility to include ARM64 based Linux environments such as ones available in cloud computing. The ARM architecture is gaining popularity in cloud computing due to its lower power consumption and reduced cost, and having O3DE projects such as multi-player game servers running on ARM-based servers provides potential infrastructure cost savings. 

## Feature design description

O3DE can only be built on a host platform (Windows, Linux, or Mac), and will default the target build to the current host platform that it is on. Some platforms, such as Android and iOS, can be cross-compiled through specification of the target platform’s specific tool chain or custom generator. Introducing an architecture flavor of a host platform will introduce a new use case where a host platform on one cpu architecture can be a target platform on another host platform with a different cpu architecture through cross compiling.  However we will limit the feature to allow cross compiling ARM64 on an x64 machine, and not the other way around (unless there is a practical scenario to justify it). 

There are two use cases that will support building for Linux / ARM64

* **Compiling O3DE for Linux/ARM64 on a Linux/ARM64 host.**<br>
  When building on an Linux/ARM64 host, the default Linux/clang toolchain will already locate the native ARM64 tools.
* **Cross Compiling O3DE for Linux/ARM64 on a Linux x86_64 host**<br>
  To cross compile for ARM64 on a Linux x86_64 host, a custom toolchain will be specified that is designed to look for arm64 tools that run on x86 machines.

## Technical design description

The cmake build system controls what and how things are built, so the majority of the change will occur in cmake. In particular, the follow areas of cmake will be impacted.

* **3rd Party pre-built binary system**<br>
The 3rd Party package system uses pre-built libraries that are downloaded from CloudFront. The packages are organized by platform, so depending on the target platform that is being built, the right platform-specific package is downloaded and utilized appropriate. There is currently only one architecture per platform.  The host platforms (Windows, Mac, Linux) uses x86 based packages, while the target platforms (iOS, Android) uses ARM based packages. Because of this, only the platform name was needed to identify right packages to use. By expanding the ability for Linux to support multiple architectures, the selection criteria will need to expand to include the architecture as well. 
For backwards compatibility and to support the packages that already have been built for their respective architecture, the selection criteria will fall back to just platform name if the current architecture is the original default architecture.
* **Platform Abstraction Layer (PAL) separation**<br>
The PAL layer was designed to abstract platform specific cmake scripts and definitions based on only the platform name. There will be no changes to this architecture. Instead, anything that differentiates a platform by different architectures will be handled by the platform’s PAL script directly. The existing Linux-specific cmake scripts will make any adjustments that are architecture dependent within the script itself without the need of introducing yet another layer of abstraction. 
* **CMake tool chain for Linux/ARM64**<br>
There will be a new toolchain needed to build Linux on the ARM64 architecture. The toolchain may need to handle the case of native ARM64 compilation (e.g. building on an ARM64 host machine directly) and cross compiling (e.g. building using ARM64 cross compilers on an x64 based host platform).

## What are the advantages of the feature?

* Support for ARM64 based processors will provide cost saving benefits on cloud based workflows vs the traditional x86 architecture. Over time this could result in significant savings over applications such as multiplayer servers and hosting simulations.
* Introducing the ability support multiple architectures for the same platform will set the framework for other multi-architecture platforms in the future.

## What are the disadvantages of the feature?

* This will add another platform support burden in the following areas

  * **Build support (Build Nodes, AR, etc)** <br>
  Every platform that is supported needs infrastructure setup to include it in the automated review process. This will increase the infrastructure usage cost and potentially increase the duration of pull requests. Also, adding new platforms to the AR process adds additionalk burden on the community and contributors to make sure their changes/contributions pass on that platform.

  * **Feature Parity** <br>
  Every feature that is supported on the current host platforms will need to be supported on the new architecture as well. This will increase automated and manual testing. (This can be mitigated by making Linux ARM64 as a target platform, ie disabling any editor or tools as part of the build)

* All supported 3rd party libraries will need to be built on Linux ARM64 and packaged

  * Every new 3rd Party library dependency must be built on an ARM64 architecture in addition to its x86 counterpart
  * Risk of an external 3rd Party library not supporting ARM

## How will this be implemented or integrated into the O3DE environment?

The majority of the changes to existing code/scripts will be in the cmake / PAL related code to add the architecture detection along with the current platform detection. 

## Are there any alternatives to this feature?

The alternative to this feature is to not support it and only support the x86 architecture.

## How will users learn this feature?

The first use cases that come to mind are ones that require O3DE to run on servers, particularly cloud-based servers. Once complete, the feature to support Linux/ARM64 will be part of the release information for the tagged release it will be delivered in (Estimated end of Q1 2023). 

## Frequently Asked Questions

**Q**: Adding any new platform variant introduces a burden on contributors that changes/additions must accept. For Linux and ARM64, this may be especially difficult given that the platform may not be as easy to procure to fix any build or test failures. Should this Linux ARM64 be a gating platform for submission or be treated as a non-gating periodic build. <br>
**A**: Except for build failures, automated test failures for ARM64 should be not gate pull requests from submission into the development branch<br>

**Q**: What will be the performance of O3DE in ARM-based processors? Will it be required to do any ARM-specific code optimizations? <br>
**A**: The impact of ARM64 vs x86 should mostly occur in areas that utilize SIMD. Areas of the code that is related to these operations will need to be able to either support NEON or implement non-optimized versions of these operations. We do have Android ARM support currently so that may be a good guideline<br>

**Q**: What 3rdParty packages that O3DE uses do not have ARM support in their cmakes and will be required to be implemented? <br>
**A**: Of the current 3rd Party packages that is used by Linux so far, only *squish-ccr* and *ISPCTexComp* have issues being built in ARM64.<br>

**Q**: What are the core/most needed 3rdParty libs that O3DE needs to run? <br>
**A**: That will depend on the type target that is being built (GameLauncher, ServerLauncher, Editor, etc) <br>

**Q**: What if we find a 3rd Party library dependency that does not support ARM64? <br>
**A**: Depending on the library and its scope within the project, a mitigation strategy would be to gate the inclusion of that feature through a PAL trait. The disabled feature itself can be disable, ignored (with a warning), or trigger an error, but that depends on the nature of the feature. <br>


**Q**: Will developers be able to produce ARM-based 3rdParty packages if they don't have a arm-based linux machine? <br>
**A**: It is possible to cross compile for ARM64 on an x86 host, but the procedure to do that will vary depending on the package. Another alternative is to run an ARM64 based Docker image on an x86 device through any support virtualization software such as QEMU<br>

