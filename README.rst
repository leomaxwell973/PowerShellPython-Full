**PowerShellPython**
This fork features a modified ``subprocess.py`` (``subprocess.run``) that prefixes code to replace core shell operations from ``cmd.exe`` with ``PowerShell``, targeting commands like **ninja, cmake, cl/msvc, link, etc.** The goal is to **bypass cmd.exe issues** by using PowerShell for Python execution.

**Use / Goals:**

**Bypass CMD issues causing build/install fails.**

- To fix CMD length issues with typical cmd.exe builds and installs.

- If you run into CMD line length exceeded (especially with link.exe), this will likely fix it.

**Bring seamless pip ecosystem back to Windows.**

- To fix some pip usage with certain large projects, mainly and tested are Xformers (with extensions) and flash-attn.

- If you have pip install issues with these, this may fix it, though pip issues vary.

**Make Windows more viable and functionality less dependent on Linux VM-ware or other invasive workarounds.**

- Via the above, and give An alternative shell for workarounds, troubleshooting and debugging.

**Features**

- **Portable & Future-Proof** – No additional imports; designed for maximum compatibility with minimal invasiveness.
- \*Modification Limited to\* ``subprocess.run`` – The rest of ``subprocess.py`` remains untouched.
- **Works with Standard Build Toolchains** – Requires the usual dependencies: MSVC, C++, CL, VC (Visual Studio), CMake (Windows version, *not Python’s*), Ninja (Windows version, not Python’s), CUDA, etc.
- **Fixes vcvarsall.bat Conflicts** – Uses a **custom call** to initialize MSVC without interference from PowerShell prefixing.
- **Supports Different PowerShell Versions** – While tested on **PowerShell v1.0**, it should work with other versions based on pathing/env priority.
- **Validated on Both Native & VM Environments** – Tested with **Python 3.10.6, CUDA 12.1, PyTorch 2.5.1+cu121, Xformers v0.0.29.post1, and Flash-Attn v2.7.4.post1**.
- \*Works with Plain\* ``pip install`` – No need for manual builds or Git repo installations.

**Recommended Fixes & Enhancements**

**Optional: PowerShell Helper Script (ver.ps1)**

This **optional** script improves **verbose logging compatibility**. Place it in ``system32`` or another accessible location and **enable execution** via:

```powershell
Set-ExecutionPolicy Unrestricted
```

**ver.ps1:**

```powershell
$os = Get-CimInstance -ClassName Win32_OperatingSystem
Write-Output ""
Write-Output "Microsoft Windows [Version $($os.Version)]"
```

**Highly Recommended**: Setuptools Fix ( ``build.py`` Patch)

Issue in ``build.py`` involving ``+ plat_specifier`` can cause Windows pip install failures (e.g., Xformers). Remove the lines to resolve it:


``setuptools/_distutils/command/build.py``:
```python
if self.build_purelib is None:
   self.build_purelib = os.path.join(self.build_base, 'lib')  # <<< Remove + plat_specifier
if self.build_platlib is None:
   self.build_platlib = os.path.join(self.build_base, 'lib')  # <<< Remove + plat_specifier
...
self.build_temp = os.path.join(self.build_base, 'temp')  # <<< Remove + plat_specifier
```

A **scripted auto-fix** is included in PowerShellPython but is **commented out by default**.

**Installation & Usage**
1. In this forked version, its all setup and ready to go.
   1. Note: Renamed original subprocess as subprocess_original.py so user may switch between them.
2. Apply the ``build.py`` patch if needed
3. (Optional) Add ``ver.ps1`` to improve log handling
**NOTE:** You do not actually need to run from powershell at all, you can run cmd like normal, python will just be calling powershell itself from the backend.

**Conclusion**

PowerShellPython aims to make **Windows AI development smoother** by replacing ``cmd.exe`` with **PowerShell** where possible. While not a universal fix, it has shown **significant improvements in AI package installations and builds**, reducing cmd-related failures.

This is a work-in-progress—feedback is welcome!

If you'd like to show appreciation (donate) for this work:
https://buymeacoffee.com/leomaxwell


=========================================================================================================

This is Python version 3.14.0 alpha 4
=====================================

.. image:: https://github.com/python/cpython/actions/workflows/build.yml/badge.svg?branch=main&event=push
   :alt: CPython build status on GitHub Actions
   :target: https://github.com/python/cpython/actions

.. image:: https://dev.azure.com/python/cpython/_apis/build/status/Azure%20Pipelines%20CI?branchName=main
   :alt: CPython build status on Azure DevOps
   :target: https://dev.azure.com/python/cpython/_build/latest?definitionId=4&branchName=main

.. image:: https://img.shields.io/badge/discourse-join_chat-brightgreen.svg
   :alt: Python Discourse chat
   :target: https://discuss.python.org/


Copyright © 2001 Python Software Foundation.  All rights reserved.

See the end of this file for further copyright and license information.

.. contents::

General Information
-------------------

- Website: https://www.python.org
- Source code: https://github.com/python/cpython
- Issue tracker: https://github.com/python/cpython/issues
- Documentation: https://docs.python.org
- Developer's Guide: https://devguide.python.org/

Contributing to CPython
-----------------------

For more complete instructions on contributing to CPython development,
see the `Developer Guide`_.

.. _Developer Guide: https://devguide.python.org/

Using Python
------------

Installable Python kits, and information about using Python, are available at
`python.org`_.

.. _python.org: https://www.python.org/

Build Instructions
------------------

On Unix, Linux, BSD, macOS, and Cygwin::

    ./configure
    make
    make test
    sudo make install

This will install Python as ``python3``.

You can pass many options to the configure script; run ``./configure --help``
to find out more.  On macOS case-insensitive file systems and on Cygwin,
the executable is called ``python.exe``; elsewhere it's just ``python``.

Building a complete Python installation requires the use of various
additional third-party libraries, depending on your build platform and
configure options.  Not all standard library modules are buildable or
usable on all platforms.  Refer to the
`Install dependencies <https://devguide.python.org/getting-started/setup-building.html#build-dependencies>`_
section of the `Developer Guide`_ for current detailed information on
dependencies for various Linux distributions and macOS.

On macOS, there are additional configure and build options related
to macOS framework and universal builds.  Refer to `Mac/README.rst
<https://github.com/python/cpython/blob/main/Mac/README.rst>`_.

On Windows, see `PCbuild/readme.txt
<https://github.com/python/cpython/blob/main/PCbuild/readme.txt>`_.

To build Windows installer, see `Tools/msi/README.txt
<https://github.com/python/cpython/blob/main/Tools/msi/README.txt>`_.

If you wish, you can create a subdirectory and invoke configure from there.
For example::

    mkdir debug
    cd debug
    ../configure --with-pydebug
    make
    make test

(This will fail if you *also* built at the top-level directory.  You should do
a ``make clean`` at the top-level first.)

To get an optimized build of Python, ``configure --enable-optimizations``
before you run ``make``.  This sets the default make targets up to enable
Profile Guided Optimization (PGO) and may be used to auto-enable Link Time
Optimization (LTO) on some platforms.  For more details, see the sections
below.

Profile Guided Optimization
^^^^^^^^^^^^^^^^^^^^^^^^^^^

PGO takes advantage of recent versions of the GCC or Clang compilers.  If used,
either via ``configure --enable-optimizations`` or by manually running
``make profile-opt`` regardless of configure flags, the optimized build
process will perform the following steps:

The entire Python directory is cleaned of temporary files that may have
resulted from a previous compilation.

An instrumented version of the interpreter is built, using suitable compiler
flags for each flavor. Note that this is just an intermediary step.  The
binary resulting from this step is not good for real-life workloads as it has
profiling instructions embedded inside.

After the instrumented interpreter is built, the Makefile will run a training
workload.  This is necessary in order to profile the interpreter's execution.
Note also that any output, both stdout and stderr, that may appear at this step
is suppressed.

The final step is to build the actual interpreter, using the information
collected from the instrumented one.  The end result will be a Python binary
that is optimized; suitable for distribution or production installation.


Link Time Optimization
^^^^^^^^^^^^^^^^^^^^^^

Enabled via configure's ``--with-lto`` flag.  LTO takes advantage of the
ability of recent compiler toolchains to optimize across the otherwise
arbitrary ``.o`` file boundary when building final executables or shared
libraries for additional performance gains.


What's New
----------

We have a comprehensive overview of the changes in the `What's New in Python
3.14 <https://docs.python.org/3.14/whatsnew/3.14.html>`_ document.  For a more
detailed change log, read `Misc/NEWS
<https://github.com/python/cpython/tree/main/Misc/NEWS.d>`_, but a full
accounting of changes can only be gleaned from the `commit history
<https://github.com/python/cpython/commits/main>`_.

If you want to install multiple versions of Python, see the section below
entitled "Installing multiple versions".


Documentation
-------------

`Documentation for Python 3.14 <https://docs.python.org/3.14/>`_ is online,
updated daily.

It can also be downloaded in many formats for faster access.  The documentation
is downloadable in HTML, PDF, and reStructuredText formats; the latter version
is primarily for documentation authors, translators, and people with special
formatting requirements.

For information about building Python's documentation, refer to `Doc/README.rst
<https://github.com/python/cpython/blob/main/Doc/README.rst>`_.


Testing
-------

To test the interpreter, type ``make test`` in the top-level directory.  The
test set produces some output.  You can generally ignore the messages about
skipped tests due to optional features which can't be imported.  If a message
is printed about a failed test or a traceback or core dump is produced,
something is wrong.

By default, tests are prevented from overusing resources like disk space and
memory.  To enable these tests, run ``make buildbottest``.

If any tests fail, you can re-run the failing test(s) in verbose mode.  For
example, if ``test_os`` and ``test_gdb`` failed, you can run::

    make test TESTOPTS="-v test_os test_gdb"

If the failure persists and appears to be a problem with Python rather than
your environment, you can `file a bug report
<https://github.com/python/cpython/issues>`_ and include relevant output from
that command to show the issue.

See `Running & Writing Tests <https://devguide.python.org/testing/run-write-tests.html>`_
for more on running tests.

Installing multiple versions
----------------------------

On Unix and Mac systems if you intend to install multiple versions of Python
using the same installation prefix (``--prefix`` argument to the configure
script) you must take care that your primary python executable is not
overwritten by the installation of a different version.  All files and
directories installed using ``make altinstall`` contain the major and minor
version and can thus live side-by-side.  ``make install`` also creates
``${prefix}/bin/python3`` which refers to ``${prefix}/bin/python3.X``.  If you
intend to install multiple versions using the same prefix you must decide which
version (if any) is your "primary" version.  Install that version using
``make install``.  Install all other versions using ``make altinstall``.

For example, if you want to install Python 2.7, 3.6, and 3.14 with 3.14 being the
primary version, you would execute ``make install`` in your 3.14 build directory
and ``make altinstall`` in the others.


Release Schedule
----------------

See `PEP 745 <https://peps.python.org/pep-0745/>`__ for Python 3.14 release details.


Copyright and License Information
---------------------------------


Copyright © 2001 Python Software Foundation.  All rights reserved.

Copyright © 2000 BeOpen.com.  All rights reserved.

Copyright © 1995-2001 Corporation for National Research Initiatives.  All
rights reserved.

Copyright © 1991-1995 Stichting Mathematisch Centrum.  All rights reserved.

See the `LICENSE <https://github.com/python/cpython/blob/main/LICENSE>`_ for
information on the history of this software, terms & conditions for usage, and a
DISCLAIMER OF ALL WARRANTIES.

This Python distribution contains *no* GNU General Public License (GPL) code,
so it may be used in proprietary projects.  There are interfaces to some GNU
code but these are entirely optional.

All trademarks referenced herein are property of their respective holders.
