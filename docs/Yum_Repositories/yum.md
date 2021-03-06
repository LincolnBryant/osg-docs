-   **YUM Repositories**
-   About This Document
-   Repositories
-   Enabling Repositories
-   Priorities
-   References
-   Comments

**YUM Repositories** {#yum-repositories .unnumbered}
====================

<span class="twiki-macro DOC_STATUS_TABLE"></span> <span
class="twiki-macro TOC"></span>

About This Document
===================

This document introduces YUM repositories and how OSG uses them.

Repositories
============

OSG hosts four public-facing repositories at
[repo.grid.iu.edu](http://repo.grid.iu.edu/):

-   **release**: This repository contains software that we are willing
    to support and can be used by the general community.
-   **contrib**: RPMs contributed from outside the OSG.
-   **testing**: This repository contains software ready for testing. If
    you install packages from here, they may be buggy, but we will
    provide limited assistance in providing a migration path to a fixed
    version.
-   **development**: This repository is the bleeding edge. Installing
    from this repository may cause the host to stop functioning, and we
    will not assist in undoing any damage.

OSG’s RPM packages rely also on external packages provided by supported
OSes and EPEL. You must have the following repositories available and
enabled:

-   your OS repositories (SL 5/6/7, CentOS 5/6/7, or RHEL 5/6/7
    repositories)
-   EPEL repositories
-   the OSG repositories you’d like to use

If one of these repositories is missing you may have missing
dependencies.

<span class="twiki-macro WARNING"></span> We did not test other
repositories. If you use packages from other repositories, like
`jpackage`, `dag`, or `rpmforge`, you may encounter problems.

Enabling Repositories
=====================

In [our advice on using
yum](Documentation/Release3.InstallBestPractices) you will learn many
tricks and tips on using yum.

To use the packages in a repository without adding special options to
the yum command the repository must be enabled.

<span class="twiki-macro STARTSECTION">OSGRepoBrief</span>

—%SHIFT%+ Install the Yum Repositories required by OSG

The OSG RPMs currently support <span
class="twiki-macro SUPPORTED_OS"></span>.

OSG RPMs are distributed via the OSG yum repositories. Some packages
depend on packages distributed via the
[EPEL](http://fedoraproject.org/wiki/EPEL) repositories. So both
repositories must be enabled.

—%SHIFT%++ Install EPEL

-   Install the EPEL repository, if not already present. **Note:** This
    enables EPEL by default. Choose the right version to match your OS
    version. \<pre class=“rootscreen”\>

%RED%\# EPEL 5 (For RHEL 5, CentOS 5, and SL 5) <span
class="twiki-macro ENDCOLOR"></span> <span
class="twiki-macro UCL_PROMPT_ROOT"></span> curl -O
<https://dl.fedoraproject.org/pub/epel/epel-release-latest-5.noarch.rpm>
<span class="twiki-macro UCL_PROMPT_ROOT"></span> rpm -Uvh
epel-release-latest-5.noarch.rpm %RED%\# EPEL 6 (For RHEL 6, CentOS 6,
and SL 6) <span class="twiki-macro ENDCOLOR"></span> <span
class="twiki-macro UCL_PROMPT_ROOT"></span> rpm -Uvh
<https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm>
%RED%\# EPEL 7 (For RHEL 7, CentOS 7, and SL 7) <span
class="twiki-macro ENDCOLOR"></span> <span
class="twiki-macro UCL_PROMPT_ROOT"></span> rpm -Uvh
<https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm>\</pre\>
**WARNING**: if you have your own mirror or configuration of the EPEL
repository, you **MUST** verify that the OSG repository has a better yum
priority than EPEL
([details](Documentation.Release3.InstallBestPractices#YumPriorities)).
Otherwise, you will have strange dependency resolution (*depsolving*)
issues.

—%SHIFT%++ Install the Yum priorities package

For packages that exist in both OSG and EPEL repositories, it is
important to prefer the OSG ones or else OSG software installs may fail.
Installing the Yum priorities package enables the repository priority
system to work.

1.  \<p\>Choose the correct package name based on your operating
    system’s major version:\</p\>
    -   For EL 5 systems, use `yum-priorities`
    -   For EL 6 and EL 7 systems, use `yum-plugin-priorities`

2.  \<p\>Install the Yum priorities package:\</p\>\\ \<pre
    class=“rootscreen”\><span
    class="twiki-macro UCL_PROMPT_ROOT"></span> yum install
    *PACKAGE*\</pre\>\\ \<p\>Replace *`PACKAGE`* with the package name
    from the previous step.\</p\>
3.  \<p\>Ensure that `/etc/yum.conf` has the following line in the
    `[main]` section (particularly when using ROCKS), thereby enabling
    Yum plugins, including the priorities one:\</p\>\\ \<pre
    class=“file”\>plugins=1\</pre\> **NOTE**: If you do not have a
    required key you can force the installation using
    `--nogpgcheck=; e.g., =yum install --nogpgcheck yum-priorities`.

—%SHIFT%++ Install OSG Repositories

\<ol\> \<li\> \<p\>If you are upgrading from OSG 3.1 (or 3.2) to OSG 3.2
(or 3.3), remove the old OSG repository definition files and clean the
Yum cache:\</p\>\\ \<pre class=“rootscreen”\><span
class="twiki-macro UCL_PROMPT_ROOT"></span> yum clean all <span
class="twiki-macro UCL_PROMPT_ROOT"></span> rpm -e osg-release\</pre\>\\
\<p\>This step ensures that local changes to `*.repo` files will not
block the installation of the new OSG repositories. After this step,
`*.repo` files that have been changed will exist in `/etc/yum.repos.d/`
with the `*.rpmsave` extension. After installing the new OSG
repositories (the next step) you may want to apply any changes made in
the `*.rpmsave` files to the new `*.repo` files.\</p\> \</li\> \<li\>
\<p\>Install the OSG repositories using one of the following methods
depending on your EL version:\</p\>\\ \<ol\> \<li\> \<p\>For EL versions
greater than EL5, install the files directly from
`repo.grid.iu`:\</p\>\\ \<pre class=“rootscreen”\><span
class="twiki-macro UCL_PROMPT_ROOT"></span> rpm -Uvh
%RED%URL%ENDCOLOR%\</pre\>\\ \<p\>Where `%RED%URL%ENDCOLOR%` is one of
the following:\</p\>\\ <span class="twiki-macro TABLE"
SORT="off"></span>

  Series                      EL6 URL (for RHEL 6, CentOS 6, or SL 6)                             EL7 URL (for RHEL 7, CentOS 7, or SL 7)
  ------------- ------------------------------------------------------------------- -------------------------------------------------------------------
  **OSG 3.2**    `https://repo.grid.iu.edu/osg/3.2/osg-3.2-el6-release-latest.rpm`                                  N/A
  **OSG 3.3**    `https://repo.grid.iu.edu/osg/3.3/osg-3.3-el6-release-latest.rpm`   `https://repo.grid.iu.edu/osg/3.3/osg-3.3-el7-release-latest.rpm`

\</li\> \<li\> \<p\>For EL5, download the repo file and install it using
the following:\</p\> \<pre class=“rootscreen”\><span
class="twiki-macro UCL_PROMPT_ROOT"></span> curl -O
<https://repo.grid.iu.edu/osg/3.2/osg-3.2-el5-release-latest.rpm> <span
class="twiki-macro UCL_PROMPT_ROOT"></span> rpm -Uvh
osg-3.2-el5-release-latest.rpm\</pre\> \</li\> \</ol\> \</li\> \</ol\>

<span class="twiki-macro IF"
then="For more details, please see our [[Documentation.Release3.YumRepositories][yum repository documentation]]."
else="&quot;&quot;">\$ TOPIC != 'YumRepositories'</span>

<span class="twiki-macro ENDSECTION">OSGRepoBrief</span>

Priorities
==========

<span class="twiki-macro NOTE"></span> Make sure you installed the Yum
priorities plugin, as described above. Not doing so is a common mistake
that causes failed installations.

The only OSG repository enabled by default is the release one. If you
want to enable another one, such as `osg-testing`, then edit its file
(e.g. `/etc/yum.repos.d/osg-testing.repo`) and change the enabled option
from 0 to 1:

``` {.file}
[osg-testing]
name=OSG Software for Enterprise Linux 5 - Testing - $basearch
#baseurl=http://repo.grid.iu.edu/osg/3.2/el5/testing/$basearch
mirrorlist=http://repo.grid.iu.edu/mirror/osg/3.2/el5/testing/$basearch
failovermethod=priority
priority=98
enabled=%RED%1%ENDCOLOR%
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-OSG
```

<span class="twiki-macro WARNING"></span> if you have your own mirror or
configuration of the EPEL repository, you **MUST** verify that the OSG
repository has a better yum priority than EPEL. Otherwise, you will have
strange dependency resolution issues.

References
==========

-   [Basic use of Yum](YumRpmBasics)
-   [Best practices in using Yum](InstallBestPractices)

Comments
========

  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------- ---------------------
  When will RHEL/Centos/SL 7 be supported?                                                                                                                                                                                                                                                 Main.MickTimony   11 Dec 2014 - 19:32
  OSG 3.1 will be unsupported after April 2015:\<br /\>[http://osggoc.blogspot.com/2015/02/announcing-osg-software-versions-3220.html\<br](http://osggoc.blogspot.com/2015/02/announcing-osg-software-versions-3220.html<br) /\>\<br /\>It would be nice if the docs would reflect this.   Main.MickTimony   11 Feb 2015 - 17:15
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------- ---------------------

<span class="twiki-macro COMMENT" type="tableappend"></span>
