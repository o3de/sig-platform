# Test Plan: ARM64 Linux Dedicated Server
### **1.0 Introduction:**

ARM64 is a different CPU architecture based on reduced instruction set compute (RISC) standards. ARM64 consumes less energy and is more economical to run than what other O3DE binaries target for architecture. A sig-platform RFC proposes extending support for running dedicated server on ARM64.

This document covers high level risk mitigation plans for confirming functional support for ARM64 Ubuntu dedicated game server on AWS Graviton instances. ARM64 is offered by many cloud compute services; this plan will focus on AWS Graviton EC2 instances. Additional work beyond the scope of this document is required to demonstrate function with other providers.

##### **1.1 References:**

- RFC for O3DE support https://github.com/o3de/sig-platform/blob/main/rfcs/linux-arm64-support.md
- AWS instance type for ARM64 https://aws.amazon.com/ec2/graviton/
- AWS managed game server ARM64 support https://aws.amazon.com/about-aws/whats-new/2021/10/new-region-availability-graviton2-support-amazon-gamelift/
- Google Cloud instance type for ARM64 https://cloud.google.com/compute/docs/instances/arm-on-compute
- Microsoft Azure instance type for ARM64 https://azure.microsoft.com/en-us/blog/azure-virtual-machines-with-ampere-altra-arm-based-processors-generally-available/

### **2.0 Defect Tracking Issues:**

Bugs will be tracked with Github Issues (GHI) primarily entered in [https://github.com/o3de/o3de](https://github.com/o3de/o3de).
Labels will include [`kind/bug`](https://github.com/o3de/o3de/labels/kind%2Fbug), [`feature/networking`](https://github.com/o3de/o3de/labels/feature%2Fnetworking), [`platform/linux`](https://github.com/o3de/o3de/labels/platform%2Flinux), [`arch/arm64`](https://github.com/o3de/o3de/labels/arch%2Farm64), [`sig/platform`](https://github.com/o3de/o3de/labels/sig%2Fplatform), and initially [`needs-triage`](https://github.com/o3de/o3de/labels/needs-triage).

### **3.0 Test Schedule and Entry Criteria:**

Testing cannot begin until entry criteria are met and required test documents are available.

One individual should be able to setup an environment and conduct basic deployment scenarios within a couple of days. Additional time would be needed for bug fix verification and any regression testing.

##### **3.1 Remaining Test Tasks:**


##### **3.2 Test Entry Criteria**

- Documentation is available for deploying binaries to ARM64 Ubuntu and running "headless" (-rhi=null -NullRenderer)
- Documentation is available for building cross-platform or natively on ARM64
- ARM64 build is free of errors for both DLY_UNITY_BUILD ON and OFF
- ARM64 builds on platform and/or cross-platform
- Unit tests related to non-render context server and game systems pass consistently

### **4.0 Risks:**

Risks are scored using a risk assessment matrix of impact and likelihood https://en.wikipedia.org/wiki/Risk_matrix

| Risk                                                                                                                                                                                                               | Mitigation                                                                                                                                                                                                                                                                                                     | Detection Method                                                                          | Notes                          | Impact        | Likelihood  | Risk Level     |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|--------------------------------|---------------|-------------|----------------|
| Non-render context server has not been shown to work on Ubuntu. If a Vulkan render hardware interface (RHI) context is required, GPU instances my be required. GameLift doesn't support G5g (ARM64 GPU) instances. | Sig-platform can utilize G5g AWS EC2 instances if required. This will require that account limits for G and VT instance be non-zero (likely greater than 8 to account for 2xlarge CPU count). This precludes the use of GameLift which doesn't list this as an option.                                         | Exploratory testing will reveal if `-rhi=null -NullRenderer` will work on non-GPU context |                                | marginal      | unlikely    | 2 - negligible |
| GameLift MultiplayerSample project deployment as a means of testing involves additional unknown work over simply using a C6g instance to build and run. We may see schedule delays as a result.                    | Sig-platform will iteratively work to address blocking and critical platform bugs exposed by deploying to GameLift. This is likely to result in schedule overrun if not accounted for in estimates.                                                                                                            | Exploratory testing will reveal if GameLift deployed servers can function                 |                                | marginal      | very likely | 8 - low        |
| MultiplayerSample project server is expected to take additional work to get running on ARM64 Ubuntu                                                                                                                | Sig-platform will iteratively work to address blocking and critical platform bugs exposed. This is likely to result in schedule overrun if not accounted for in estimates.                                                                                                                                     |                                                                                           |                                | marginal      | very likely | 8 - low        |
| ARM64 vm instances are not free. Even though the cost is negligible, payment responsibility is not defined for this resource.                                                                                      | T4g.small is within the free tier for up to 750 hours, however, is insufficient to run dedicated server with only 2 CPU and 2 GiB memory. C6g.2xlarge & C7g.2xlarge on-demand prices are about $0.30/hour. GameLift costs should be in scale with that. Open 3D Foundation would cover the cost if not others. |                                                                                           |                                | insignificant | certain     | 5 - Low        |

##### **4.1 Assumptions and Dependencies**

- Dedicated server without a render context (terminal or service only, AKA 'headless') can demonstrate stability without complex client configuration. A server indicates its health state without requiring clients. Reported health state is an enterprise level requirement for server applications.
- AWS EC2 `C6g` or `C7g` compute instances can be used with standard instance limit quotas. GPU instances like `G5g` require increased instance limit for G and VT instances.
- Either AWS Graviton2 or Graviton3 are functionally sufficient to confirm ARM64 support.
- O3DE ARM64 support on Ampere Altra Arm is unknown.
- AWS EC2 `.2xlarge` is a suitable instace size with 8 vCPU and 16 GiB memory.
- MultiplayerSample project is suitable for testing on ARM64

### **5.0 In Scope:**

- Unit tests

##### **5.1 Test Items:**

- Dedicated server runs with `-rhi=null -NullRenderer` options and maintains a stable non-render context console state
- Dedicated server runs without a render context (command line console) on ARM64
- If GameLift is available, we can connect clients to a deployed ARM64 GameLift MultiplayerSample dedicated server

### **6.0 Out of Scope:**

- Client connection is optional without GameLift (configuring vm's for connection increases the scope of testing)
- Unit tests for Editor
- Editor
- Editor Python Bindings (EPB) tests

### **7.0 Test Approach:**

##### **7.1 Test Method**

This is a listing of the test strategies that will be used for test execution. Examples of common test strategies include but are not limited to:

**Dynamic:** Exploratory testing will be used at both early and late stages of a feature development, test approach, test cases and risk analysis will adapt to new information as this testing is completed.

**Consulted:** Input from the developer of the feature, and potentially from customers, will be used to help determine areas to test.

##### **7.2 Automation Approach**

- Unit tests

##### **7.3 Test Data and Environments**  
- AWS G5g (Graviton2) NVIDIA T4G Tensor Core GPUs (these would be if GPU is required and would require account limits for G type CPU's to be raised to greater than 8)
- AWS C6g (Graviton2) or C7g (Graviton3) 2xlarge instances or recommended size.

### **8.0 Test Exit Criteria:**

- [`priority/critical`](https://github.com/o3de/o3de/issues?q=is%3Aopen+label%3Apriority%2Fcritical+label%3Akind%2Fbug+label%3Asig%2Fplatform+label%3Aplatform%2Flinux+label%3Afeature%2Fnetworking) and [`priority/blocker`](https://github.com/o3de/o3de/issues?q=is%3Aopen+label%3Apriority%2Fblocker+label%3Akind%2Fbug+label%3Asig%2Fplatform+label%3Aplatform%2Flinux+label%3Afeature%2Fnetworking) triage labeled github issues are closed appropriately
- Specified functionality is confirmed to exist and function.
- All unit tests for the feature are consistently passing

