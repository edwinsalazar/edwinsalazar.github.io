Solution Deployment
###################

.. |refresh-button| image:: ../images/appd-refresh.png
    :width: 20

.. |http-backend-icon| image:: ../images/appd-http-backend.png
    :width: 35


Lab Resources
-------------

Use your assigned POD number to locate your Lab resources in the tables below. You will need the information in these tables for the Lab exercises in this section.


AppDynamics resources:

- **Controller URL**: https://cisco-cx-ps-lab.saas.appdynamics.com/controller/
- **Account Name**: ``cisco-cx-ps-lab``
- **Crontroller Credentials**: Provided by the instructor.

.. _appd resource mapping:

.. csv-table::
    :file: references/appd-resource-mapping.csv
    :width: 60%
    :header-rows: 1

ThousandEyes resources:

- **Controller URL**: https://app.thousandeyes.com/
- **Controller Credentials**: Authenticate using Cisco SSO
- **Account Group**: ``CX FSO Training``
- **Account Group Token**: Provided by the instructor.

.. _teyes resource mapping:

.. csv-table::
    :file: references/te-resource-mapping.csv
    :width: 60%
    :header-rows: 1


1. Instrument nopCommerce's backend
-----------------------------------

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

    .. image:: images/remote-desktop-addpc.png
        :width: 500
        :align: center

    Use your Windows VM FQDN to fill the **PC Name** field:

    .. image:: images/remote-desktop-pcname.png
        :width: 500
        :align: center

#. Run the MSI installer located at ``C:\Users\fsolab\Downloads\dotNetAgentSetup64-<version>.msi``:

    a. Accept the End-user License Agreement.
    b. Leave the default installation paths and click **Install**.
    c. Once the installation is complete, leave checked the option to lunch the agent configuration utility and click **Finish**.

#. Use the .NET Agent Configuration wizard to configure and deploy the agent:

    a. Click **Next** to start the configuration.
    b. Leave the default **Log directory permissions** and click **Next**.
    c. A new **Log directory permissions** step is shown, with `Granted Successfully` status for each account. Click **Next**.
    d. On the **Controller Configuration** step, fill up the following fields:
        
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
    f. Select your respective ``nopCommerce-#`` business application from the **Existing Applications from the Controller** input field on the `Application Configuration` step. The number (``#``) on the application name must be the same as your assigned student number. Click **Next**.

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

    j. When you see the status **"Finished applying configuration. Your system is ready."**, click **Next**, then **Done** to close the wizard.


#. Log into the `AppDynamics Controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_ using the AppDynamics account, username, and password provided by your instructor, navigate to the :guilabel:`Applications` tab, open your nopCommerce application, then click on :guilabel:`Tiers & Nodes`.

#. You should see the Web tier you created using the agent configuration utility. Then, expand it to see the Node and Agent status.

    .. image:: images/appd-tiers-nodes.png
        :width: 1200
        :align: center

    |

    .. tip::
        If the Web tier is not showing in AppDynamics UI, wait for a few minutes, and click the Refresh |refresh-button| button.

#. Navigate to the :guilabel:`Application Dashboard` to visualize the application flow map.

    .. image:: images/appdNopcommerceFlowMapWithBackends.png
        :width: 1200
        :align: center

    |

    .. note::
        You may see an HTTP backend icon |http-backend-icon| with the ``x HTTP backends`` label instead of ``api.sandbox.paypal.com-443`` label.

    
    As seen in the flow map above, three backends were auto-discovered by AppDynamcis:

        1. nopCommerce DB
        2. Paypal APIs (HTTP)
        3. UPS APIs (Windows Communication Foundation or WCF)

    In AppDynamics, uninstrumented databases and remote services are collectively known as backends.

    AppDynamics discovers backends from exit point calls in the application code. An exit point is a call from an instrumented node to another node or to a backend. When the destination of an exit point call is not instrumented, the exit call results in a backend discovery event.

    We can use this information to determine what additional tests can be configured in ThousandEyes to monitor the external dependencies of our application.

    .. tip:: 
        If the application flow map is not showing in AppDynamics UI, wait for a few minutes and click the **Refresh** |refresh-button| button.

**Congratulations!** You have completed the instrumentation of the nopCommerce backend.


2. Configure On-Prem ThousandEyes Enterprise Agent
--------------------------------------------------

ACME has followed the steps in `this ThousandEyes guide <https://docs.thousandeyes.com/product-documentation/global-vantage-points/enterprise-agents/installing/how-to-set-up-the-virtual-appliance>`_ to deploy, on-premise, an OVA with the ThousandEyes Enterprise Agent. However, it still needs to be configured.

In this lab, you will help the customer configure the virtual appliance and register it on ThousandEyes.

You will need the following ThousandEyes information during this exercise:
    
    - **On-Prem Enterprise Agent configuration URL**: It can be found in the :ref:`ThousandEyes resources table <teyes resource mapping>`.
    - **Enterprise Agent default username**: ``admin``
    - **Enterprise Agent default password**: ``welcome``
    - **Enterprise Agent new password**: ``WelcomeTeyes!``
    - **Account Group Token**: Provided by the instructor.
    - **On-Prem Enterprise Agent Hostname**: ``dmz-te-student-#`` (Replace the ``#`` symbol with your student number.)

.. tip::

    Check the `requirements <https://docs.thousandeyes.com/product-documentation/global-vantage-points/enterprise-agents/installing/enterprise-agent-system-requirements>`_ for the agent. In addition, you can follow the `installation instructions <https://docs.thousandeyes.com/product-documentation/global-vantage-points/enterprise-agents/installing/enterprise-agent-deployment-using-linux-package-method>`_.


#. The instructor will start your assigned "ThousandEyes Virtual Appliance" VM and provide the agent configuration URL. See below a screenshot of the virtual appliance welcome message captured from the VM console:

    .. image:: images/teAppliance.png
        :width: 600
        :align: center

#. Open a web browser and enter the URL displayed on the welcome screen, which is your **On-Prem Enterprise Agent configuration URL**.

#. If you receive a *"Your connection is not private"* message, click on **Advanced** and **Proceed to...** (Assuming you are using Chrome browser).

    .. image:: images/connectionNotPrivate.png
        :width: 600
        :align: center

#. Enter the default username and password (``admin``/``welcome``). Then click **Login**. You will be prompted to change the default password.

    .. image:: images/teLogin.png
        :width: 600
        :align: center


#. Change the password (New password: ``WelcomeTeyes!``).

    .. image:: images/teChangePass.png
        :width: 600
        :align: center

#. Wait a few seconds for the password change process to complete.

    .. image:: images/teSaveSettings.png
        :width: 300
        :align: center

#. Enter the account group token (provided by the instructor) in the **Account Group Token** field. Keep the **Browserbot** and **Enable Crash Reports** configuration as is (Both should be pointed to "Yes"), and press **Continue**.

    .. image:: images/accountToken.png
        :width: 600
        :align: center

    You will be redirected to a Review page similar to the one shown below:

    .. image:: images/teAgentConfigReview.png
        :width: 800
        :align: center

    It is normal if the **Browserbot** is not running yet. It can take a few minutes for it to be started. 
    
    The **NTP** service check will fail. We will configure NTP later in this task. 

#. On the left-hand side menu, click on :guilabel:`Network` and change the **Hostname** according to your assigned **On-Prem Enterprise Agent Hostname** listed on the :ref:`ThousandEyes resources table <teyes resource mapping>` (For example: ``dmz-te-student-1``, ``dmz-te-student-2``, etc.). Then, scroll down and click **Continue** (processing the agent name update may take a few seconds).

    .. image:: images/teMenuNetwork.png
        :width: 600
        :align: center

    .. image:: images/teApplyNetworkSettings.png
        :width: 600
        :align: center

#. Change the **Primary NTP server** value to: ``all.ntp.esl.cisco.com``, and press **Continue**.

    .. image:: images/teNTP.png
        :width: 600
        :align: center

#. Click :guilabel:`Review` on the left-hand side menu and press the **Run Diagnostics** button on the top of the page. Wait for all the items to pass the validation step.

    .. image:: images/teMenuReview.png
        :width: 1200
        :align: center

#. Click **Complete** (You will be re-directed to the :guilabel:`Network` page).

#. Open a web browser to the `ThousandEyes controller <https://app.thousandeyes.com/>`_ page and click **Single sign-on** to log into the platform using your CEC credentials.

    .. image:: images/teLogin2.png
        :width: 300
        :align: center

#. Check the current Account Group on the top-right corner of the controller page, under your name, as shown below. 

    .. image:: images/teAccountGroup2.png
        :width: 300
        :align: center

    | Change the current Account Group to ``CX FSO Training`` if it differs from this value.

    .. image:: images/teAccountGroup.png
        :width: 300
        :align: center

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Agent Setting` on the left-side navigation menu and look for your agent hostname. E.g.:

    .. image:: images/teAgent1.png
        :width: 800
        :align: center

#. Your agent could appear in the list with an IP address or ``thousandeyes-va-######`` as a name. If so, click on your agent row, and a pop-up window will appear on the right. Change the **Agent Name** field value to ``dmz-te-student-#``, where ``#`` is your student number, and click **Save Changes**.

    .. image:: images/teAgentName.png
        :width: 1200
        :align: center


**Congratulations!** You have registered a new Enterprise Agent in the ThousandEyes SaaS controller.


3. Install ThousandEyes Enterprise Agent in Azure
-------------------------------------------------

ACME has deployed an Ubuntu 20.04 virtual machine in Azure for you to install a ThousandEyes Enterprise Agent on it. Please go through the following steps to install and register the new enterprise agent.

You will need the following ThousandEyes information during this lab exercise:

    - **Controller URL**: https://app.thousandeyes.com/
    - **Controller Credentials**: Authenticate using Cisco SSO
    - **Account Group Token**: Provided by the instructor.

.. note::
    The full list of supported Enterprise Agent Operating Systems can be found `here <https://docs.thousandeyes.com/product-documentation/global-vantage-points/enterprise-agents/installing/enterprise-agent-system-requirements#supported-enterprise-agent-operating-systems>`__. In addition, you can find the official Enterprise Agent installation instructions using the  Linux package method `here <https://docs.thousandeyes.com/product-documentation/global-vantage-points/enterprise-agents/installing/enterprise-agent-deployment-using-linux-package-method>`__.


#. Log in to your assigned Linux Ubuntu system.

    .. note::
        The instructor will provide Linux credentials. Replace the ``#`` symbol on the command below with your student number.


    .. code-block:: bash

        ssh fsolab@az-te-student-#.eastus.cloudapp.azure.com

#. Install available upgrades of all packages currently installed on the system:

    .. code-block:: bash

        sudo apt-get update -y

#. To avoid synchronization problems with the ThousandEyes collector it is strongly recommended that you install a Network Time Protocol (NTP) package. To download and install an NTP package, issue the following commands on the agent's host machine:

    .. code-block:: bash

        sudo apt-get -y install ntp
        sudo service ntp start
        service ntp status

#. Download and run the agent installation script. Replace the ``<Account Group Installation Token>`` with the token provided by the instructor. When prompted to change the default log path, answer ``N`` (No).

    .. code-block:: bash

        curl -Os https://downloads.thousandeyes.com/agent/install_thousandeyes.sh
        chmod +x install_thousandeyes.sh
        sudo ./install_thousandeyes.sh -b <Account Group Installation Token>

#. Open the `ThousandEyes controller <https://app.thousandeyes.com/>`_, navigate to :guilabel:`Cloud and Enterprise Agents > Agent Setting` on the left-side navigation menu and confirm that your agent named ``az-te-student-#``, where ``#`` is your student number, is listed there.

    .. image:: images/teAgent2.png
        :width: 800
        :align: center

**Congratulations!** At this point, you should have two ThousandEyes Enterprise Agents deployed, one running on-prem and the other one running on Azure. Let’s now continue with the Solution Configuration section.

.. sectionauthor:: Yossi Meloch <ymeloch@cisco.com> , Jairo Leon <jaileon@cisco.com>, Ovesnel Mas Lara <omaslara@cisco.com>
