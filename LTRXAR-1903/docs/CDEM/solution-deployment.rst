Solution Deployment
###################

.. |refresh-button| image:: images/appd-refresh.png
    :width: 20


Lab Resources
-------------

Use your assigned POD number to locate your Lab resources in the tables below. You will need the information in these tables for the Lab exercises in this section. Verify access to your Public Store link.

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


1. Instrument nopCommerce's Frontend
------------------------------------

ACME wants to learn more about its end users to improve the store performance and UI design. ACME also wants to understand how the performance of the store varies by:

    - location
    - client type, device, browser and browser version, and network connection for web requests
    - application and application version, operating system version, device, and carrier for mobile requests

In this lab, you will leverage AppDynamics' Browser Real-User Monitoring (Browser RUM) to instrument the frontend of the nopCommerce web application, which will allow you to see how the web application is performing from the point of view of a real end-user.

The AppDynamics Java Agent and .NET Agent support auto-instrumentation of browser applications by automatically injecting the JavaScript Agent. The automatic injection uses AppDynamics server-side agents to automatically add the "adrum" header and footer to each of your web pages.

You will need the following AppDynamics information during this lab exercise:

    - **Controller URL**: https://cisco-cx-ps-lab.saas.appdynamics.com/controller/
    - **Account Name**: ``cisco-cx-ps-lab``
    - **Username**: ``fso-training@cisco.com``
    - **Password**: Provided by the instructor.

Please, follow the steps below to enable automatic JavaScript Agent injection:

#. Open the `AppDynamics Controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_, navigate to your ``nopcommerce#`` application (where ``#`` is your POD number), then to :guilabel:`Configuration > User Experience App Integration > JavaScript Agent Injection`.

#. Slide the **Enable** switch to On.

    .. image:: images/eum-javascript-agent-0.png
        :width: 800
        :align: center


#. From the **Inject the JavaScript Agent configured for this Browser App** dropdown, select your browser application ``nopCommerce-UI-#`` (where the ``#`` symbol is your POD number), then check the **Enable** Automatic Injection of JavaScript check box and click **Save**.

    .. image:: images/eum-javascript-agent-1.png
        :width: 1200
        :align: center

    |

    .. note::
        Your browser application was previously created by the instructor on our AppDynamics SaaS controller for this lab exercise. You can see the list of browser applications by navigating to the :guilabel:`User Experience` tab on the top navigation bar.

#. After enabling automatic injection, you must specify the server-side business transactions for which automatic JavaScript injection is enabled. First, select all business transactions from the list on the right shown in the screenshot (If you don't see any business transactions, click **Refresh List**). Next, click **< Add** to move the business transaction to the list on the left and click **Save**.

    .. image:: images/eum-javascript-agent-2.png
        :width: 1200
        :align: center

    |

    .. note::
        Not all your business transactions may appear here—the list includes only those transactions that AppDynamics can parse for automatic injection, those based on Jasper-compiled JSPs or ASP.NET, ASP.NET Core, or ASPX.NET pages.

#. Navigate to the :guilabel:`User Experience` tab on the top navigation bar and double-click on your assigned browser application (nopCommerce-UI-#) to confirm that the **Overview** dashboard shows information for your application.

    .. image:: images/eum-overview-dashboard.png
        :width: 1200
        :align: center

    |

    .. tip::
        If no data is shown for your browser application, wait for a few minutes, use the time range menu to change the range to the **last 5 minutes**, and click the **Refresh** |refresh-button| button.

#. Explore the **Geo** and **Usage Stats** dashboards.

    .. image:: images/eum-geo-dashboard.png
        :width: 1200
        :align: center

    |

    .. image:: images/eum-usagestats-dashboard.png
        :width: 1200
        :align: center


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

#. Your agent could appear in the list with an IP address or ``thousandeyes-va-######`` as a name. If so, click on your agent row, and a pop-up window will appear on the right. Change the **Agent Name** field value to ``dmz-te-student-#``, where ``#`` is your POD number, and click **Save Changes**.

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

#. Open the `ThousandEyes controller <https://app.thousandeyes.com/>`_, navigate to :guilabel:`Cloud and Enterprise Agents > Agent Setting` on the left-side navigation menu and confirm that your agent named ``az-te-student-#``, where ``#`` is your POD number, is listed there.

    .. image:: images/teAgent2.png
        :width: 800
        :align: center

**Congratulations!** At this point, you should have two ThousandEyes Enterprise Agents deployed. Let’s now continue with the Solution Configuration section.

.. sectionauthor:: Yossi Meloch <ymeloch@cisco.com> , Jairo Leon <jaileon@cisco.com>, Ovesnel Mas Lara <omaslara@cisco.com>
