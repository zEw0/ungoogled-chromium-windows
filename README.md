# ungoogled-chromium-windows

Windows packaging for [ungoogled-chromium](//github.com/Eloston/ungoogled-chromium).

## Downloads

[Download binaries from the Contributor Binaries website](//ungoogled-software.github.io/ungoogled-chromium-binaries/).

**Source Code**: It is recommended to use a tag via `git checkout` (see building instructions below). You may also use `master`, but it is for development and may not be stable.

## Building

**IMPORTANT**: Please setup only what is referenced below. Do NOT setup other Chromium compilation tools like `depot_tools`, since we have a custom build process which avoids using Google's pre-built binaries.

### Build requirements:

1. Git - For downloading required building scripts
2. 7-zip - For unzipping required building scripts
3. Python version 3.6 - 3.9 or 3.10.2+ - For building and packaging scripts
4. Python version 2.7.18
5. Visual Studio 2019
6. Windows 10 SDK - Choose the Windows 10 SDK even when you are using Windows 11.

**IMPORTANT**: Add both Python versions to path and also disable path length limit (MAX_PATH)       

**RECOMMENDATION**: It is recommended that you go to both of your Python version's installtion path and make a copy of the `python.exe` file.                                                          
* Rename the copy `python.exe` file to `python2` if the Python version number starts with a 2.
* Rename the copy `python.exe` file to `python3` if the Python version number starts with a 3.

#### Required Python packages:

1. pypiwin32
2. future

### Environment Variables

1. Right click on your Windows start button => System => Advanced system settings => Environment Variables
2. Create a new system variable
    * Variable Name: `vs2019_install` - Put your Visual Studio installation path in the variable value section.
    * Variable Value: `DRIVE:\path\to\Microsoft Visual Studio\2019\Community` (Replace `Community` with your installed version)
3. Create another new system variable
    * Variable Name: `WINDOWSSDKDIR` - Put your Windows 10 SDK installation path in the variable value section.
    * Variable Value: `DRIVE:\path\to\Windows Kits\10`
4. Create one last new system variable
    * Variable Name: `GYP_MSVS_VERSION` - Put your Visual Studio version in the variable value section.
    * Variable Value: `2019`

### Building ungoogled-chromium

**IMPORTANT**: If you are using a 32-Bit system, please go to: `flags.windows.gn` and change `target_cpu` to `"x86"`

Now that you've finished setting up the required packages to build ungoogled-chromium, you are now ready for the final step.

1. Open up your command prompt as an administrator and enter the following commands in order.
    * `git clone --recurse-submodules https://github.com/ungoogled-software/ungoogled-chromium-windows.git`
    * `git checkout --recurse-submodules TAG_OR_BRANCH_HERE` - Replace TAG_OR_BRANCH_HERE with a tag or branch name
    * `py build.py` - This may take a while depending on your cpu.
    * `py package.py` - After the building process has been completed you may enter this command.
2. A zip archive and an installer will be created after your building process has been completed

### Tips

If your build fails, you **must** take additional steps before re-running the build:

* If the build fails while downloading the Chromium source code (which is during `build.py`), it can be fixed by removing `build\download_cache` and re-running the build instructions.

* If the build fails at any other point during `build.py`, it can be fixed by removing everything under build other than `build\download_cache` and re-running the build instructions. This will clear out all the code used by the build, and any files generated by the build.

If your build keeps failing and you are tired of waiting a long time to delete those large files, try using this powershell command `Remove-Item PATH -Recurse -Force`.                     
**Warning**: Files deleted by this command^^ will be permanently lost.

## Developer info

### First-time setup

1. [Setup MSYS2](http://www.msys2.org/)
2. Run the following in a "MSYS2 MSYS" shell:

```sh
pacman -S quilt python3 vim tar
# By default, there doesn't seem to be a vi command for less, quilt edit, etc.
ln -s /usr/bin/vim /usr/bin/vi
```

### Updating patches

**IMPORTANT**: Run the following in a "MSYS2 MSYS" shell:

1. Navigate to the repo path: `cd /path/to/repo/ungoogled-chromium-windows`
    * You can use Git Bash to determine the path to this repo
    * Or, you can find it yourself via `/<drive letter>/<path with forward slashes>`
2. Setup patches and shell to update patches
    1. `./devutils/update_patches.sh merge`
    2. `source devutils/set_quilt_vars.sh`
3. Setup Chromium source
    1. `mkdir -p build/{src,download_cache}`
    2. `./ungoogled-chromium/utils/downloads.py retrieve -i ungoogled-chromium/downloads.ini -c build/download_cache`
    3. `./ungoogled-chromium/utils/downloads.py unpack -i ungoogled-chromium/downloads.ini -c build/download_cache build/src`
4. Go into the source tree: `cd build/src`
5. Use quilt to refresh patches. See ungoogled-chromium's [docs/developing.md](https://github.com/Eloston/ungoogled-chromium/blob/master/docs/developing.md#updating-patches) section "Updating patches" for more details
6. Go back to repo root: `cd ../..`
7. Remove all patches introduced by ungoogled-chromium: `./devutils/update_patches.sh unmerge`
    * Ensure patches/series is formatted correctly, e.g. blank lines
8. Sanity checking for consistency in series file: `./devutils/check_patch_files.sh`
9. Check for ezbuild dependency changes in file `build/src/DEPS` and adapt `downloads.ini` accordingly
10. Use git to add changes and commit

## License

See [LICENSE](LICENSE)
