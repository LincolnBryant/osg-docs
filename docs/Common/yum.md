
YUM Repositories
================

About This Document
-------------------

This document introduces YUM repositories and how OSG uses them.

Repositories
------------

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
---------------------

In [our advice on using
yum](Documentation/Release3.InstallBestPractices) you will learn many
tricks and tips on using yum.

To use the packages in a repository without adding special options to
the yum command the repository must be enabled.

### Install the Yum Repositories required by OSG

The OSG RPMs currently support {{ supportedOs }}

OSG RPMs are distributed via the OSG yum repositories. Some packages
depend on packages distributed via the
[EPEL](http://fedoraproject.org/wiki/EPEL) repositories. So both
repositories must be enabled.

### Install EPEL

Install the EPEL repository, if not already present. **Note:** This
enables EPEL by default. Choose the right version to match your OS
version.

```bash
# EPEL 5 (For RHEL 5, CentOS 5, and SL 5)
$ curl -O https://dl.fedoraproject.org/pub/epel/epel-release-latest-5.noarch.rpm
$ rpm -Uvh epel-release-latest-5.noarch.rpm
# EPEL 6 (For RHEL 6, CentOS 6, and SL 6)
$ rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
# EPEL 7 (For RHEL 7, CentOS 7, and SL 7)
$ rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```
    
**WARNING**: if you have your own mirror or configuration of the EPEL
repository, you **MUST** verify that the OSG repository has a better yum
priority than EPEL ([details](Common/InstallBestPractices#YumPriorities)).
Otherwise, you will have strange dependency resolution (*depsolving*) issues.

### Install the Yum priorities package

For packages that exist in both OSG and EPEL repositories, it is
important to prefer the OSG ones or else OSG software installs may fail.
Installing the Yum priorities package enables the repository priority
system to work.

1.  Choose the correct package name based on your operating
    system’s major version:
    -   For EL 5 systems, use `yum-priorities`
    -   For EL 6 and EL 7 systems, use `yum-plugin-priorities`

2.  Install the Yum priorities package:
    ```
    yum install *PACKAGE*
    ```
    Replace *`PACKAGE`* with the package name
    from the previous step.
3.  Ensure that `/etc/yum.conf` has the following line in the
    `[main]` section (particularly when using ROCKS), thereby enabling
    Yum plugins, including the priorities one:

     ```
     plugins=1
     ```
     
    **NOTE**: If you do not have a
    required key you can force the installation using
    `--nogpgcheck=; e.g., =yum install --nogpgcheck yum-priorities`.

### Install OSG Repositories

1. If you are upgrading from OSG 3.1 (or 3.2) to OSG 3.2
   (or 3.3), remove the old OSG repository definition files and clean the
   Yum cache:

   ```
   # yum clean all
   # rpm -e osg-release
   ```
   
   This step ensures that local changes to `*.repo` files will not
   block the installation of the new OSG repositories. After this step,
   `*.repo` files that have been changed will exist in `/etc/yum.repos.d/`
   with the `*.rpmsave` extension. After installing the new OSG
   repositories (the next step) you may want to apply any changes made in
   the `*.rpmsave` files to the new `*.repo` files.
2. Install the OSG repositories using one of the following methods
depending on your EL version:
    1. For EL versions greater than EL5, install the files directly from
`repo.grid.iu`:

        ```
        rpm -Uvh URL
        ```
        
        Where `URL` is one of the following:
    
        | Series      | EL6 URL (for RHEL 6, CentOS 6, or SL 6) | EL7 URL (for RHEL 7, CentOS 7, or SL 7) |
        |----------   | ----------------------------------------| --------------------------------------- | 
        | **OSG 3.2** | `https://repo.grid.iu.edu/osg/3.2/osg-3.2-el6-release-latest.rpm`  | N/A
        | **OSG 3.3** | `https://repo.grid.iu.edu/osg/3.3/osg-3.3-el6-release-latest.rpm`  | `https://repo.grid.iu.edu/osg/3.3/osg-3.3-el7-release-latest.rpm` |

    2. For EL5, download the repo file and install it using the following:
        ```
        # curl -O https://repo.grid.iu.edu/osg/3.2/osg-3.2-el5-release-latest.rpm
        # rpm -Uvh osg-3.2-el5-release-latest.rpm
        ```

Priorities
==========

<span class="twiki-macro NOTE"></span> Make sure you installed the Yum
priorities plugin, as described above. Not doing so is a common mistake
that causes failed installations.

The only OSG repository enabled by default is the release one. If you
want to enable another one, such as `osg-testing`, then edit its file
(e.g. `/etc/yum.repos.d/osg-testing.repo`) and change the enabled option
from 0 to 1:

```
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

If you have your own mirror or
configuration of the EPEL repository, you **MUST** verify that the OSG
repository has a better yum priority than EPEL. Otherwise, you will have
strange dependency resolution issues.

References
==========

-   [Basic use of Yum](Common/YumRpmBasics)
-   [Best practices in using Yum](Common/InstallBestPractices)
