.. _troubleshooting:

疑难排解
===============

一般来说，当你遇到问题时，一定要确保这一点，你使用的是`最新版本
<http://www.orthanc-server.com/download.php>`__ 的Orthanc.

还可以在 `Orthanc用户讨论组
<https://groups.google.com/forum/#!forum/orthanc-users>`__上进行搜索, 
或在最新的Orthanc Book（在本页顶部有一个搜索框）上搜索。你的问题可能已经讨论过了。

开始
-------
* If **Orthanc fails to start** with the error "**The TCP port of the DICOM
  server is privileged or already in use**", this means another software is
  already using the port Orthanc is trying to use.  Usually, this means
  that an other instance of Orthanc is running.  However, note that, by default, 
  Orthanc uses port 4242 which might also be used by other software like
  a `Juniper VPN client <https://www.file.net/process/dsncservice.exe.html>`__.
  To determine which other process is using the port: 

  On Windows, you may use the `Resource Monitor <https://en.wikipedia.org/wiki/Resource_Monitor>`__.
  In the `Network` tab, check the `Listening Ports`.  

  On Linux, you may use this command line: ``sudo ss --tcp --listen --numeric --processes``.

  Starting with version 1.3.0, the check at Orthanc startup is more robust
  (it also checks for UDP socket using the same port) and Orthanc 1.3.0 might 
  display error messages that where not displayed by previous versions.

Orthanc Explorer
----------------

* **I cannot login to Orthanc Explorer**: For security reasons, access
  to Orthanc from remote hosts is disabled by default. Only the
  localhost is allowed to access Orthanc. You have to set the
  ``RemoteAccessAllowed`` option in the :ref:`configuration file
  <configuration>` to ``true``. It is then strongly advised to set
  ``AuthenticationEnabled`` to ``true`` and to add a user to the
  ``RegisteredUsers`` option, also in the configuration file.


性能问题
------------------

* **Run-time debug assertions**: If performance is important to you,
  make sure to add the option ``-DCMAKE_BUILD_TYPE=Release`` when
  invoking ``cmake`` while :ref:`compiling Orthanc
  <compiling>`. Indeed, by default, `run-time debug assertions
  <https://en.wikipedia.org/wiki/Assertion_(software_development)#Assertions_for_run-time_checking>`_
  are enabled, which can seriously impact performance, especially if
  your Orthanc server stores a lot of DICOM instances.

  Note that the `official Docker images
  <https://github.com/jodogne/OrthancDocker>`__ of Orthanc <= 1.0.0
  were not compiled in ``Release`` mode. As a consequence, to improve
  performance, make sure to use either the mainline version of the
  container (run ``docker pull jodogne/orthanc`` to ensure you use the
  most recent version of the mainline), or versions more recent than
  ``jodogne/orthanc:1.1.0``.

* **Orthanc slows down if storing many files**: The default database
  engine that is built in Orthanc is `SQLite
  <https://www.sqlite.org/>`__. As SQLite is above all a lightweight
  database engine, it is not designed to `store very large datasets
  <https://www.sqlite.org/whentouse.html>`__. If you are sure that you
  have properly disabled run-time debug assertions (cf. above), but
  still experience degradation in performance over time, you should
  seriously consider switching to a more scalable database engine. To
  this end, you can notably check out the :ref:`official PostgreSQL
  plugin <postgresql>`.

  As a rule of thumb, the performance of the default SQLite engine
  built in Orthanc should run fine up to about 50,000 DICOM instances.


* **Orthanc Explorer is slow under Windows on the localhost**:

  - Favor ``127.0.0.1`` instead of ``localhost`` when specifying the
    network address of a server. Users have reported that this minor
    change `can massively improve performance
    <https://groups.google.com/d/msg/orthanc-users/tTe28zR0nGk/Lvs0STJLAgAJ>`__
    on Windows. Starting with Orthanc 1.0.1, the samples from the
    source distribution have been adapted in this way.

  - As an alternative, you can disable IPv6 support. This is a
    Windows-specific problem that is discussed `here
    <http://superuser.com/questions/43823/google-chrome-is-slow-to-localhost>`__
    and `here
    <http://stackoverflow.com/questions/1726585/firefox-and-chrome-slow-on-localhost-known-fix-doesnt-work-on-windows-7>`__.

* If you experience **slow DICOM transfers under GNU/Linux**, please
  read the `following bug report
  <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=785400>`__. This
  issue does *not* affect all the versions of GNU/Linux. A patch to
  this issue is shipped with the Orthanc source code. In order to take
  advantage of this patch, you need to statically link Orthanc against
  DCMTK by using the ``-DUSE_SYSTEM_DCMTK=OFF`` flag `when invoking
  CMake
  <https://bitbucket.org/sjodogne/orthanc/src/default/LinuxCompilation.txt>`__.


Windows特有问题
-----------------------

* Under Windows, Orthanc creates the "OrthancStorage" folder, and
  crashes with the error "**SQLite: Unable to open the database**":
  Your directory name is either too long, or it contains special
  characters. Please try and run Orthanc in a folder with a simple
  name such as ``C:\Orthanc``.

* If **Orthanc crashes when handling one large DICOM file**, this most
  probably indicates a memory allocation error. Indeed, the `official
  Windows binaries
  <http://www.orthanc-server.com/download-windows.php>`__ are compiled
  using a 32bit compiler. As a consequence, Orthanc only has access to
  less than 4GB of RAM. If this is an important limitation for you,
  precompiled command-line versions of Orthanc for Windows 64bit are
  available courtesy of `Osimis
  <http://www.osimis.io/en/download.html>`__.
