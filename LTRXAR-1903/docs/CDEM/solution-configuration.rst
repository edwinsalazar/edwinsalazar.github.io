Solution Configuration
######################

.. |add-button| image:: images/appd-add-button.png
    :width: 20

1. Create ThousandEyes Tests
============================

In this lab exercise, you will create four different tests in ThousandEyes:

    1. Network - Agent to Server
    2. Network - Agent to Agent
    3. Web - Page Load


1.1 Create Agent to DB Server Test
----------------------------------

An agent-to-server test measures round-trip network performance between an agent and a remote server. The target may be either be an IP address or hostname.

#. Open a web browser to the `ThousandEyes controller <https://app.thousandeyes.com/>`_ page and authenticate using Single Sign-On.

    .. image:: images/teLogin2.png
        :width: 300
        :align: center

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Tests`:

    .. image:: images/teTestSetting.png
        :width: 200
        :align: center

#. Click **Add New Test**

    .. image:: images/newTest.png
        :width: 350
        :align: center

#. Choose the **Network** layer:

    .. image:: images/teNetworkLayer.png
        :width: 450
        :align: center

#. Choose the **Agent to Server** test type:

    .. image:: images/teTestType2.png
        :width: 300
        :align: center

#. Fill in the requested fields with the following information:

        - **Test Name**: Please prepend your POD number to the test name. E.g., ``student-01 - Connectivity to MySQL from Azure``
        - **Target**: ``172.40.142.11``
        - **Port**: ``3306``
        - **Interval**: ``5 minutes``
        - **Agents**: Choose your enterprise agent running on Azure: ``az-te-student-#``, where ``#`` is your POD number. You may want to filter the agents' list by the **Enterprise** Built-In label located on the right panel.

    .. image:: images/teNewTest.png
        :width: 700
        :align: center

#. Keep all the other pre-set configurations as is.

#. Click **Create New Test**.


1.2 Create an Agent-to-Agent Network Test
-----------------------------------------

An agent-to-agent network test evaluates the performance of the underlying network between two physical sites. In this task, we will create a test to measure standard network metrics such as packet loss, latency, and jitter of the network path or paths (if asymmetric) between ACME On-Prem datacenter and Azure.

    .. tip::
        Visit the ThousandEyes `Agent-to-Agent Test Overview <https://docs.thousandeyes.com/product-documentation/internet-and-wan-monitoring/tests/network-tests/agent-to-agent-test-overview>`_ page for more information.


#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Tests`:

    .. image:: images/teTestSetting.png
        :width: 200
        :align: center

#. Click **Add New Test**:

    .. image:: images/newTest.png
        :width: 350
        :align: center

#. Choose the **Network** layer:

    .. image:: images/teNetworkLayer.png
        :width: 450
        :align: center

#. Choose the **Agent to Agent** test type:

    .. image:: images/teTestType.png
        :width: 300
        :align: center

#. Fill in the requested fields with the following information:

        - **Test Name**: Please prepend your POD number to the test name. E.g., ``student-01 - Network Connectivity Azure-OnPrem``
        - **Target Agent**: Select your on-prem agent: ``dmz-te-student-#``, where ``#`` is your POD number.
        - **Interval**: ``5 minutes``
        - **Agents**: Choose your enterprise agent running on Azure: ``az-te-student-#``, where ``#`` is your POD number. You may want to filter the agents' list by the **Enterprise** Built-In label located on the right panel.
        - **Direction**: ``Both Directions``

    .. image:: images/teAgent2AgentTest.png
        :width: 700
        :align: center

#. Keep all the other pre-set configurations as is.

#. Click **Create New Test**.


1.3 Create nopCommerce Page Load Test
-------------------------------------

Page load tests use te-chromium, a browser-based upon the Chromium browser codebase, to obtain in-browser site performance metrics. The metrics include the completed page load time and phase information for each DOM component.

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Tests`:

    .. image:: images/teTestSetting.png
        :width: 200
        :align: center

#. Click **Add New Test**:

    .. image:: images/newTest.png
        :width: 350
        :align: center

#. Choose the **Web** layer:

    .. image:: images/teWebLayer.png
        :width: 450
        :align: center

#. Choose the **Page Load** test type:

    .. image:: images/teTestType3.png
        :width: 450
        :align: center

#. Fill in the requested fields with the following information:

        - **Test Name**: Please prepend your POD number to the test name. E.g., ``student-01 - Load Home Page``
        - **Page Load Interval**: ``5 minutes``
        - **HTTP Server Interval**: ``5 minutes``
        - **URL**: Enter your designated *"Public Store Link"* in the **URL** field, E.g., `<http://nopcommerce-web-#.eastus.cloudapp.azure.com>`_, where ``#`` is your POD number. (please check the :ref:`Lab Resources section <lab resource mapping>`)
        - **Agents**: Choose the following (3) Cloud Enterprise agents: :guilabel:`Miami, FL`, :guilabel:`Rio de Janeiro, Brazil`, :guilabel:`Melburne, Australia`

    .. image:: images/tePageLoadTest.png
        :width: 700
        :align: center

#. Keep all the other pre-set configurations as is.

#. Click **Create New Test**.


2. Create ThousandEyes Labels
=============================

Labels are used to tag and filter the agents and tests. In this lab, you will:

- Create a label with your POD number and assign it to all your tests and agents
- Create a label with the location of the agent (Azure/On-Prem) and assign it to your agents

2.1 Create Agent Labels
-----------------------

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Agent Settings > Agent Labels`:

#. Click **Add New Label** button:

    .. image:: images/teAgentLabel.png
        :width: 1000
        :align: center

#. In the *Add New Label* dialog, type the **Label Name**. Please prepend your POD number to the name. E.g., ``student-01 Agents``:

    .. image:: images/teLabelName.png
        :width: 500
        :align: center

#. Select a label color by clicking one of the color circles:

    .. image:: images/teLabelColor.png
        :width: 400
        :align: center

#. Click the **Agents** drop-down menu and select the checkboxes next to your created agents:

    .. image:: images/teLabelAgents.png
        :width: 400
        :align: center

#. Click **Add New Label**:

    .. image:: images/teAgentsAddLabel.png
        :width: 800
        :align: center

2.2 Create Test Labels
----------------------

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Test Labels`:

#. Click **Add New Label** button:

    .. image:: images/teTestLabel.png
        :width: 800
        :align: center

#. In the *Add New Label* dialog, type the **Label Name**. Please prepend your POD number to the name. E.g., ``student-01 Tests``:

    .. image:: images/teTestLabelName.png
        :width: 500
        :align: center

#. Select a label color by clicking one of the color circles:

    .. image:: images/teLabelColor.png
        :width: 400
        :align: center

#. Click the **Tests** drop-down menu and select the checkboxes next to your four tests:

    .. image:: images/teLabelTests.png
        :width: 400
        :align: center

#. Click **Add New Label**:

    .. image:: images/teTestsAddLabel.png
        :width: 800
        :align: center


3. Create ThousandEyes Alert Rules
==================================

As part of this exercise, you will create four **"Alert Rules"** for the four tests created previously. 


3.1 Create Agent to DB Server Alert Rule
----------------------------------------

#. Navigate to :guilabel:`Alerts > Alert Rules > Cloud and Enterprise Agents`.

#. Click **Add New Alert Rule** button.

    .. image:: images/teRules2.png
        :width: 700
        :align: center

#. Fill in the requested fields with the following information:

    - **Alert Type**: ``Network``, ``Agent to Server``
    - **Rule Name**: ``student-# Azure to OnPRem DB Server - Major``, where ``#`` is your POD number.
    - **Tests**: locate the Agent to Server test previously created. E.g., ``student-17 - Connectivity to MySQL from Azure``
    - **Agents**: ``All Agents``
    - **Severity**: ``Major``

#. Configure the **ALERT CONDITIONS** section as shown below:

    .. image:: images/teAlertAgent2Server.png
        :width: 900
        :align: center

#. Click **Create New Alert Rule**.


3.2 Create Agent to Agent Alert Rule
------------------------------------

#. Navigate to :guilabel:`Alerts > Alert Rules > Cloud and Enterprise Agents`.

#. Click **Add New Alert Rule** button:

    .. image:: images/teRules2.png
        :width: 700
        :align: center

#. Fill in the requested fields with the following information:

    - **Alert Type**: ``Network``, ``Agent to Agent``
    - **Rule Name**: ``student-# Azure to OnPRem Network - Major``, where ``#`` is your POD number.
    - **Direction**: ``Both Directions``
    - **Tests**: locate the Agent to Agent test previously created. E.g., ``student-17 - Network Connectivity Azure-OnPrem``
    - **Agents**: ``All Agents``
    - **Severity**: ``Major``

#. Configure the **ALERT CONDITIONS** section as shown below:

    .. image:: images/teAlertAgent2Agent.png
        :width: 900
        :align: center

#. Click **Create New Alert Rule**.


3.3 Create Page Load Alert Rule
-------------------------------

#. Navigate to :guilabel:`Alerts > Alert Rules > Cloud and Enterprise Agents`.

#. Click **Add New Alert Rule** button:

    .. image:: images/teRules2.png
        :width: 700
        :align: center

#. Fill in the requested fields with the following information:

    - **Alert Type**: ``Web``, ``Page Load``
    - **Rule Name**: ``student-# Page Load Rule - Major``, where ``#`` is your POD number.
    - **Tests**: locate the Page Load test previously created. E.g., ``student-17 - Load Home Page``
    - **Agents**: ``All Agents``
    - **Severity**: ``Major``

#. Configure the **ALERT CONDITIONS** section as shown below:

    .. image:: images/teAlertPageLoad.png
        :width: 900
        :align: center

#. Click **Create New Alert Rule**.


4. Create an Alert in AppDynamics
=================================

ACME wants to alert its AppOps team whenever the end-user experience of the online store is impacted. ACME considers that the end-user experience is impacted when any store page takes more than 3000ms to load in their browsers.

In this lab, you will address ACME’s alerting requirement by configuring a Health Rule, an Action, and a Policy, which together will allow sending email alerts whenever the ``End User Response Time`` is over 3 seconds for any nopCommerce web page.


4.1 Create a Health Rule
------------------------

Health rules let you specify the parameters that represent what you consider normal or expected operations for your environment. The parameters rely on metric values, for example, the average response time browsing applications.

With instrumented nopCommerce's front-end, you can now create health rules to monitor the pages' health. To do this, follow the steps below:

#. Open the `ApppDynamics UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_ and navigate to :guilabel:`User Experience > Browser Apps`. Then, double click on your ``nopCommerce-UI`` application.

#. Navigate to the :guilabel:`Alert & Respond` tab and click :guilabel:`Health Rules` on the left side menu.

#. Click the |add-button| button to create a new Health Rule using the following information:

    On the :guilabel:`Overview` tab:

    - **Name**: ``End User Response Time - Pages``
    - **Use data from the last**: ``10`` min(s)

    |

    .. image:: images/health-rule-ui-01.png
        :width: 1200
        :align: center

    On the :guilabel:`Affected Entities` tab:

    - Select **Health Rule Type**: ``User Experience - Browser Apps``
    - **What does this Health Rule affect?**: ``Pages``
    - **Select what Pages this Health Rule affects**: ``These specified Pages``
    - Use the arrow **<** button to move all available pages to the selected pages.

    |

    .. image:: images/health-rule-ui-02.png
        :width: 1200
        :align: center

    On the :guilabel:`Critical Criteria` tab, add a condition:

    - **Metric Name**: ``End User Response Time``
    - **Metric**: ``End User Response Time (ms)``
    - **Type of comparison**: ``> Specific Value``
    - **Threshold Value**: ``3000``
    - **Trigger only when violation occurs**: ``3 times``

    |

    .. image:: images/health-rule-ui-03.png
        :width: 1200
        :align: center

    |

     .. tip ::
        When multiple conditions are added, pay attention to the drop-down list above the conditions, you may want to select ``All`` or ``Any`` or ``Custom`` depending on your needs.
    

    On the :guilabel:`Warning Criteria` tab:

    - Click **Copy from Critical Criteria** button to copy the Critical Criteria values to the Warning Criteria tab.
    - Change the threshold value next to **> Specific Value** to ``2000``.
    - **Save** the Health Rule.

    |

    .. image:: images/health-rule-ui-04.png
        :width: 1200
        :align: center
    
    The rule should now be enabled and listed under the :guilabel:`Health Rules` section.

        .. image:: images/health-rule-ui-05.png
            :width: 1200
            :align: center
        
        |

    .. note ::
        (Optional) You can play setting different threshold values to the Warning and Critical conditions to see how the health rule behaves.


4.2 Create an Action
--------------------

An action is a predefined, reusable, automated response to an event.

To create an action, follow the steps below:

#. Navigate to :guilabel:`User Experience > Browser Apps`, double click on your ``nopCommerce-UI`` application, and go to the :guilabel:`Alert & Respond` tab and click :guilabel:`Actions` on the left side menu.

#. Click the **Create** |add-button| button to create a new Action.

#. Enter the following information in the pop-up window:

    - Select **Send an email** option under the Notifications section and click **OK** to continue.

    |

    .. image:: images/action-UI-01.png
        :width: 600
        :align: center
    
    - Type your **email address** ``your-cec-id@cisco.com`` and click **Save**.

    |

    .. image:: images/action-UI-02.png
        :width: 600
        :align: center


4.3 Create a Policy
-------------------

A policy can trigger an action in response to any event. You configure which actions are triggered by which events when you configure policies. For example, you can use policies to automate monitoring, alerting, and problem remediation.

Now that you have a Health Rule for the "User Experience - Browser Apps" and an Action to send an email, let’s create a policy that alerts you by email whenever the ``End User Response Time - Pages`` health rule is violated.

#. Navigate to :guilabel:`User Experience > Browser Apps`, double click on your ``nopCommerce-UI`` application, and go to the :guilabel:`Alert & Respond` tab and click :guilabel:`Policies` on the left side menu.

#. Click **Create Policy Manually** to create a new policy using the following information:

    On the :guilabel:`Trigger` tab:

    - Set **Name** to ``Send UI Alert by Email``
    - Expand the **Health Rule Violation Events** section and check the following options:

        - **Health Rule Violation Started - Warning**
        - **Health Rule Violation Started - Critical**

    |

    .. image:: images/policy-UI-01.png
        :width: 1200
        :align: center
    
    - On the :guilabel:`Health Rule Scope` tab:

        - Select **These Health Rules**.
        - Click the |add-button| button to select the desired health rules.
        - Select the ``End User Response Time - Pages`` health rule and click **Select Health Rule(s)** to confirm the selection.

    |

    .. image:: images/policy-UI-02.png
        :width: 1200
        :align: center
    
    |

    - On the :guilabel:`Actions` tab:

        - Click the **Create** |add-button| button to add a new action.
        - Select ``your-cec-id@cisco.com`` action created on the previous exercise and click **Select**.

    |

    .. image:: images/policy-UI-03.png
        :width: 1200
        :align: center

    |

    - Click **OK** to configure the action.

    |

    .. image:: images/policy-UI-04.png
        :width: 600
        :align: center

    |

    - Click **Save** to save the policy.

    |


    **Congratulations**, your policy is ready! As soon as the ``End User Response Time - Pages`` health rule is violated, the specified action will be triggered, and an alert message will be sent to the specified email address.


5. Integrate AppDynamics and ThousandEyes
=========================================

As part of this use case, we will integrate AppDynamics and ThousandEyes to provide a comprehensive view of the application experience. At the time of writing, there are four officially supported integrations:

    1. `Embedding ThousandEyes Widgets in AppDynamics Dashboards <https://docs.thousandeyes.com/product-documentation/integration-guides/appdynamics/integration-widgets>`_
    2. `Sending ThousandEyes Alerts to AppDynamics <https://docs.thousandeyes.com/product-documentation/integration-guides/appdynamics/integration-alerts>`_
    3. `Triggering ThousandEyes Snapshots from AppDynamics <https://docs.thousandeyes.com/product-documentation/integration-guides/appdynamics/integration-snapshot>`_
    4. `Visualize ThousandEyes data along with application data on the dashboard created in AppDynamics Dash Studio <https://docs.appdynamics.com/latest/en/appdynamics-essentials/dashboards-and-reports/dash-studio/thousandeyes-integration-with-appdynamics>`_

In this lab, you will practice integrations 2 and 4.

You will need the following information during this lab exercise:

- AppDynamics SaaS Controller:

    - **Controller URL**: https://cisco-cx-ps-lab.saas.appdynamics.com/controller/
    - **Account Name**: ``cisco-cx-ps-lab``
    - **Username**: ``fso-training@cisco.com``
    - **Password**: Provided by the instructor.

- ThousandEyes SaaS Controller:

    - **Controller URL**: https://app.thousandeyes.com/
    - **Controller Credentials**: Authenticate using Cisco SSO
    - **Authorization Token**: You will create it during the lab.


5.1 Sending ThousandEyes Alerts to AppDynamics
----------------------------------------------

Another powerful way to integrate ThousandEyes and AppDynamics is to send ThousandEyes alerts to AppDynamics. ThousandEyes alerts are displayed in AppDynamics as custom events. 


#. Open a web browser to the `ThousandEyes Controller UI <https://app.thousandeyes.com/>`_.

#. Navigate to :guilabel:`Integrations` and click the **+ New Integration** button: 

    .. image:: images/teAppDIntegration1.png
        :width: 1200
        :align: center

#. Select **AppDynamics**:
    
    .. image:: images/teAppDIntegrationTemplate.png
        :width: 500
        :align: center

#. Enter the following information in the pop-up window to create a new integration:

    - **Name**: ``AppD - nopCommerce-UI-# - Error``, where ``#`` is your POD number.
    - **AppDynamics Instance**: ``https://cisco-cx-ps-lab.saas.appdynamics.com`` 
    - **Application Name**: ``nopCommerce-UI-#``, where ``#`` is your POD number.
    - **Auth Type**: ``Basic``
    - **AppDynamics Username**: ``fso-training%40cisco.com@cisco-cx-ps-lab``
    - **AppDynamics Password**: Provided by the instructor.
    - **Severity**: ``Error``

    .. image:: images/teAppDIntegrationForm.png
        :width: 500
        :align: center

    .. note::
        The integration user requires the following application-level permission in AppDynamics: ``Create Events``.

    
#. Click **Test** to ensure the integration works, then click **Save**.

#. Navigate to :guilabel:`Alerts > Alert Rules > Cloud and Enterprise Agents`.

#. Open one of your existing alert rules existing (E.g: ``student-01 Page Load Rule`` alert rule.), click the :guilabel:`Notifications` tab, set the **Send notifications to** field to the newly created integration and click **Save Changes**.


5.2 Visualizing ThousandEyes data in Dash Studio
------------------------------------------------

.. note::
    
    .. image:: ../images/stop-hand-solid.svg
        :width: 25
        :align: left

    The instructor will demonstrate the activities of this lab for you. Please stop here and watch the demonstration.

#. Create a ThousandEyes Authorization Token.

    - Open a web browser to the `ThousandEyes Controller UI <https://app.thousandeyes.com/>`_ and navigate to :guilabel:`Account Settings > Users and Roles > Profile > User API Tokens`.
    - Click OAuth Bearer Token **Create**.
    - Copy and save the token in a secure location for later use.

    |

    .. image:: images/te-auth-token-01.png
        :width: 1200
        :align: center        

#. Configure ThousandEyes integration within AppDynamics.

    - Open `ApppDynamics Controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_ and click the :guilabel:`Settings` gear on the top right of the page.
    - Navigate to :guilabel:`Administration > Integrations > ThousandEyes`.
    - Change the **Enable ThousandEyes Integration** field to ``On``.
    - On the **Authorization Token** field, paste the **Authorization Token** you created on the previous step using the following format ``Bearer your-token``.
    - Click **Save**.

    |

    .. image:: images/appd-te-integration-01.png
        :width: 1200
        :align: center

    |

You can now create dashboards in AppDynamics Dash Studio to visualize ThousandEyes data.


6. Dashboards Creation
======================

Once the "Visualizing ThousandEyes data in Dash Studio" integration is done, you can use AppDynamics Dash Studio to combine ThousandEyes data with application data in a single pane of glass dashboard.

#. Open the `ApppDynamics Controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_  and navigate to :guilabel:`Dashboards & Reports > Dash Studio`.

#. Search for your ``nopCommerce-#-ds`` dashboard (where # is your POD number), select it and click **Edit**:

    .. image:: images/ds-dashboard-01.png
        :width: 1200
        :align: center

#. On the **Dashboard**, click **Add Time Series Widget** and fill the **Data** section with the following information:

    - **Enter Widget Title**: ``End User Response Time``
    - **Show me data for**: ``Browser Applications``
    - **Application**: ``nopCommerce-UI-#`` (where # is your POD number)
    - **Browser Entity Type**: ``Pages`` and ``Named``
    - **Pages**: ``nopCommerce-web-#-eastus.cloudapp.azure.com`` (where # is your POD number)
    - **Metric**: ``End User Response Time (ms)``
    - **Limit To**: ``Top`` and ``20`` (default values)

#. Add a new **Time Series Widget** and fill the **Data** section with the following information:

    - **Enter Widget Title**: ``Network Latency``
    - **Show me data for**: ``ThousandEyes``
    - **Metric Category**: ``Network - Agent to Server``
    - **Metric**: ``Latency``
    - **Group By**: ``Tests``
    - **Account Groups**: ``CX FSO Training``
    - **Tests**: ``student-# - Connectivity to MySQL from Azure`` (where # is your POD number)
    - **Test Labels**: ``student-# Agents`` (where # is your POD number)
    - **Agents**: ``Leave it blank``
    - **Agents Labels**: ``Leave it blank``
    - **Custom Chart Type**: ``Area``

#. Organize the widgets in the **Dashboard** by dragging and dropping them to have them in similar positions as the image below.

    .. image:: images/ds-dashboard-02.png
        :width: 1200
        :align: center

.. sectionauthor:: Yossi Meloch <ymeloch@cisco.com>, Jairo Leon <jaileon@cisco.com>, Ovesnel Mas Lara <omaslara@cisco.com>

