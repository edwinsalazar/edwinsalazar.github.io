Overview
########

The `Customer Digital Experience Monitoring​​ (CDEM) <https://ebooks.cisco.com/story/sales-play-appendix/page/2/15>`_ use case allows AppOps / DevOps and NetOps teams to enable actionable, end-to-end insight into application experience, its underlying dependencies, and business impact.​​

The CDEM use case leverages AppDynamics and ThousandEyes products to provide a comprehensive and unified view into the complex digital supply chain for collaborative troubleshooting across teams for faster MTTR (Mean time to repair/resolution).

.. note::
   It is recommended to complete the Hybrid Application Monitoring (HAM) Lab before starting with the Customer Digital Experience Monitoring​​ (CDEM) scenario.

Lab Topology
------------

   .. image:: images/cdem_lab_topology.png
      :width: 800
      :align: center


Lab Objectives
--------------
With the hands-on labs in this section, you will help the AppOps/NetOps team of ACME company to:

- Deploy ThousandEyes agents to monitor the network and external dependencies of the nopCommerce application.
- Learn more about its online store users (location, devices, browsers, journey within the UI) 
- Correlate user behavior to technical problems and business
- Reduce the time to detect and isolate application performance issues impacting the end-user experience

During the lab exercises, you will:

- Configure AppDynamics to inject the Javascript Agent into nopCommerce web pages automatically
- Practice the deployment of ThousandEyes Enterprise Agent (Two methods: using an OVA and using a Linux package)
- Create different types of ThousandEyes tests
- Create ThousandEyes labels and alert rules
- Integrate ThousandEyes and AppDynamics
- Create health rules, policies, and actions on AppDynamics

Prerequisites
-------------

Before you can start working on the CDEM labs, you need the following:

- Access to an AppDynamics SaaS account (provided by the instructor)
- Access to a ThousandEyes SaaS account (provided by the instructor)
- Microsoft Remote Desktop application on your laptop
- VPN access to Cisco's DMZ Lab

.. sectionauthor:: Yossi Meloch <ymeloch@cisco.com>, Jairo Leon <jaileon@cisco.com>, Ovesnel Mas Lara <omaslara@cisco.com>
