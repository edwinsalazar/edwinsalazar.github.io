.. |appdyanmics-logo| image:: ../images/appdynamics-logo.png
    :width: 18

.. |thousandeyes-logo| image:: ../images/thousandeyes-logo.png
    :width: 22

Overview
########
    
The `Application Dependency Monitoring​​ (ADM) <https://www.cisco.com/c/en/us/solutions/full-stack-observability/full-stack-observability-demo.html>`_ use case allows AppOps and NetOps teams to understand the performance of managed and unmanaged (third-party) application services and APIs, including Internet and cloud network performance.

- Gain end-to-end network visibility across your enterprise, Internet, and end-user devices
- Correlate insights from performance data in order to proactively remediate issues
- Ensure uptime with real-time global outage detection

The ADM use case leverages |appdyanmics-logo| `AppDynamics <https://www.appdynamics.com/>`_ and |thousandeyes-logo| `ThousandEyes <https://www.thousandeyes.com/>`_ products.


Lab Topology
------------

   .. image:: images/adm_lab_topology.png
      :width: 800
      :align: center


Lab Objectives
--------------

With the hands-on activities in this Lab, you will help ACME company to:

- Gain visibility into external dependencies of the nopCommerce application, which are outside of ACME's IT team control, like DNS, payment gateways, shipping provider APIs.

During the lab exercises, you will:

- Instrument the backend of nopCommerce application by installing AppDynamics .NET agent
- Explore AppDynamics application flow map
- Create custom backend detection rules in AppDynamics
- Deploy ThousandEyes agents to monitor the network and external dependencies of the nopCommerce application
- Create ThousandEyes tests
- Create ThousandEyes labels and alert rules
- Integrate ThousandEyes and AppDynamics


Prerequisites
-------------

Before you can start working on the ADM labs, you need:

- Access to an AppDynamics SaaS account (provided by the instructor)
- Access to a ThousandEyes SaaS account (provided by the instructor)
- Microsoft Remote Desktop application on your laptop
- VPN access to CXPM's DMZ Lab

.. sectionauthor:: Ovesnel Mas Lara <omaslara@cisco.com>
