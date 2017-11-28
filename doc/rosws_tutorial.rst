rosws Tutorial
==============

In this tutorial we will set up a workspace using different SCM
providers and introduce the ``rosws`` commands to run SCM 
configuration operations in this workspace.

In the context of rosws, a workspace is a set of folders on 
your harddisk that are listed in a special file named ``.rosinstall``.
Also this file stores for each folder whether this folder is managed
under some version control system (subversion, mercurial, git, bazaar).
This allows you to deal with many common version control operations
using a single interface.

rosws also generates a file called setup.sh. This adds all folders
listed in the .rosinstall to an environment variable called 
ROS_PACKAGE_PATH. This variable can the be used by other tools to 
e.g. find folders with libraries you want to compile against.

In you get stuck with a technical problem, you may check out
the answers page: http://answers.ros.org/questions/tags:rosinstall

.. contents:: Contents
   :depth: 3

Prerequisites
-------------

Start by updating / installing ``rosws``, which comes with rosinstall.

*Ubuntu*

On Ubuntu you first need to setup ROS sources: http://wiki.ros.org/fuerte/Installation/Sources

:: 

    $ sudo apt-get install python-rosinstall

If you do not want to add the ROS sources as outlined above you can use ``pip`` 
as outlined below, but know that ``pip`` won't automatically update and overrides 
any verison installed from debian packages. 

*Other Platforms*

On platforms where debian packages are  
You will need the program ``pip``

You can then get the latest rosinstall like this:

::

    $ sudo pip install -U rosinstall

Troubleshoot
~~~~~~~~~~~~

If you had rosinstall series 0.5 installed, you may encounter conflicts like::

  ImportError: No module named rosinstall.rosinstall_cli

To fix this, you will have to remove leftover directories from the earlier install, as sadly the python setuptools will not do it for you. On Ubuntu Lucid, as an example::

  $ sudo rm -rf /usr/local/lib/python2.6/dist-packages/rosinstall-0.5*


Initialize a workspace
----------------------

We will create a folder that serves as workspace, which you can 
delete after the tutorial.

.. note:: The following creates a workspace without ROS. This means variables that are important for ROS like ROS_PACKAGE_PATH will not be set. If you want to create a workspace for ROS, follow the tutorial :ref:`rosws_ros_tutorial`.

::

  $ cd /tmp
  $ mkdir rosws_tutorial
  $ cd rosws_tutorial
  $ rosws init
  Writing /tmp/rosws_tutorial/.rosinstall
  (Over-)Writing setup.sh, setup.bash, and setup.zsh in /tmp/rosws_tutorial

  rosws init complete.

  Type 'source ./setup.bash' to change into this environment. 
  Add that source command to the bottom of your ~/.bashrc to set it up every time you log in.

  If you are not using bash please see http://wiki.ros.org/rosinstall/NonBashShells


To see what this command did, just type:

::
  
  $ ls -a
  ./  ../  .rosinstall  setup.bash  setup.sh  setup.zsh

This is all that ``rosws init`` does, it creates these files. If you
ever want to start your workspace over, you can delete all these
files, and that's it. Note that ``rm *`` on linux does not delete 
the ``.rosinstall``, you need to call ``rm .rosinstall`` as well.

We can run ``rosws info`` to see an overview of the environment:

::

  $ rosws info
  workspace: /tmp/rosws_tutorial
  ROS_ROOT: None

As you can see the workspace is empty and we do not have a ros root in
the workspace. For this tutorial, we will use the ROS fuerte release.
Note though that you do not need a ros root to have a valid workspace 
with rosws.

Now is a good time to check out the ``help`` and ``--version`` commands::

  $ rosws help
  $ rosws help init
  $ rosws help info
  $ rosws --version

Remember this help is available if you get in trouble.



Extending your workspace
------------------------

Using rosws is useful for mostly one purpose, using several SCM
repositories, like Subversion, git, Mercurial, Bazaar. So far in 
our workspace we have none such folders, so let's see how we can 
add a few.

For the sake of having an example, let us try a merge in stacks from
the electric ROS distribution. (This will not take long, trust me).
You do not need any ros installation for this step of the tutorial.

::

  $ rosws merge 'http://packages.ros.org/cgi-bin/gen_rosinstall.py?rosdistro=electric&variant=robot&overlay=yes'
     Performing actions: 

     Add new elements:
  nodelet_core,  xacro,  eigen,  bond_core,  pluginlib,  geometry,  robot_model,  assimp,  
  orocos_kinematics_dynamics,  common,  filters,  diagnostics,  bullet,  driver_common,  
  executive_smach,  common_msgs
  
  Overwriting /tmp/rosws/.rosinstall
  
  rosws update complete.
  
  Do not forget to do ...
  $ source /tmp/rosws/setup.sh
  ... in every open terminal.
  Config changed, remember to run rosws update to update the tree
  
What this command did is just adding entries to your .rosinstall, nothing else so far. 
The ``rosws`` tool behaves differently from the rosinstall ``tool`` in that respect.
We can inspect the new config in detail again using the ``rosws info`` command:

::

  $ rosws info
   Localname                  S SCM  Version-Spec                     UID  (Spec) URI  (Spec) (https://...)
   ---------                  - ---- ------------                     ----------- -------------------------
   xacro                      x hg   xacro-1.6.0                                  kforge.ros.org/common/xacro
   executive_smach            x hg   executive_smach-1.0.4                        kforge.ros.org/smach/executive_smach
   robot_model                x hg   robot_model-1.6.4                            kforge.ros.org/robotmodel/robot_model
   assimp                     x hg   assimp-0.1.1                                 kforge.ros.org/robotmodel/assimp
   pluginlib                  x hg   pluginlib-1.6.0                              kforge.ros.org/common/pluginlib
   orocos_kinematics_dynamics x git  orocos_kinematics_dynamics-0.2.3             http://git.mech.kuleuven.be/robotics/orocos_kinematics_dynamics.git
   nodelet_core               x hg   nodelet_core-1.6.2                           kforge.ros.org/common/nodeletcore
   geometry                   x hg   geometry-1.6.1                               kforge.ros.org/geometry/geometry
   bullet                     x hg   bullet-2.76.5                                kforge.ros.org/geometry/bullet
   filters                    x hg   filters-1.6.0                                kforge.ros.org/common/filters
   eigen                      x hg   eigen-1.6.0                                  kforge.ros.org/geometry/eigen
   driver_common              x svn  tags/driver_common-1.2.4                     code.ros.org/svn/ros-pkg/stacks/driver_common/
   diagnostics                x svn  tags/diagnostics-1.6.4                       code.ros.org/svn/ros-pkg/stacks/diagnostics/
   common                     x hg   common-1.6.1                                 kforge.ros.org/common/common
   common_msgs                x svn  tags/common_msgs-1.6.0                       code.ros.org/svn/ros-pkg/stacks/common_msgs/
   bond_core                  x hg   bond_core-1.6.1                              kforge.ros.org/common/bondcore
   /opt/ros/fuerte/stacks                                                         
   /opt/ros/fuerte/share                                                          
   /opt/ros/fuerte/share/ros

So this looks much more fun. For each stack definition that we merged in, we see the SCM provider, the URI, and the version. The column labeled ``S`` gives us the status, an ``x`` means that the folder is missing. That's because we did only merge in the definitions, so far we did not pull the actual stacks. The ``UID (Spec)`` column would show the current revision of each stack, but as we did not check out any, it is empty for all.

.. note:: For svn, rosws, uses the standard layout to reduce table width. So 
  uri = ``https://code.ros.org/svn/ros-pkg/stacks/common_msgs/tags/common_msgs-1.6.0``
  becomes
  uri = ``code.ros.org/svn/ros-pkg/stacks/common_msgs``
  Version-Spec = ``tags/common_msgs-1.6.0``

We can use ``rosws info`` also to compare just a few entries and to look into one entry in detail:

::

  $ rosws info common filters
   Localname S SCM  Version-Spec  UID  (Spec) URI  (Spec) (https://...)
   --------- - ---- ------------  ----------- -------------------------
   filters   x hg   filters-1.6.0             kforge.ros.org/common/filters
   common    x hg   common-1.6.1              kforge.ros.org/common/common

  $ rosws info diagnostics
  Localname:         diagnostics
  Path               /tmp/rosws_tutorial/diagnostics
  Status:            x
  SCM:               svn
  URI:               https://code.ros.org/svn/ros-pkg/stacks/diagnostics/tags/diagnostics-1.6.4
  Current URI:       
  Version-Spec:      
  Spec-Revision:     
  Current-Revision:  
  Other Properties:  []

As you can see, the display changes if we just give one entry, and the
SVN uri is displayed in the original format stored in the
``.rosinstall`` file.

Since we do not want to strain your network connection, we will undo 
the merge. rosws created a ``.rosinstall.bak`` file when we run the 
merge command. You can always undo one change by replacing ``.rosinstall``
with the ``.rosinstall.bak``.

::

  $ mv .rosinstall.bak .rosinstall



Working with Entries
--------------------

To have something small to work with, we will add single ROS stack to our 
workspace for real. The ``set`` command is for manual adding of entries:

::

  $ rosws set common_msgs https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk --svn
       Add element: 
   {'svn': {'local-name': 'common_msgs', 'uri': 'https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk'}}
  Continue(y/n): y    
  Overwriting /tmp/rosws_tutorial/.rosinstall
  Config changed, remember to run 'rosws update common_msgs' to update the folder from svn

The command by default tells you what it thinks you have meant, which
is generally useful if the workspaces become larger. You can just 
confirm.

``rosws info`` again shows the current state of your workspace.

We will go ahead and check this stack out, as it is fairly small it should not take too long.

Pulling entries
~~~~~~~~~~~~~~~

:: 

  $ rosws update common_msgs
  [common_msgs] Installing https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk (None) to /tmp/rosws_tutorial/common_msgs
  [common_msgs] Done.
  $ ls
  common_msgs/  setup.bash  setup.sh  setup.zsh

.. note:: You can also at any time update all your workspace trees just using ``rosws update``

You can now see that the repository was checked out, also using ``rosws info``:

:: 

  $ ls -a
  ./  ../  common_msgs/  .rosinstall  .rosinstall.bak  setup.bash  setup.sh  setup.zsh
  $ rosws info
   Localname                 S SCM  Version-Spec UID  (Spec) URI  (Spec) (https://...)
   ---------                 - ---- ------------ ----------- -------------------------
   common_msgs                 svn  trunk        -r39122     code.ros.org/svn/ros-pkg/stacks/common_msgs/
   ...

You see now the UID (Spec) column contains your current revision. If
you see a different revision number, that is okay.

We can play with that a bit::

  $ svn update common_msgs -r PREV
  ...
  $ rosws info
   Localname                 S SCM  Version-Spec UID  (Spec) URI  (Spec) (https://...)
   ---------                 - ---- ------------ ----------- -------------------------
   common_msgs                 svn  trunk        -r38989     code.ros.org/svn/ros-pkg/stacks/common_msgs/
   ...

You should notice that for you, the revision number should have
changed as well.  We needed use the ``svn`` command here because we
changed the ``common_msgs`` version without changes to the .rosinstall
file.

Setting entry versions:
~~~~~~~~~~~~~~~~~~~~~~~

Let's say you want to stay with one revision for some time, we can specify a revision like this:

::

  $ rosws set common_msgs --version=-r38935
       Change element from: 
   {'svn': {'local-name': 'common_msgs', 'uri': 'https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk'}}
       to
   {'svn': {'local-name': 'common_msgs', 'version': '-r38935', 'uri': 'https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk'}}
  Continue(y/n): y
  Overwriting /tmp/rosws_tutorial/.rosinstall
  Config changed, remember to run 'rosws update common_msgs' to update the folder from svn

Again you see the tool asks you to confirm.

::

  $ rosws info
   Localname                 S SCM  Version-Spec UID  (Spec)       URI  (Spec) (https://...)
   ---------                 - ---- ------------ -----------       -------------------------
   common_msgs               V svn  trunk        -r39028 (-r38935) code.ros.org/svn/ros-pkg/stacks/common_msgs/

What you see now in the output of rosws info in the UID column is two numbers. 
The first is current revision, the second is what is now stated in your .rosinstall.
Also notice the Status column ``S`` now shows a ``V``. This indicates some 
specification mismatch, and it is the quick way to see whether your filesystem 
matches what is given in your .rosinstall.

To clean this up, you could now set the value to the actual value, or
run ``rosws update`` to update to the version in the spec, or remove
the version spec. We will do the latter using the set command::

  $ rosws set common_msgs --version=""
       Change element from: 
   {'svn': {'local-name': 'common_msgs', 'version': '-r38935', 'uri': 'https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk'}}
       to
   {'svn': {'local-name': 'common_msgs', 'version': '', 'uri': 'https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk'}}
  Continue(y/n): y
  Overwriting /tmp/rosws_tutorial/.rosinstall
  Config changed, remember to run 'rosws update common_msgs' to update the folder from svn

Diff and Status:
~~~~~~~~~~~~~~~~

The rosws command also allows to produce diff and status output for
the supported SCMs. To see that, we need to change a file.

::

  $ echo '# foo' >> common_msgs/CMakeLists.txt 

  $ rosws status common_msgs
  M       common_msgs/CMakeLists.txt

  $ rosws diff common_msgs
  Index: common_msgs/CMakeLists.txt
  ===================================================================
  --- common_msgs/CMakeLists.txt  (revision 39028)
  +++ common_msgs/CMakeLists.txt  (working copy)
  @@ -26,3 +26,4 @@
 
   install(FILES stack.xml stack.yaml
         DESTINATION share/common_msgs)
  +# foo
  
  $ rosws info
   Localname                 S SCM  Version-Spec UID  (Spec) URI  (Spec) (https://...)
   ---------                 - ---- ------------ ----------- -------------------------
   common_msgs               M svn  trunk        -r39028     code.ros.org/svn/ros-pkg/stacks/common_msgs

You can pass multiple folder names to rosws diff and status, or pass
none, and it will accumulate the outputs for all trees. Also note how
the status column in ``rosws info`` now shows an ``M`` for modified.

Detaching Entries
~~~~~~~~~~~~~~~~~

Detached entries have no SCM information associated, running update
with them will not affect them. You can detach our common_msgs entry 
using the set command::

  $ rosws set common_msgs --detached
       Change element from: 
   {'svn': {'local-name': 'common_msgs', 'uri': 'https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk'}}
       to
   {'other': {'local-name': 'common_msgs'}}
  Continue(y/n): y
  Overwriting /tmp/rosws_tutorial/.rosinstall


Removing entries
~~~~~~~~~~~~~~~~

Finally you may sometimes want to remove entries from your workspace,
there is the ``remove`` command for that::

  $ rosws remove common_msgs 
  Overwriting /tmp/rosws_tutorial/.rosinstall
  Removed entries ['common_msgs']


Updating many
~~~~~~~~~~~~~

Doing several SCM actions at a time can be very time-consuming, and we
can gain a lot of time by doing the work in parallel. ``rosws`` allows to 
checkout or update entries in parallel as well:

Here is how to generate a snapshot for one or more entries. 
We first extend our workspace again::

  $ rosws set geometry https://kforge.ros.org/geometry/geometry --hg --version=geometry-1.6.1
  $ rosws set common_msgs https://code.ros.org/svn/ros-pkg/stacks/common_msgs/trunk --svn

  $ rosws update --parallel=2

``-j=2`` is a shorter version of that option.
The default for rosws is not to do parallel work because checking out
or updating both may require user interaction, which can get confusing
with many threads running at the same time.

Getting the most out of info
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Advanced users may want to look at bit more at the info command options.

Then we can print the info e.g. of just geometry as yaml or store it in a file::

  $ rosws info geometry --yaml > geometry-1.6.1.rosinstall

Another interesting feature for scripters is the ``--only`` option::

  $ rosws info --only=path,cur_revision
  /opt/ros/fuerte/share/ros,
  /opt/ros/fuerte/share,
  /opt/ros/fuerte/stacks,
  /opt/ros/fuerte/setup.sh,
  /tmp/rosws_tutorial/common_msgs,-r39123
  /tmp/rosws_tutorial/geometry,53a0d9160982

This yields a CSV representation of the columns you gave, in this case
retrieving from SCM providers the current revision id.

Adding ROS to workspace
-----------------------

If you want to make your workspace a ROS workspace, you will need to do two things:

* Manually add core ros stacks to your .rosinstall (copy and paste from /opt/ros/<distro>/.rosinstall to the TOP of your local .rosinstall file)
* regenerate your setup.* files by calling ``rosws regenerate``

Cleanup workspace
-----------------

The tutorial ends here, what you can do is deleting the workspace folder:

::

  $ rm -rf /tmp/rosws_tutorial
