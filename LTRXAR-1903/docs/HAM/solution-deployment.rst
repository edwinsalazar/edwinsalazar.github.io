Solution Deployment
###################

.. |refresh-button| image:: ../images/appd-refresh.png
    :width: 20

Lab Resources
-------------

Use your assigned POD number to locate your Lab resources in the tables below. You will need the information in these tables for the Lab exercises in this section. Verify access to your Public Store link.

.. _lab resource mapping:

.. csv-table::
    :file: references/resource-mapping.csv
    :width: 60%
    :header-rows: 1

You will be using the following AppDynamics controller:

- **Controller UI**: https://cisco-cx-ps-lab.saas.appdynamics.com/controller/
- **Account Name**: ``cisco-cx-ps-lab``
- **Controller Host**: ``cisco-cx-ps-lab.saas.appdynamics.com``

.. note::
   The instructor will provide needed credentials and the AppDynamics Access Key at the beginning of the lab.

.. note::
   The MySQL server will be shared with all students. Each instance of the nopCommerce application will have a different DB within the shared server.


1. Install AppDynamics Machine Agent
------------------------------------

The Machine Agent is required to enable the `Server Visibility <https://docs.appdynamics.com/22.2/en/infrastructure-visibility/server-visibility>`_ features and report extended hardware metrics to the controller. The Machine Agent reports basic metrics from the server's OS, for example, %CPU and memory utilization, disk and network I/O.  You can use these metrics to find correlations between infrastructure issues on one or more servers and application-performance issues reported by the App Agents.

In this exercise, you will complete the following tasks to help ACME monitor the performance of the server where the Web tier of the store runs:

    - Unzip the Machine Agent file into a specific directory on the file system
    - Configure the Machine Agent properties
    - Start the Machine Agent as a Windows service
    - Verify the reported metrics on the AppDynamics controller UI

You will need the following information during this exercise:

    - Windows VM:

        - FQDN
        - Username
        - Password

    - AppDynamics SaaS Controller:

        - Host
        - Account Name
        - Account Access Key
        - Username
        - Password

.. note::
   The Machine Agent installation bundle file was previously downloaded for you and placed in the following directory ``C:\Users\fsolab\Downloads`` of your Windows IIS VM. Should you need to download the installer in the future, you can obtain it from the `AppDynamics Download Center <https://download.appdynamics.com/download/>`_.

#. Open a Remote Desktop session to your Windows VM hosted in Azure using the Microsoft Remote Desktop application. The screenshots below are for macOS operating system. Please go through the equivalent steps if you are using Windows.

    .. image:: images/remote-desktop-addpc.png
        :width: 500
        :align: center

    Use your Windows VM FQDN to fill the **PC Name** field:

    .. image:: images/remote-desktop-pcname.png
        :width: 500
        :align: center

#. Unzip the Machine Agent file located at ``C:\Users\fsolab\Downloads\machineagent-bundle-64bit-windows-<version>.zip`` into ``C:\Users\fsolab\Downloads\machineagent-bundle-64bit-windows-<version>``. 

    .. image:: images/agent-unzip.png
        :width: 800
        :align: center
    
    |

    .. caution::
        Ensure no spaces are used in the path to avoid the `Unquoted Service Path Enumeration Vulnerability <https://docs.appdynamics.com/22.2/en/infrastructure-visibility/machine-agent/install-the-machine-agent/windows-install-using-zip-with-bundled-jre#WindowsInstallUsingZIPwithBundledJRE-UnquotedServicePathEnumerationVulnerability>`_.

#. Configure the Machine Agent by editing the ``C:\Users\fsolab\Downloads\machineagent-bundle-64bit-windows-<version>\conf\controller-info.xml`` XML file.

    .. tip::
        We made available the **Notepad++** tool on your Windows VM. It can make it easier to edit the XML configuration file.

    The following settings must be changed:
    
    - **controller-host**: your AppDynamics SaaS controller host (provided by the instructor)
    - **controller-port**: ``443``
    - **controller-ssl-enabled**: ``true``
    - **account-access-key**: Your AppDynamics access key (provided by the instructor)
    - **account-name**: Your AppDynamics account name (provided by the instructor)
    - **sim-enabled**: ``true`` (needed to enable expanded set of Server Visibility metrics)
    - **dotnet-compatibility-mode**: ``true``

    |

    Don't forget to save the file after changes are done!

    You can use the following configuration file as a reference. The highlighted lines are the ones you need to change.

    .. literalinclude:: ./controller-info.xml
        :emphasize-lines: 14, 21, 27, 50, 56, 61, 86
        :linenos:

#. It is now time to start the agent as a Windows service. Open a Windows Command Prompt (CMD) window and run the following commands:

    .. code-block:: bash
    
        cd C:\Users\fsolab\Downloads\machineagent-bundle-64bit-windows-<version>
        cscript InstallService.vbs

    .. image:: images/cmd.png
        :width: 800
        :align: center

#. Now that you have the Machine Agent installed, let's `open the AppDynamics controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_, log in using the AppDynamics account, username and password provided by your instructor, and navigate to the :guilabel:`Servers` tab to confirm that your Windows server appears in the Server Visibility module. You should be able to see information about server resources like CPU, Memory, Disk, Network, etc.

    .. image:: images/appd-servers.png
        :align: center

    |

    .. note::
        It could take a few minutes for the metrics to appear on the Server dashboard.


    .. note::
        The Controller UI may show the server name with **"-java-MA"** appended to the host ID. This is expected behavior; the **"-java-MA"** suffix indicates that the host has a Machine Agent running in .NET Compatibility Mode.


2. Install AppDynamics .NET Agent
---------------------------------

To monitor ACME's nopCommerce application, you need to install the AppDynamics .NET Agent on the Windows VM that hosts the nopCommerce application. This agent allows monitoring IIS applications, Windows services, or standalone applications. 

In this exercise, you will complete the following tasks:

    - Install the .NET agent using the MSI installer package
    - Use the AppDynamics Agent Configuration utility to configure the .NET Agent and restart IIS
    - Verify the reported metrics on the AppDynamics controller UI

You will need the following information during this exercise:

    - Windows VM:

        - FQDN
        - Username
        - Password

    - AppDynamics SaaS Controller:

        - Host
        - Account Name
        - Account Access Key
        - Username
        - Password
        - Application Name

.. note::
   The .NET Agent MSI installer package file was previously downloaded for you and placed in the following directory ``C:\Users\fsolab\Downloads`` of your Windows IIS VM. Should you need to download the installer in the future, you can obtain it from the `AppDynamics Download Center <https://download.appdynamics.com/download/>`_.

#. Using the Microsoft Remote Desktop application, open a Remote Desktop session to your Windows VM hosted in Azure (skip this step if you are already connected).

#. Run the MSI installer located at ``C:\Users\fsolab\Downloads\dotNetAgentSetup64-<version>.msi``:

    a. Accept the End-user License Agreement.
    b. Leave the default installation paths and click **Install**.
    c. Once the installation is complete, leave checked the option to lunch the agent configuration utility and click **Finish**.

#. Use the .NET Agent Configuration wizard to configure and deploy the agent:

    a. Click **Next** to start the configuration.
    b. Leave the default `Log directory permissions` and click **Next**.
    c. A new `Log directory permissions` step is shown, with `Granted Successfully` status for each account. Click **Next**.
    d. On the `Controller Configuration` step, fill up the following fields:
        
        - **Server(Name/IP)**: Your AppDynamics SaaS controller host (provided by the instructor)
        - **Port Number**: ``443``
        - **Enable SSL**: ``checked``
        - **Enable TLS 1.2**: ``checked``
        - **Multi-Tenant Controller**: ``checked``
        - **Account Name**: Your AppDynamics account name (provided by the instructor)
        - **Account Access Key**: Your AppDynamics access key (provided by the instructor)

    .. image:: images/controller-config.png
        :width: 65%
        :align: center

    |
    
    e. Click **Test Controller Connection** and make sure the connection succeeds before clicking **Next**.
    f. Select your respective ``nopCommerce-#`` business application from the **Existing Applications from the Controller** input field on the `Application Configuration` step. The number (``#``) on the application name must be the same as your assigned POD number. Click **Next**.

        .. note::
            Your business application was previously created by the instructor on our AppDynamics SaaS controller for this lab exercise.

    .. image:: images/dotnet-app-name.png
        :width: 65%
        :align: center

    |

    g. Select the **Manual (Advanced)** method and click **Next** on the `Assign IIS Applications to tiers` step.
    h. Now, let's add a new ``Web`` tier and assign it to the ``nopCommerce`` IIS application as shown in the following example screenshots, then click **Next**.

    .. image:: images/dotnet-add-tier.png
        :width: 65%
        :align: center

    .. image:: images/dotnet-assign-tier.png
        :width: 65%
        :align: center

    |

    i. On the `Configuration Summary` step, ensure that the **Restart IIS** checkbox is checked, then click **Next** to advance through the installer. The wizard will apply the configurations you’ve selected.

    .. image:: images/dotnet-config-summary.png
        :width: 65%
        :align: center

    |

    j. When you see the status “Finished applying configuration. Your system is ready.” click **Next**, then **Done** to close the wizard.


#. Log into the `AppDynamics Controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_ using the AppDynamics account, username, and password provided by your instructor, navigate to the :guilabel:`Applications` tab, open your nopCommerce application, then click on :guilabel:`Tiers & Nodes`.

#. You should see the Web tier you created using the agent configuration utility. Then, expand it to see the Node and Agent status.

    .. image:: images/appd-tiers-nodes.png
        :width: 75%
        :align: center

    |

    .. tip::
        If the Web tier is not showing in AppDynamics UI, wait for a few minutes, use the time range menu to change the range to **Last 5 minutes**, and click the Refresh |refresh-button| button.

#. Navigte to the :guilabel:`Application Dashboard` to visualize the application flow map.

    .. image:: images/appd-nopcommerce-flowmap.png
        :width: 75%
        :align: center

    |

    .. tip:: 
        If the application flow map is not showing in AppDynamics UI, wait for a few minutes, use the time range menu to change the range to **Last 5 minutes**, and click the Refresh |refresh-button| button.

**Congratulations!** You have completed the instrumentation of the nopCommerce application.


3. Install AppDynamics DB Agent
-------------------------------

.. note::
    
    .. image:: ../images/stop-hand-solid.svg
        :width: 25
        :align: left

    Stop here. The instructor will do the AppDynamics Database Visibility agent installation as a demonstration. The agent will be installed on a VM running CentOS.

.. note::
   The agent installation file was previously downloaded for you and placed in the following directory ``/root/`` of your MySQL VM. Should you need to download the installer in the future, you can obtain it from the `AppDynamics Download Center <https://download.appdynamics.com/download/>`_.


#. Ensure that the machine running the Database Visibility agent meets the system requirements listed `here <https://docs.appdynamics.com/latest/en/database-visibility/database-visibility-system-requirements>`_.


#. Install the following packages needed for the DB agent installation:

    .. code-block:: bash

        yum install -y unzip
        yum install -y java-11-openjdk-devel

#. Create a new directory for the DB Agent installation.

    .. code-block:: bash

        mkdir -p /opt/appd/db-agent


#. Unzip the DB agent ZIP file into the directory created on the previous step:

    .. code-block:: bash

        cd /root
        unzip db-agent-22.9.0.2850.zip -d /opt/appd/db-agent


#. Change the directory to ``/opt/appd/db-agent``:

    .. code-block:: bash

        cd /opt/appd/db-agent

#. Change the following settings on the DB agent configuration file:

    - **controller-host**: your AppDynamics SaaS controller host (provided by the instructor)
    - **controller-port**: ``443``
    - **controller-ssl-enabled**: ``true``
    - **account-access-key**: Your AppDynamics access key (provided by the instructor)
    - **account-name**: Your AppDynamics account name (provided by the instructor)

    |

    .. code-block:: bash

        vi conf/controller-info.xml

    You can use the following configuration file as a reference. The highlighted lines are the ones you need to change.

    .. literalinclude:: ./references/db-controller-info.xml
        :emphasize-lines: 16, 23, 29, 64, 69
        :linenos:

#. Create an initialization script that starts the agent automatically whenever the machine starts up.

    .. tip::
        The file is already created for you and placed in the lab directory ``/root/appd-db-agent``.

    a. The following script can be used as an example, the highlighted lines are the ones you need to change.


        .. literalinclude:: ./references/appd-db-agent
            :emphasize-lines: 17, 24
            :linenos:


    b. Enable execution permissions for the script:

        .. code-block:: bash

            cd /root
            chmod 775 appd-db-agent

    c. Place the script in the ``/etc/init.d`` directory on your system:

        .. code-block:: bash

            cp appd-db-agent /etc/init.d

        .. note::
            If SELinux is enabled, we don't want to preserve the SELinux context of the ``appd-db-agent`` file when copying it to ``/etc/init.d``. With ``cp`` command the copied file inherits the SELinux context of the destination directory. To learn more about SELinux contexts visit `this link <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/sect-security-enhanced_linux-working_with_selinux-maintaining_selinux_labels_>`_.

    d. Add the script as a service as follows:

        .. code-block:: bash

            chkconfig --add appd-db-agent
            chkconfig --level 2345 appd-db-agent on


    e. Start the agent:

        .. code-block:: bash

            systemctl start appd-db-agent 
            systemctl status appd-db-agent


    The Database Agent now starts automatically upon machine startup.

    .. note::
        Additional information on automatically starting the DB agent can be found `here for Linux <https://docs.appdynamics.com/latest/en/database-visibility/administer-the-database-agent/start-and-stop-the-database-agent/start-the-database-agent-automatically-on-linux>`_ and `here for Windows <https://docs.appdynamics.com/latest/en/database-visibility/administer-the-database-agent/start-and-stop-the-database-agent/start-the-database-agent-automatically-on-windows>`_.


#. Confirm that the agent is running and registered. To do so, open the `AppDynamics controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_, click the settings gear on the top right corner of the screen, click :guilabel:`AppDynamics Agents` (only the account administrators see this option), and search for your agent on the :guilabel:`Databases Agents` tab.


.. sectionauthor:: Ali Eftekhari <aleftekh@cisco.com>, Jairo Leon <jaileon@cisco.com>, Ovesnel Mas Lara <omaslara@cisco.com>