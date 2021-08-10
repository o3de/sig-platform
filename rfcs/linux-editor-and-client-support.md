# RFC - Linux editor and client support

  - [Summary](#summary)
  - [Business Need](#business-need)
  - [Feature Breakdown](#feature-breakdown)
  - [Dependencies](#dependencies)
  - [Scope](#scope)
  - [Timeline](#timeline)
  - [Questions and Concerns](#questions-and-concerns)

## Summary

Support the Editor and Runtime client on Linux platforms and bring the Linux host platform up to feature parity with Windows. This includes:

- Make sure the Editor launches using Atom as the enabled Renderer
- Make sure the Game Client launches using Atom as the enabled Renderer
- The Atom Sample Viewer ( [https://github.com/o3de/o3de-atom-sampleviewer](https://github.com/o3de/o3de-atom-sampleviewer) ) will be used as the baseline for content and rendering
- Support the Native Input implementation for Linux
- Support the Native Window implementation for Linux

## Business Need

The windows host platform has been primarily the first class citizen for O3DE. Linux does not have support for the host tools outside of the Asset Processor. Linux support as a host and tool platform is important not only for Linux gaming clients, but also other areas such as 3D simulations and virtual worlds.

## Feature Breakdown

- **Editor / Asset Processor Support**  
    Enable the Editor and Asset Processor GUI applications to launch on Linux without crashing.  
      
    
- **Native Input Support**  
    The Native Input abstraction layer in O3DE (Code/Framework/AzFramework/Input) allows for creating platform specific implementations on how input is handled for keyboards, mice, touch pad, etc. For Linux, the focus will specifically be on the following types of input:
    - Keyboard
    - Mouse  
          
        
- **Native Window Support**  
    The Native Window abstraction layer (Code/Framework/AzFramework/Windowing) allows for creation platform specific implementations on how windows and created and managed. Support for Native Windows on Linux will focus on the following modes:
    - Full Screen
    - Windowed  
          
        
- **Runtime Client Support**  
    The run time game/project launcher will be able to launch and run natively on Linux. This should be true for both development and release builds of the runtime game client.

## Dependencies

- **Atom**  
    Issues related to Atom's Vulkan implementation may be uncovered as progress is made enabling and fixing items in the Editor and Runtime client. For graphics/vulkan related issues, there may need to be some support from the Atom team to help troubleshoot/solve such issues.  
      
    
- **O3DE Community**  
    Issues that are uncovered will be posted in Github Issues so that we can leverage the communities expertise and resource to fix them.  
      
    
- **RFC - Linux Asset Platform**  
    Editor and Runtime needs assets built for Linux in order to work. The RFC for the Linux Asset Platform is a dependency on this effort, but not completely a blocker since progress can be made using the existing Linux assets compiled for the 'PC' asset platform. The completion of this effort will depend on the completion of the Linux Asset Platform effort as well.

## Scope

- This project will be focused on getting the Editor and Tools to be able to launch and do basic operations with respect to editing and processing a game/project.
- Any trait that was disabled for Linux but enabled for windows will be removed.
- Any blocker defects related to the Editor and Run time will be in the scope of this effort
- Any critical/major/minor  defects will not be part of the acceptance criteria for this effort, but will still be triaged appropriately
- Although the implementation will avoid any ubuntu specific systems or libraries, the implementation will be based on the current 3rd Party package dependencies that were based on pre-built binaries built on Ubuntu 20.04 LTS. Any variance that is needed for other distributions is out of scope for this project.
- Scope will be limited to running the Editor and Client against the Atom Sample Viewer project
- The abstraction to the Linux display server will be done with xcb at first, but we will leave the possibility to support wayland or xlib (based on vulkan). Implementing wayland and/or xlib is out of scope.

## Timeline

| Task | Estimate |
| --- | --- |
| Fix Atom / Vulkan Issues | TBD |
| Fix Additional Blocker defects for Editor | TBD |
| Implement Native Window for Linux full screen support | 5 Days |
| Implement Native Window styles & resize for Linux support | 3 Days |
| Implement Native Input support for Linux | 5 Days |
| Enable / Fix remaining Disabled Unit tests for Linux | 15 Days |
| Enable LyTestTools for Linux | 1 Day |
| Fix failing tests from LyTestTools in Linux | TBD |

  

## Questions and Concerns

- The amount of blocker/crash bugs cannot be estimated at this time. This is only possible as each blocker crash is fixed to allow the start up for the Editor / Game Client to continue.  
      
    
- The Atom gem's current Linux / Vulkan RHI is based on using xcb to create and manage windows. XCB is not the only client interface to Linux's display server. There is Wayland and XLib as well. The abstraction layers in O3DE should allow for us to be flexible to use any one of these clients, but more research may be needed, and that is not in the scope of this effort. For now want to proceed with xcb in order to get the editor and runtime up and running as soon as possible.  
      
    - The Editor's interface is currently using Qt's xcb interface to create and manage windows for Linux. Because of this, implementation will focus on using xcb for the rendering/vulkan implementation.  
         
        
    - For the game client, which does not rely on Qt at all, will plan on continuing use of xcb to manage native windows (and input) for game clients.  
          
        
- There have been discussions / questions about why we are not using SDL for managing the display. There was also points about SDL being able to manage sound and input as well and as an easier path forward to support Linux. The following are some points that came up from the discussions  
      
    
    - SDL can handle the xcb/wayland abstraction out of the box so we don't have to.  
          
        
    - Lumberyard was architected around a unified application / input abstraction layer and better fits with lower level client APIs like XCB. SDL abstracts these layers out as well.  
          
        
    - If we really want to use SDL, it is not a natural fit into our API abstraction, and we may end up having to wrapping our abstractions around theirs