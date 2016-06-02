Configuring a Site to Use the Worker Node Client Software From OASIS
====================================================================

<span class="twiki-macro TOC"></span>

About This Guide
----------------

The *OSG Worker Node Client* is a collection of software components that is expected to be added to every worker node that can run OSG jobs. It provides a common environment and a minimal set of common tools that all OSG jobs can expect to use. See the [reference section](#ReferenceSection) below for contents of the Worker Node Client.

It is possible to install the Worker Node Client software in a variety of ways, depending on what works best for distributing and managing software at your site:

-   Use the Worker Node Client software from OASIS (this guide) - useful when [OASIS](NavTechOASIS) is already mounted on your worker nodes
-   [Install using RPMs and Yum](InstallWNClient) - useful when managing your worker nodes with a tool (e.g., Puppet, Chef) that can automate RPM installs
-   [Install using a tarball](InstallWNClientTarball) - useful when installing onto a shared filesystem for distribution to worker nodes

This document is intended to guide system administrators through the process of configuring a site to make the Worker Node Client software available from OASIS.

Before Starting
---------------

As with all OSG software installations, there are some one-time (per host) steps to prepare in advance:

-   Ensure the host has [a supported operating system](SupportedOperatingSystems)

Also note that, once configured to use OASIS, each use of Worker Node Client software will cause that software and its associated files to be downloaded from a CVMFS server or your local cache thereof. This may result in extra network activity, especially if Worker Node Client tools are used heavily.

Configuring Your Site to Use the Worker Node Client From OASIS
--------------------------------------------------------------

Below are the one-time steps that you must perform to configure your site to use the Worker Node Client software from OASIS.

\<ol\> \<li\>\<p\>On every worker node, [install and configure OASIS](InstallCvmfs)\</p\>\</li\> \<li\> \<p\>Determine the OASIS path to the Worker Node Client software for your worker nodes:\</p\> <span class="twiki-macro TABLE" sort="off"></span>

| Worker Node OS | Use…                                                                                 |
|:---------------|:-------------------------------------------------------------------------------------|
| EL 5           | `/cvmfs/oasis.opensciencegrid.org/osg-software/osg-wn-client/3.2/current/el5-x86_64` |
| EL 6           | `/cvmfs/oasis.opensciencegrid.org/osg-software/osg-wn-client/3.2/current/el6-x86_64` |

\</li\> \<li\> \<p\>On the CE, in the `/etc/osg/config.d/10-storage.ini` file, set the `grid_dir` configuration setting to the path from the previous step\</p\> \<p\>For more information, see the [OSG environment variables reference page](EnvironmentVariables) and the [CE configuration instructions](IniConfigurationOptions#Storage).\</p\> \</li\> \<li\> \<p\>Once you finish making changes to configuration files on your CE, validate, fix, and apply the configuration:\</p\> \<pre class=“rootscreen”\><span class="twiki-macro UCL_PROMPT_ROOT"></span> osg-configure -v <span class="twiki-macro UCL_PROMPT_ROOT"></span> osg-configure -c\</pre\> \</li\> \</ol\>

Validing the Worker Node Client
-------------------------------

To verify functionality of the worker node client, you will need to submit a test job against your CE and verify the job’s output.

1.  Submit a job that executes the `env` command (e.g. Run [condor\_ce\_trace](TroubleshootingHTCondorCE#condor_ce_trace) with the `-d` flag from your HTCondor CE)
2.  Verify that the value of `OSG_GRID` is set to the directory of your WN Client installation

### Manually Using the Worker Node Client From OASIS

If you must log onto a worker node and use the Worker Node Client software directly during your login session, use the steps below to set up access to the software:

\<ol\> \<li\> \<p\>Determine the OASIS path to the Worker Node Client software for your worker nodes:\</p\> <span class="twiki-macro TABLE" sort="off"></span>

| Worker Node OS | Use…                                                                                          |
|:---------------|:----------------------------------------------------------------------------------------------|
| EL 5           | `/cvmfs/oasis.opensciencegrid.org/osg-software/osg-wn-client/3.2/current/el5-x86_64/setup.sh` |
| EL 6           | `/cvmfs/oasis.opensciencegrid.org/osg-software/osg-wn-client/3.2/current/el6-x86_64/setup.sh` |

\</li\> \<li\> \<p\>Set up access to the Worker Node Client software for the duration of your login session:\</p\> \<pre class=“screen”\><span class="twiki-macro UCL_PROMPT"></span> source *PATH*\</pre\> \<p\>where `<em>PATH</em>` is from the previous step. \</li\> \</ol\>

Troubleshooting
---------------

Some possible issues that may come up:

\* A missing softlink to the CA certs directory. To check this, run:\\ \<pre class=“screen‘\>ls -l /cvmfs/oasis.opensciencegrid.org/mis/osg-wn-client/3.2/current/el5-x86\_64/etc/grid-security/\</pre\>\\ \<p\>and check that `certificates` is linked to somewhere. The fix is to yum update the oasis-config package to version 4. A known workaround is to run:\</p\>\\ \<pre class=“screen”\>export X509\_CERT\_DIR=/cvmfs/oasis.opensciencegrid.org/mis/certificates\</pre\>\\ \<p\>before any commands.\</p\> \* OpenJDK 7 must be installed from RPMs before using software that needs Java (e.g., `srm-ls`) from OASIS. There are some instructions [here](Documentation.Release3.InstallSoftwareWithOpenJDK7). If you are running under an EL5 distribution, java programs may produce an error like this:\\ \<pre class=’screen’\>Exception in thread”main" java.lang.NoClassDefFoundError: org/globus/gsi/OpenSSLKey\</pre\> This shouldn‘t happen under EL6 distributions. The (unofficial) workaround is to run the following as root:\\ \<pre class=’screen’\><span class="twiki-macro UCL_PROMPT_ROOT"></span> mkdir /usr/share/java-1.7.0 <span class="twiki-macro UCL_PROMPT_ROOT"></span> mkdir /usr/lib/java-1.7.0\</pre\>\\ This problem is also mentioned [here](SoftwareTeam.Java6MigrationDetails).

How to get Help?
----------------

To get assistance please use this [Help Procedure](HelpProcedure).

\#ReferenceSection

Reference
---------

Please see the documentation on using [yum and RPM](Documentation/Release3.YumRpmBasics), [the best practices](Documentation/Release3.InstallBestPractices) for using yum to install software, and using [yum repositories](Documentation/Release3.YumRepositories).

