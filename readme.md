# PowerShell Desired State Configuration for Linux
*Copyright (c) Microsoft Corporation ver. 1.1.1*
*All rights reserved.* 

MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the ""Software""), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED *AS IS*, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#Getting Started#

**Supported Linux operation system versions **
The following Linux operating system versions are supported for DSC for Linux. 
- CentOS 5, 6, and 7 (x86/x64)
- Debian GNU/Linux 6, 7 and 8 (x86/x64) 
- Oracle Linux 5, 6 and 7 (x86/x64) 
- Red Hat Enterprise Linux Server 5, 6 and 7 (x86/x64) 
- SUSE Linux Enterprise Server 10, 11 and 12 (x86/x64) 
- Ubuntu Server 12.04 LTS and 14.04 LTS (x86/x64) 

## Requirements ##
The following table describes the required package dependencies for DSC for Linux.

 **Required package** 	| **Description** 	| **Minimum version**
--------------------- | --------------------- | -------------------
Glibc |	GNU C Library	| 2.4 - 31.30
python | Python | 2.4 - 3.4
omi | Open Management Infrastructure | 1.0.8-4
openssl| OpenSSL Libraries | 0.9.8e or 1.0
python-ctypes|Python CTypes library | Must match Python version
libcurl | cURL http client library | 7.15.1

OMI Packages can be found at: [https://collaboration.opengroup.org/](omi/documents.php?action=view&gdid=34607)

##Installing DSC Packages##

```
wget https://github.com/Microsoft/PowerShell-DSC-for-Linux/releases/download/v1.1.1-70/dsc-1.1.1.packages.tar.gz
tar -xvf dsc-1.1.1.packages.tar.gz

#Install the appropriate package for your architecture (x86 or x64) and OpenSSL version (1.0.x or 0.9.8):
#sudo dpkg -i dsc-1.1.1-70.ssl_100.x64.deb
#sudo rpm -Uvh dsc-1.1.1-70.ssl_100.x64.rpm

```

**For more information, review the latest [release notes](https://github.com/Microsoft/PowerShell-DSC-for-Linux/releases/tag/v1.1.1-70) and [product documentation](https://msdn.microsoft.com/en-us/powershell/dsc/lnxgettingstarted).**

## To author DSC MOF configuration for Linux on a Windows computer:

----------


**Prerequisites**

* A Windows computer with:
  * Adminstrative privileges
  * Windows PowerShell (>=4.0)


* Install the Linux Resource Provider MOF module:
  * The "nx" module can be installed from the PowerShell Gallery with:
	`install-module nx`
   * In order to compile a Configuration MOF that uses the DSC for Linux resources, use `Import-DscResource -Module nx` inside a DSC Configuration block.
  
* Managing a Linux system with DSC
   * You need a compiled configuration MOF to apply a new configuration to a system.  Please refer to part 1 of this section, as well as the DSC for Windows documentation, for instructions on how to generate a configuration MOF.
   * Once you have a MOF, you can apply it by running:
        `Start-DscConfiguration -CimSession:$myCimSession -Path:"C:\path_to_compiled_mof_directory\" -Wait -Verbose`
   * You can get the current configuration of the system by running:
        `Get-DscConfiguration -CimSession:$myCimSession`
   * You can test the current configuration of the system by running:
       `Test-DscConfiguration -CimSession:$myCimSession`
   * For more information on creating a CimSession for use with the -CimSession parameter, see: http://technet.microsoft.com/en-us/library/jj590760.aspx


## Building the Desired State Configuration (DSC) Local Configuration Manager and Linux Resource Providers

### Prerequisites

----------

* At least one modern Linux system with:
	* root login capability
	* These build tools:
		* GNU Make
		* g++
		* Python version 2.5 or later, the package python-devel
	* Open Management Infrastructure (OMI) 1.0.8. http://theopengroup.org/software/omi
		* pam-devel
		* openssl-devel


### Building and installing the Local Configuration Manager and Linux Resource Providers

----------
1. Extract PSDSC.tar into a directory that you will build it from.
2. Download and extract OMI 1.0.8 into a directory named "omi-1.0.8" in a directory parallel to the LCM and Providers directory.  The directory tree should look something like:
```
   ./configure
   ./LCM
   ./license.txt
   ./omi-1.0.8
   ./omi-1.0.8/agent
   ./omi-1.0.8/base
   ...
```
3. Building
	* Configuring OMI and building
    	* Configure OMI with desired options (refer to OMI documentation for this step).
    		* The default configuration installs to /opt/omi-1.0.8
     		* To use the default configuration, run: `cd omi-1.0.8 && ./configure`
      	* Run: `make`
	* Installing OMI:
      *  Run: `cd omi-1.0.8 && sudo ./output/install`
	* Registering the LCM + nxProviders with OMI:
      *  Run: `sudo make reg`

4. Running OMI
	* On the Linux system, run omiserver with environment variable OMI_HOME set to OMI's installed directory
    	* Run as root: OMI_HOME=*PATH_TO_INSTALLED_OMI_DIR* *PATH_TO_INSTALLED_OMI_DIR*/bin/omiserver
      	* The default installation for OMI 1.0.8 is "/opt/omi-1.0.8".  Thus, for default installations, part 4Ai above becomes:
            `OMI_HOME=/opt/omi-1.0.8 /opt/omi-1.0.8/bin/omiserver`
   		* Note: In order to run following reboots, it is recommended to configure OMI as a System-V, Upstart, or SystemD daemon 

## Building and using DSC and OMI from source

DSC and OMI can also be built together entirely from source in a self-contained directory. This is useful primarily for developers.

```sh
# Clone DSC source
git clone https://github.com/Microsoft/PowerShell-DSC-for-Linux.git
cd PowerShell-DSC-for-Linux

# Place the OMI source where DSC expects it
# Alternatively clone from Git and symlink to omi/Unix
wget https://collaboration.opengroup.org/omi/documents/33715/omi-1.0.8.tar.gz
tar xf omi-1.0.8.tar.gz

# Build OMI in developer mode
cd omi-1.0.8
./configure --dev
make -j
cd ..

# Build DSC in developer mode
./configure --no-rpm --no-dpkg --local
make -j
make reg

# Start the OMI server
./omi-1.0.8/output/bin/omiserver
```


