# SystemC-first-project
this project is a guide for those want to start using systemc on viusal studio (windows os ) 



This release has been tested on Visual C++ version 2019 running Windows 10.

Note: This section covers the installation based on Microsoft Visual C++. For Cygwin or MinGW-based installations, see Section 1.

Note: If you experience spurious errors about missing files in the downloaded archive, please make sure to either download the ZIP archive from accellera.org or use a reliable archive software, fully supporting modern tar archive versions.

Some paths in the SystemC archive are longer than the historical 99 character limit, and several Windows archivers (e.g. WinZip) have been reported to trip over this. The open source archiver 7-zip is known to work.

Microsoft Visual C++ 2017 (compiler version 15.0) or later
The download directory contains two subdirectories: msvc16 and examples/build-msvc.

The msvc16 directory contains the project and workspace files to compile the systemc.lib library. Double-click on the SystemC.sln file to launch Visual C++ 2019 with the workspace file. The workspace file will have the proper switches set to compile for Visual C++ 2019. Select Build SystemC under the Build menu or press F7 to build systemc.lib.

The examples/build-msvc directory contains the project and workspace files to compile the SystemC examples. Go to one of the examples subdirectories and double-click on the .vcxproj file to launch Visual C++ with the workspace file. The workspace file will have the proper switches set to compile for Visual C++ 2019. Select 'Build .exe' under the Build menu or press F7 to build the example executable.

For convenience, a combined solution file SystemC_examples.sln with all example projects can be found in the examples/build-msvc directory. A similar solution file tlm_examples.sln for the TLM examples is available as well.

The provided project files are prepared for both the 32-bit (Win32) and 64-bit (x64) configurations. Please refer to the Microsoft Visual Studio documentation for details about 64-bit builds.

In addition to building static libraries for SystemC, the provided project files include support for building a SystemC DLL (configurations DebugDLL, ReleaseDLL).

Creating SystemC Applications
Start Visual Studio. From the Start Page select New Project and Win32 Console Project. Type the project name and select a suitable location then click OK.

Select the Application Settings page of the Win32 Application Wizard and make sure the 'Empty project' box is ticked. Click 'Finish' to complete the wizard.

Add new/existing C++ files to the project and edit code.

Display the project Property Pages by selecting 'Properties...' from the Project menu.

From the C/C++ tab, select the Language properties and set 'Enable Run-Time Type Info' to Yes.

From the C/C++ tab, select the Command Line properties and add /vmg to the 'Additional Options:' box.

From the Linker tab, select the Input properties and type systemc.lib in the 'Additional Dependencies' box.

Click OK.

Also make sure that the compiler and linker can find the SystemC header and library files respectively. There are two ways to do this:

To update the include file and library directory search paths for all projects:

Select Tools -> Options... and the Projects -> VC++ Directories tab

Select show directories for: Library files

Select the 'New' icon and browse to: C:\systemc-3.0.0\msvc16\systemc\debug

Select show directories for: Include files

Select the 'New' icon and browse to: C:\systemc-3.0.0\src

To add the include file and library directory search paths for the current project only:

Display the project Property Pages by selecting 'Properties...' from the Project menu.

From the C/C++ tab, select the General properties and type the path to the SystemC 'src' directory in the text entry field labeled 'Additional include directories' (e.g., the examples use ..\..\..\src).

From the Linker tab, select the General properties and type the path to the SystemC library: ...\systemc-3.0.0\msvc16\systemc\debug in the 'Additional Library Directories:' box.

Click OK.

Building against a SystemC DLL
In order to link your application against a DLL-build of SystemC (build configurations DebugDLL, ReleaseDLL in the SystemC library build), several changes are needed.

Adjust the linker library directory settings to reference DebugDLL (or ReleaseDLL) instead of Debug or Release, respectively:
...\systemc-3.0.0\msvc16\systemc\DebugDLL

Add the preprocessor switch SC_WIN_DLL to your project's properties
(C/C++ -> Preprocessor -> Preprocessor Definitions).

When running the simulation, you need to add the location of the SystemC DLL to your PATH variable.

SystemC Library Configuration Switches
In addition to the explicitly selectable feature given as options to the configure script (see 1.), some aspects of the library implementation can be controlled via

preprocessor switches given during library build
preprocessor switches added while building a SystemC application
environment variables
The currently supported switches are documented in this section.

Preprocessor switches
Additional preprocessor switches for the library build can be passed to the configure script via the CXXFLAGS variable:

  ../configure CXXFLAGS="-DSC_OVERRIDE_DEFAULT_STACK_SIZE=0x80000"
In Visual C++, the preprocessor symbols can be added to the project configuration via the 'C/C++' tab under the 'Preprocessor' properties in the 'Preprocessor definitions' setting. Alternatively, you can add the switches to the SystemC.vsprops property sheet to apply these settings to all build configurations.

SC_CPLUSPLUS
Override automatically detected C++ standard support

This setting allows downgrading the assumed version of the underlying C++ standard on the current platform. By default, the compiler-selected version is chosen.

Currently known values are:

SC_CPLUSPLUS=201703L (C++17, ISO/IEC 14882:2017)
SC_CPLUSPLUS=202002L (C++20, ISO/IEC 14882:2020)
SC_CPLUSPLUS=202302L (C++23, ISO/IEC 14882:2023)
Note: This symbol needs to be consistently defined in the library and any application linking against the built library.

SC_DEFAULT_WRITER_POLICY=<sc_writer_policy>
Override default value for the signal writer policy

This setting allows deactivating the multiple writer checks for sc_signals at (application) compile time. This mechanism supersedes the old environment variable SC_SIGNAL_WRITE_CHECK (see below).

Supported values:

SC_ONE_WRITER (default)
SC_MANY_WRITERS (allow multiple writers in different deltas)
SC_UNCHECKED_WRITERS (non-standard, disable all checks)
Note: Only effective when building an application.
Note: This setting needs to be consistently set across all translation units of an application.

SC_DISABLE_VCD_SCOPES
Disable grouping of VCD trace variables in hierarchical scopes by default

Note: Only effective during library build.
See : Environment variable SC_VCD_SCOPES

SC_DISABLE_COPYRIGHT_MESSAGE
Do not print the copyright message when starting the application

Note 1: This does not remove the copyright from the binary. sc_core::sc_copyright() still works as expected.

Note 2: Only effective during library build.
See : Environment variable SC_COPYRIGHT_MESSAGE

SC_ENABLE_ASSERTIONS
Always enable the sc_assert expressions

Some build systems define NDEBUG by default in optimised build configurations. As a result, the SystemC assertion macro sc_assert() is disabled (similar to the C assert() macro). By defining this preprocessor symbol (when building the library and/or an application), the sc_assert() checks are always enabled, irrespectively of the definition of NDEBUG.

SC_INCLUDE_FX
Enable SystemC fixed-point data-types

To improve compilation times, the fixed-point data-types are not enabled by default in an SystemC application. Define this symbol before including the SystemC header in your application, if you want to use the SystemC fixed-point types.

Note: Is by default always defined during the library build to enable later use of the fixed-point data-types in an application.

Note: Can be optionally set per translation unit in an application.

SC_INCLUDE_WINDOWS_H
Explicitly include <windows.h> header from <systemc> header

Previous versions of SystemC always included the full <windows.h> header on all Windows platforms. This adds unnecessary bloat to many SystemC applications, increasing compilation times. If you rely on the inclusion of the <windows.h> header in your application, you can add this symbol to the list of preprocessor switches for your compiler.

Note: Only effective when building an application.

SC_INCLUDE_EXTRA_STD_HEADERS
Include <cstring> and <sstream> headers from <systemc> header

Previous versions of SystemC implicitly included the <cstring> and <sstream> headers on all platforms without depending on their contents. If you rely on the inclusion of these headers in your application, you can add this symbol to the list of preprocessor switches for your compiler.

Note: Only effective when building an application.

SC_ALLOW_MACROS_WITHOUT_SEMICOLON
Allow using (process) macros without terminating semicolon

Previous versions of SystemC allowed using some macros without a trailing semicolon. This is no longer supported by default. Defining the above macro restores the old behavior. Affected macros: SC_METHOD, SC_(C)THREAD.

Note: Only effective when building an application.

SC_OVERRIDE_DEFAULT_STACK_SIZE=<size>
Define the default stack size used for SystemC (thread) processes

Note: Only effective during library build.

SC_USE_STD_STRING Define sc_string symbol as an alias to std::string

Pre-IEEE-1666 versions of SystemC included an sc_string class for string objects. This class has been superseeded by std::string these days.

SC_WIN_DLL
Build (against) a DLL build of SystemC (Windows/MSVC only)

Needs to be set when building a SystemC DLL on Windows, as well as when building an application/library to be linked against a DLL version of SystemC.

Influential environment variables
Currently, three environment variables are checked at library load time and influence the SystemC library's behavior:

SC_COPYRIGHT_MESSAGE=DISABLE
Run-time alternative to SC_DISABLE_COPYRIGHT_MESSAGE (see above).

SC_SIGNAL_WRITE_CHECK=DISABLE, SC_SIGNAL_WRITE_CHECK=CONFLICT
Run-time alternative to SC_DEFAULT_WRITER_POLICY (see above).

DISABLE = disable all checks for conflicting writes (SC_UNCHECKED_WRITERS)
CONFLICT = detect conflicting writes within the same evaluation phase (SC_MANY_WRITERS)
SC_DEPRECATION_WARNINGS=DISABLE
Do not issue warnings about using deprecated features as of IEEE Std. 1666-2011.

SC_VCD_SCOPES=DISABLE, SC_VCD_SCOPES=ENABLE
Run-time configuration of hierarchically scoped names in VCD trace files (see SC_DISABLE_VCD_SCOPES).

Usually, it is not recommended to use any of these variables in new or on-going projects. They have been added to simplify the transition of legacy code.


for any question mail me on dagmazen@gmail.com
