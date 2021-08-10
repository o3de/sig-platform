# RFC - Linux Asset Platform

  - [Summary](#summary)
  - [Business Need](#business-need)
  - [Feature Breakdown](#feature-breakdown)
  - [Dependencies](#dependencies)
  - [Scope](#scope)
  - [Timeline](#timeline)

## Summary

This proposal is to create a new asset platform for Linux, in the same manner as the other existing asset platforms (PC, Mac, etc). Currently assets that are processed on Linux are placed in under 'PC' in the asset cache.

## Business Need

Adding the distinct 'Linux' asset type for Linux will make the assets unambiguous and distinct from the current 'PC' asset type. Having a distinct platform asset type allows for settings like optimization settings for Linux to be specific for Linux and won't be shared with the 'PC' settings. Additionally, compiled shader assets will be distinctly different on Linux than it is on PC.

## Feature Breakdown

- **'Linux' as a target asset type**  
    Create a distinct 'Linux' asset platform (along side the existing asset platforms) that is processed through the Asset Processor, and handled through the runtime clients.  
      
    

## Dependencies

- **Atom**  
    Issues that are uncovered with the Atom shader compiler on Linux may need some level of support to work through. The shader assets will use shaders compiled for Vulkan on the Linux platform.  
      
    
- **O3DE Community**  
    Issues that are uncovered will be posted in Github Issues so that we can leverage the communities expertise and resource to fix them.  
      
    

## Scope

- This project is limited to just support of a new asset platform for Linux in order to keep it distinct from any other platform (PC). There will be no other enhancements to any asset processing related to this.
- Originally the 'PC' asset platform was shared by Windows and Linux. 'PC' can be renamed to 'Windows' to match the naming pattern to the host platform, but that is out of scope of this RFC.
- The implementation will be based on Ubuntu 20.04 LTS. Any variance that is needed for other distributions is out of scope for this project.

## Timeline

| Task | Estimate |
| --- | --- |
| Add Linux as an asset platform | 10 Days |
| Fix all failed assets on Linux for Atom Sample Viewer and Automated Testing projects | 5 Days |

  

