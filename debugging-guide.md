# Debugging Guide

## Document Outline

This guide is comprised of the following steps:

```
- Initializing the Debugger Configurations
- Building the Source Code and Running the Debugger
- (Optional) Debugger Configurations "Anatomy"
```

***Note***: This guide ***assumes*** that the [prerequisites](./README.md#prerequisites) have been installed already. Furthermore, a setup comparable to that described in [Environment Setup](./env-setup.md) is also assumed here.

## Initializing the Debugger Configurations

To add dropdowns to the debugger view in VS Code, open location `/home/cs6290/sesc` as a VS Code project/folder via the Docker-based container environment (accessed via VS Code), and then at this top-level folder (i.e., `/home/cs6290/sesc`) add the contents in subdirectory of this repo `/.vscode` (canonical folder name for VS Code project configurations) to corresponding top-level folder there (i.e., `/home/cs6290/sesc/.vscode/`).

This will correspondingly add dropdowns in the `Run and Debug` view of VS Code, as follows:

<center>
<img src="./assets/debug-001.png" width="350px">
</center>

***Note***: The first section of the dropdown consolidates ***benchmarks*** (some of which are shared across projects), while the remaining sections of the dropdown are grouped on a project-specific basis (and additionally denoted by `Debug PRJ<...> Part <...> ....` on a per-part basis within a given project/grouping, as applicable). See corresponding project handouts and project-specific Ed FAQs (i.e., benchmark spreadsheets) for further reference.

## Building the Source Code and Running the Debugger

In the top-level `Makefile`, note that there are two primary `make` targets (via corresponding [SCONS](https://scons.org/)-based build system):
  * `make sesc.opt` (default, equivalent to running "bare" command `make`)
  * `make sesc.debug`

The project instructions (e.g., PRJ0) will instruct to perform the corresponding initial build commands. In particular, to build the initial source for SESC, run the following from the VS Code terminal (or equivalent):

```bash
cd ~/sesc
```
```bash
make
```

***Note***: This command may fail on the first attempt. Try re-running if ***errors*** occur (***warnings*** can be safely ignored otherwise).

Additionally, be advised that all of the provided debugger configurations require the corresponding simulation app to be compiled to MIPS prior to running the debugger (i.e., via corresponding command-line flag targeting files of general form `<...>.mipseb`). The project instructions will provide corresponding simulation-app specific targets, otherwise simply run the following commands:

```bash
cd ~/sesc/apps/Splash2
```
```bash
make
```

To build the debug version of the SESC app (with corresponding debugger symbols), run the following commands:

```bash
cd ~/sesc
```
```bash
make sesc.debug
```

Finally, to launch the debugger, select the appropriate configuration from the dropdown (as per the previous section of this guide), and press key `F5` (or equivalently green "play" icon).

After the pre-launch tasks complete execution, it should redirect to the debugger stopped at program entry point `main()` (per configuration option `"stopAtEntry": true` in file `/.vscode/launch.json`):

<center>
<img src="./assets/debug-002.png" width="1080px">
</center>

From this point onwards, you can set breakpoints, watch expressions, etc. to examine the run-time behavior of the running SESC program.

***Caution***: In order to ***change***/***update*** the source file(s), first **stop** the debugger, **save** changes, and then re-launch the debugger. The pre-launch tasks will correspondingly delete the previously generated report file and then rebuild the executables (via corresponding `sesc.opt` and `sesc.debug` make targets as per file `/home/cs6290/sesc/Makefile`), in order to trace the correspondingly updated version of the SESC application accordingly. Failure to do so (i.e., modifying the file ***while the debugger is still running***) will result in a "stale" state of the application, and correspondingly yielding ***indeterminate behavior*** of the debugger tracing. Accordingly, the debugging symbols used by `gdb` (which the VS Code GUI-based debugger "wraps over") via make target `sesc.debug` in the debugger tracing are based on the most recently compiled version of the program, immediately prior to launching the `gdb`-based VS Code debugger process.

Additionally, note the following general ***remarks*** regarding using the debugger configurations as provided here:
  * Setting `"stopAtEntry": true` in `launch.json` will prevent the debugger from "falling through" if no downstream breakpoints are set (otherwise, if already set immediately prior to and/or immediately following launching of the debugger, then hit `F5` to proceed to the next-occurring breakpoint accordingly on initial pause in `main()`)
  * `tasks.json` is defined such that on each launch of the debugger, existing report files are removed, and then subsequently the `make` targets `sesc.opt` and `sesc.debug` are run (i.e., to reflect updated changes in modified source files), and furthermore corresponding reports are generated in respective simulation-app-specific directories (e.g., `sesc_raytrace.mipseb.HyPart3` and `sesc_raytrace.mipseb.NTPart3` are generated in `/home/cs6290/apps/Splash2/raytrace`, and so on)
  * In addition to changing the debugger dropdown selection, ensure to make corresponding modification in config files (of general form `cmp<...>-noc.conf`, located in `/home/cs6290/sesc/confs/`) as necessary as per project instructions (i.e., this is not otherwise automated/configured in the provided debugger configs here)

## *(Optional)* Debugger Configurations "Anatomy"

***Note***: For additional information regarding  configuring and using the VS Code debugger, see the following references:
  * https://code.visualstudio.com/docs/editor/debugging
  * https://code.visualstudio.com/docs/editor/variables-reference
  * https://code.visualstudio.com/docs/cpp/launch-json-reference
  * https://code.visualstudio.com/docs/cpp/config-linux

