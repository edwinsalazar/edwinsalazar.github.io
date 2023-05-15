Solution Configuration
######################

.. |add-button| image:: images/appd-add-button.png
    :width: 20

1. Create a Custom Backend Detection Rule
=========================================

In AppDynamics, uninstrumented databases and remote services are collectively known as backends. AppDynamics automatically discovers a wide range of backends using automatic discovery rules to identify and name the databases and remote services.

AppDynamics limits the number of backend systems that can be registered and tracked. Once the limit is reached (databases and remote services combined), additional backends are put in the **All other traffic for <type>** category, where "type" is the backend type such as HTTP or Web Service.

Example of multiple HTTP backends shown on the application flow map:

    .. image:: images/appdNopcommerceFlowMapHttpBackends.png
        :width: 900
        :align: center

Metrics for the backend calls grouped in the **All other traffic** category are aggregated, as shown in the following **Remote Services** view for the nopCommerce application:

    .. image:: images/appdRemoteServicesAllOtherHTTP.png
        :width: 1200
        :align: center

If the **All other traffic** group includes important backend instances that you want to be individually tracked, for example, the "HTTP Paypal API" banckend for nopCommerce application, you can create custom backend detection rules.

Let's create a custom backend detection rule for our PayPal backend.

#. Open the `AppDynamics Controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_, navigate to your ``nopcommerce-#`` application (where ``#`` is your POD number), then to :guilabel:`Configuration > Instrumentation > Backend Detection`.

    .. image:: images/appdConfigureBackendDetection1.png
        :width: 1200
        :align: center

#. Select your nopCommerce application from the **App or Tier** list, click the :guilabel:`.NET` tab, select **HTTP** type under **Automated Backend Discovery**, scroll down if needed, and click the **Add** button under **Custom HTTP Discovery Rules**: 

    .. image:: images/appdAddBackendDetectionRule.png
        :width: 1200
        :align: center

#. On the :guilabel:`Summary` tab, enter the following **Name** for this rule: ``PayPal API``

    .. image:: images/appdCreateHttpBackendDetectionRule1.png
        :width: 600
        :align: center

#. Click the :guilabel:`Rule Configuration` tab, and create a new **Match Condition** that matches hosts starting with ``api.sandbox.paypal.com``, then click **Save**:

    .. image:: images/appdCreateHttpBackendDetectionRule2.png
        :width: 600
        :align: center

#. Verify that the newly created rule is listed in the **Custom HTTP Discovery Rules** table:

    .. image:: images/appdCustomHttpDiscoveryRules.png
        :width: 1200
        :align: center

#. Click :guilabel:`Remote Services` on the left navigation menu and delete the existing ``api.sandbox.paypal.com-443`` backend. Our newly created backend rule will detect and create a new backend with the provided name.

    .. image:: images/appdDeleteRemoteService.png
        :width: 1200
        :align: center

.. tip::
    Additional information about AppDynamics backends and backend detection rules can be found in `this AppDynamics documentation page <https://docs.appdynamics.com/appd/22.x/latest/en/application-monitoring/configure-instrumentation/backend-detection-rules>`_.


2. Create ThousandEyes Tests
============================

In this lab exercise, you will create tests in ThousandEyes to monitor multiple dependencies of the nopCommerce application:

    1. The network between Azure and OnPrem
    2. Authoritative DNS servers using a DNS Server test
    3. PayPal APIs using a Web HTTP Server test
    4. UPS APIs using a Web HTTP Server test


2.1 Create an Agent-to-Agent Network Test
-----------------------------------------

An agent-to-agent network test evaluates the performance of the underlying network between two physical sites. In this task, we will create a test to measure standard network metrics such as packet loss, latency, and jitter of the network path or paths (if asymmetric) between ACME On-Prem datacenter and Azure.

    .. tip::
        Visit the ThousandEyes `Agent-to-Agent Test Overview <https://docs.thousandeyes.com/product-documentation/internet-and-wan-monitoring/tests/network-tests/agent-to-agent-test-overview>`_ page for more information.

#. Open a web browser to the `ThousandEyes controller <https://app.thousandeyes.com/>`_ page and authenticate using Single Sign-On.

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Tests`:

    .. image:: images/teTestSetting.png
        :width: 200
        :align: center

#. Click **Add New Test**:

    .. image:: images/newTest.png
        :width: 350
        :align: center

#. Fill in the requested fields with the following information:

    - **Layer**: ``Network``
    - **Test Type**: ``Agent to Agent``
    - **Test Name**: Please prepend your student number to the test name. E.g., ``student-01 - Network Connectivity Azure-OnPrem``
    - **Target Agent**: Select your on-prem agent: ``dmz-te-student-#``, where ``#`` is your POD number.
    - **Interval**: ``5 minutes``
    - **Agents**: Choose your enterprise agent running on Azure: ``az-te-student-#``, where ``#`` is your POD number. You may want to filter the agents' list by the **Enterprise** Built-In label located on the right panel.
    - **Direction**: ``Both Directions``
    
    .. image:: images/teAgent2AgentTest.png
        :width: 700
        :align: center

#. Keep all the other pre-set configurations as is.

#. Click **Create New Test**.


2.2 Create Authoritative DNS Test
---------------------------------

DNS Server tests provide record validation, service availability, and performance metrics. In this task, we will configure ThousandEyes to test the authoritative DNS servers used by the nopCommerce application.

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Tests` and click **Add New Test**:

    .. image:: images/teNewAgentTest.png
        :width: 1100
        :align: center

#. Fill in the requested fields with the following information:

    - **Layer**: ``DNS``
    - **Test Type**: ``DNS Server``
    - **Test Name**: Please prepend your student number to the test name. E.g., ``student-01 - Authoritative DNS``
    - **Domain**: ``nopcommerce-web-#.eastus.cloudapp.azure.com`` / ``IN`` /  ``A`` (where ``#`` is your POD number)
    - **Interval**: ``5 minutes``
    - **Agents**: Choose ``Miami, FL`` Cloud Enterprise agent.

#. Click the **Lookup Servers** button, located next to the **DNS Servers** field (Note that 4 DNS servers will be added to the list)

#. Keep all the other pre-set configurations as is.

#. Click **Create New Test**:

    .. image:: images/teDNSTest.png
        :width: 700
        :align: center


2.3 Create an HTTP Server Test for PayPal API
---------------------------------------------

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Tests` and click **Add New Test**:

    .. image:: images/teNewAgentTest.png
        :width: 1100
        :align: center


#. Fill in the requested fields with the following information:

    - **Layer**: ``Web``
    - **Test Type**: ``HTTP Server``
    - **Test Name**: ``student-# - PayPal API``, where ``#`` is your POD number.
    - **URL**: ``https://api.sandbox.paypal.com/v2/checkout/orders/8UR104719C253354R``
    - **Interval**: ``10 minutes``
    - **Agents**: Choose your enterprise agent running on Azure: ``az-te-student-#``, where ``#`` is your POD number. You may want to filter the agents' list by the **Enterprise** Built-In label on the right panel.

    .. image:: images/tePaypalApiTest1.png
        :width: 700
        :align: center

#. Click the :guilabel:`Advanced Settings` tab and fill in the **HTTP AUTHENTICATION** fields with the following information:

    - **Scheme**: ``Basic``
    - **Username**: Provided by the instructor.
    - **Password**: Provided by the instructor.

    .. image:: images/tePaypalApiTestAuth.png
        :width: 700
        :align: center

#. Click **Create New Test** button.


2.4 Create an HTTP Server Test for UPS APIs
----------------------------------------------

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Tests` and click **Add New Test**:

    .. image:: images/teNewAgentTest.png
        :width: 1100
        :align: center


#. Fill in the requested fields with the following information:

    - **Layer**: ``Web``
    - **Test Type**: ``HTTP Server``
    - **Test Name**: ``student-# - UPS API``, where ``#`` is your POD number.
    - **URL**: ``https://wwwcie.ups.com/api/addressvalidation/v1/1``
    - **Interval**: ``10 minutes``
    - **Agents**: Choose your enterprise agent running on Azure: ``az-te-student-#``, where ``#`` is your POD number. You may want to filter the agents' list by the **Enterprise** Built-In label on the right panel.

    .. image:: images/teUpsApiTest1.png
        :width: 700
        :align: center

#. Click the :guilabel:`Advanced Settings` tab and fill in the **HTTP AUTHENTICATION** fields with the following information:

    - **Scheme**: ``OAuth``
    - **Initial Authentication Scheme**: ``Basic``
    - **Username**: Provided by the instructor.
    - **Password**: Provided by the instructor.
    - **Authentication URL**: ``https://wwwcie.ups.com/security/v1/oauth/token``
    - **Authentication Request Method**: ``POST``
    - **POST Body**: ``grant_type=client_credentials``
    - **Authentication Headers**: ``Content-Type: application/x-www-form-urlencoded``

    .. image:: images/teUpsApiTestAuth.png
        :width: 700
        :align: center

#. Fill in the **HTTP REQUEST** fields with the following information:

    - **Request Method**: ``POST``
    - **POST Body**: 

    .. code-block:: json

        {
            "XAVRequest": {
                "AddressKeyFormat": {
                    "ConsigneeName": "RITZ CAMERA CENTERS-1749",
                    "BuildingName": "Innoplex",
                    "AddressLine": [
                        "26601 ALISO CREEK ROAD",
                        "STE D",
                        "ALISO VIEJO TOWN CENTER"
                    ],
                    "Region": "ROSWELL,GA,30076-1521",
                    "PoliticalDivision2": "ALISO VIEJO",
                    "PoliticalDivision1": "CA",
                    "PostcodePrimaryLow": "92656",
                    "PostcodeExtendedLow": "1521",
                    "Urbanization": "porto arundal",
                    "CountryCode": "US"
                }
            }
        }    
    
    - **Custom Headers**: ``Root Request`` ``Authorization: Bearer %{json:access_token}``

    .. image:: images/teUpsApiTestHttpRequest.png
        :width: 700
        :align: center

#. Click **Create New Test** button.


3. Create ThousandEyes Labels
=============================

Labels are used to tag and filter the agents and tests. In this task, you will:

- Create a label with your student number and assign it to all your tests and agents
- Create a label with the location of the agent (Azure/On-Prem) and assign it to your agents

3.1 Create Agent Labels
-----------------------

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Agent Settings > Agent Labels`:

#. Click **Add New Label** button:

    .. image:: images/teAgentLabel.png
        :width: 1000
        :align: center

#. In the *Add New Label* dialog, type the **Label Name**. Please prepend your student number to the name. E.g., ``student-01 Agents``:

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
        :width: 1000
        :align: center

3.2 Create Test Labels
----------------------

#. Navigate to :guilabel:`Cloud and Enterprise Agents > Test Settings > Test Labels`:

#. Click **Add New Label** button:

    .. image:: images/teTestLabel.png
        :width: 1000
        :align: center

#. In the *Add New Label* dialog, type the **Label Name**. Please prepend your student number to the name. E.g., ``student-01 Tests``:

    .. image:: images/teTestLabelName.png
        :width: 500
        :align: center

#. Select a label color by clicking one of the color circles:

    .. image:: images/teLabelColor.png
        :width: 400
        :align: center

#. Click the **Tests** drop-down menu and select the checkboxes next to your four tests.

#. Click **Add New Label**:

    .. image:: images/teTestsAddLabel.png
        :width: 1000
        :align: center


4. Create ThousandEyes Alert Rules
==================================

As part of this exercise, you will create four **"Alert Rules"** for the four tests created previously. 


4.1 Create Network Agent to Agent Alert Rule
--------------------------------------------

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


4.2 Create DNS Alert Rule
-------------------------

#. Navigate to :guilabel:`Alerts > Alert Rules > Cloud and Enterprise Agents`.

    .. image:: images/teRules1.png
        :width: 200
        :align: center

#. Click **Add New Alert Rule** button:

    .. image:: images/teRules2.png
        :width: 900
        :align: center

#. Fill in the requested fields with the following information:

    - **Alert Type**: ``DNS``, ``DNS Server``
    - **Rule Name**: ``student-# DNS Rule - Major``, where ``#`` is your POD number.
    - **Tests**: locate the DNS test previously created. E.g., ``student-01 - Authoritative DNS``
    - **Agents**: ``All Agents``
    - **Severity**: ``Major``

#. Configure the **ALERT CONDITIONS** section as shown below:

    .. image:: images/teAlertDNS.png
        :width: 1000
        :align: center

#. Click **Create New Alert Rule**.


5. Integrate AppDynamics and ThousandEyes
=========================================

As part of this use case, we will integrate AppDynamics and ThousandEyes to provide a comprehensive view of the application experience. At the time of writing, there are four officially supported integrations:

    1. `Sending ThousandEyes Alerts to AppDynamics <https://docs.thousandeyes.com/product-documentation/integration-guides/appdynamics/integration-alerts>`_
    2. `Visualize ThousandEyes data along with application data on the dashboard created in AppDynamics Dash Studio <https://docs.appdynamics.com/latest/en/appdynamics-essentials/dashboards-and-reports/dash-studio/thousandeyes-integration-with-appdynamics>`_
    3. `Triggering ThousandEyes Snapshots from AppDynamics <https://docs.thousandeyes.com/product-documentation/integration-guides/appdynamics/integration-snapshot>`_
    4. `Embedding ThousandEyes Widgets in AppDynamics Dashboards <https://docs.thousandeyes.com/product-documentation/integration-guides/appdynamics/integration-widgets>`_

In this lab, you will practice integrations 3 and 4.

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

    - **Name**: ``AppD - nopCommerce-# - Error``, where ``#`` is your POD number.
    - **AppDynamics Instance**: ``https://cisco-cx-ps-lab.saas.appdynamics.com`` 
    - **Auth Type**: ``Basic``
    - **AppDynamics Username**: ``fso-training%40cisco.com@cisco-cx-ps-lab``
    - **AppDynamics Password**: Provided by the instructor.
    - **Select Services**: ``Connect to alert notifications service``
    - **Application Name**: ``nopCommerce-#``, where ``#`` is your POD number.
    - **Severity**: ``Error``

    .. image:: images/teAppDIntegrationForm.png
        :width: 500
        :align: center

#. Click **Test** to ensure the integration works, then click **Save**.

#. Navigate to :guilabel:`Alerts > Alert Rules > Cloud and Enterprise Agents`.

#. Open one of your existing alert rules existing (E.g: ``student-# DNS Rule - Major`` alert rule.), click the :guilabel:`Notifications` tab, set the **Send notifications to** field to the newly created integration and click **Save Changes**.


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


5.3 Triggering ThousandEyes Snapshots from AppDynamics
------------------------------------------------------

ThousandEyes snapshots provide an easy way to share data about an incident with multiple users, teams, or external groups. Anyone with a ThousandEyes snapshot link has full access to the ThousandEyes UI and metrics, including web, network, BGP, and full path visualization.
 
In this Lab exercise, we will create a Health Rule, Actions, and a Policy in AppDynamics to trigger a snapshot in ThousandEyes when the PayPal backend response time is over **700 ms**.

An **HTTP Request Template** was created in advance by the AppDynamics account administrators using the ThousandEyes documentation available `here <https://docs.thousandeyes.com/product-documentation/integration-guides/appdynamics/integration-snapshot>`_.

#. Let's explore the existing **HTTP Request Template**. Navigate to :guilabel:`Alert & Respond` on the top navigation bar, click **HTTP Request Templates** on the left navigation menu, and open the **ThousandEyes Snapshot** template to see its details.

    .. image:: images/appdHttpRequestTemplates.png
        :width: 1200
        :align: center

    |

    .. caution::

        .. image:: ../images/stop-hand-solid.svg
            :width: 25
            :align: left
            
        Please, don't make any changes to the existing HTTP Request templates.

#. To create a new Health Rule that monitors the Average Response Time for our PayPal backend, click **Health Rules** on the left navigation menu, make sure that your nopCommerce application is selected on the top drop-down menu, and click the |add-button| button:

    .. image:: images/appdAddHealthRule.png
        :width: 1200
        :align: center


#. Create the new Health Rule using the following information:

    On the :guilabel:`Overview` tab:

    - **Name**: ``PayPal Response Time``

    |

    On the :guilabel:`Affected Entities` tab:

    - **Health Rule Type**: ``Databases & Remote Services``
    - **Select what Databases & Remote Services this Health Rule affects**: ``These specified Databases & Remote Services``
    - **Selected backend**: ``PayPal API-api.sandbox.paypal.com-443``

    |

    .. image:: images/appdAddHealthRuleEntities.png
        :width: 1200
        :align: center

    |

    On the :guilabel:`Critical Criteria` tab, add a condition:

    - **Condition Name**: ``Response Time``
    - **Select Metric**: ``Average Response Time (ms)``
    - **Type of comparison**: ``> Specific Value``
    - **Threshold Value**: ``700``

    |

    .. image:: images/appdAddHealthRuleCriticalCriteria.png
        :width: 1200
        :align: center

    |

#. Click **Save** to create the Health Rule.

#. To create an HTTP Request Action that makes an API call to ThousandEyes to take a Snapshot, click :guilabel:`Actions` on the left side menu, then click the |add-button| **Create** button. 

    .. image:: images/appdCreateAction.png
        :width: 1200
        :align: center


#. On the **Create Action** form, select **Make an HTTP Request** type of action and click **OK**:

    .. image:: images/appdActionType.png
        :width: 500
        :align: center


#. Fill in the requested fields with the following information: 

    - **Name**: ``Take a TE Snapshot``
    - **HTTP Request Template**: ``ThousandEyes Snapshot``
    - **accessToken**: Your ThousandEyes OAuth Bearer Token, available in **Account Settings > Users and Roles** page under the **Profile** tab, in the **User API Tokens** section.
    - **accountId**: Run the following curl command ``curl https://api.thousandeyes.com/v6/account-groups.json -H 'Authorization: Bearer your-oauth-token'``, replacing ``your-oauth-token`` with your ThousandEyes OAuth Bearer token, to find the ``aid`` value for the ``CX FSO Training`` account group.
    - **lookbackhour**: ``1``
    - **testId**: The instructor will provide instructions on how to find it.
    - **testName**: ``student-# - PayPal API``, where # is your POD number

    |

    .. image:: images/appdCreateHttpAction.png
        :width: 500
        :align: center

#. Click **Save**.

#. Let's now create a **Policy** that will trigger the **Action** we created in the previous step when our **Health Rule** is violated. Click **Policies** on the left navigation menu and click the |add-button| **Create** button.

    .. image:: images/appdCreatePolicy.png
        :width: 1200
        :align: center

#. Fill in the **Create Policy** form policy using the following information:

    On the :guilabel:`Trigger` tab:

    - Set **Name** to ``Trigger a TE Snapshot``
    - Expand the **Health Rule Violation Events** section and check the following option:

        - **Health Rule Violation Started - Critical**


    .. image:: images/appdCreatePolicyTrigger.png
        :width: 1100
        :align: center
    
    - On the :guilabel:`Health Rule Scope` tab:

        - Select **These Health Rules**.
        - Click the |add-button| button and select the ``PayPal Response Time`` health rule from the list.


    .. image:: images/appdCreatePolicyRules.png
        :width: 1100
        :align: center
    
    |

    - On the :guilabel:`Actions` tab:

        - Click the |add-button| button to add a new action.
        - Select the ``Take a TE Snapshot`` action created in the previous exercise.

    .. image:: images/appdCreatePolicyActions.png
        :width: 1100
        :align: center

    |

    - Click **Save** to create the policy.

    .. image:: images/appdPolicies.png
        :width: 1100
        :align: center


.. sectionauthor:: Ovesnel Mas Lara <omaslara@cisco.com>

