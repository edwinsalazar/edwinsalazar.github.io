Solution Deployment
###################




Lab Resources
=============

Use your assigned POD number to locate your Lab resources in the tables below. You will need the information in these tables for the Lab exercises in this section.

.. _app sec lab resource mapping:

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


In this task, we will:

- Instrument the intentionally vulnerable Java application `Vulnado <https://github.com/ScaleSec/vulnado>`_ deployed in the on-prem datacenter.
- Onboard the application into AppDynamics.
- Verify that the Vulnado application is up and the frontend UI is accessible from your laptop.

The demo application used in this lab is comprised of the following technologies:
    - Java for backend services
    - Java Script and HTML for front-end UI
    - Postgres Database
    - Tomcat Web Server
    - Docker 

Vulnado Application Architecture
================================

    .. image:: images/arch.png
                :width: 800
                :align: center


1. Instrument the Java Application for AppDynamics agent
========================================================

The AppDynamics Java agent is required to enable the `Application Security Visibility.`

In this task, you will be completing the following activities to enable monitoring of the application security events in AppD Secure App:

    - Copy the AppD Java agent directory to Vulnado application directory in your VM
    - Configure the Docker file
    - Create a maven package for the application

You will need the following information provided by the instructor during this exercise:

    - CXPM DMZ VPN:
            - VPN URL
            - Username
            - Password

    - Application Server VM:
        - IP Address
        - Username
        - Password

    - AppDynamics SaaS Controller:
        - Host
        - Account Name
        - Account Access Key
        - Username
        - Password
        - Port
        - Application Name
        - Node Name
        - Node Name Prefix
         
     .. note::
        :ref:`Refer lab resource mapping table to find the application details for your POD<app sec lab resource mapping>`

Please complete the following activities to instrument the Vulnado application:

#. Open an SSH connection to your Application Server VM hosted in CXPM DMZ Lab. The screenshots below are for macOS operating system. Please go through the equivalent steps if you are using Windows.

#. Change the user to root

     .. code-block:: bash

        sudo su -



    .. tip::

            Enter your ssh login password to switch the user to root 


    .. image:: images/ssh-vm.png
            :width: 800
            :align: center

   
#. Change directory to the Vulnado application directory and verify the content

    .. code-block:: bash

       cd /opt/vulnado/
       ls -l

    .. image:: images/vulnado-app-dir.png
        :width: 800
        :align: center

    .. note::
        The Java Agent installation file was previously downloaded, extracted for you and placed in the following directory ``/opt/appdynamics/appagent`` in your VM. Should you need to download the installer in the future, you can obtain it from the `AppDynamics Download Center <https://download.appdynamics.com/download/>`_.

#. Copy AppDynamics Java agent to the Vulnado application directory

    .. code-block:: bash

       cp -r /opt/appdynamics/appagent/ .
       ls -l


    .. image:: images/appd-agent.png
        :width: 800
        :align: center

#. Create the application package from the source code 

    .. code-block:: bash

       mvn package
       
    .. image:: images/app-package-build.png
        :width: 1200
        :align: center

    You will see the compiled application package has been created in the ``target`` directory

    .. code-block:: bash

        ls -l target/

    .. image:: images/target-content.png
        :width: 800
        :align: center

  
#. Configure the application deployment manifest by updating the Dockerfile located at ``/opt/vulnado/Dockerfile``.

    .. tip::
        You may use ``vi`` or ``nano`` editor to modify the Docker file.

    The following settings must be changed:
    
    - **APPDYNAMICS_CONTROLLER_HOST_NAME**: your AppDynamics SaaS controller host (provided by the instructor)
    - **APPDYNAMICS_AGENT_ACCOUNT_NAME**: Your AppDynamics account name (provided by the instructor)
    - **APPDYNAMICS_AGENT_ACCOUNT_ACCESS_KEY**: Your AppDynamics access key (provided by the instructor)
    - **APPDYNAMICS_AGENT_APPLICATION_NAME**: ``Vulnado-N`` (replace N with the podnumber assigned to you)
    - **APPDYNAMICS_AGENT_TIER_NAME**: ``Vulnado-Tier-N`` (replace N with the podnumber assigned to you)
    - **APPDYNAMICS_JAVA_AGENT_REUSE_NODE_NAME_PREFIX**: ``Vulnado_N`` (replace N with the podnumber assigned to you)

     .. code-block:: bash

        vi Dockerfile


    Don't forget to save the file after changes are done!

    You can use the following configuration file as a reference. The highlighted lines are the ones you need to change.

    .. literalinclude:: references/dockerfile_reference.txt
        :emphasize-lines: 3, 7, 9, 11, 13, 17
        :linenos:


#. Create the docker build with the latest manifest file, run the following command:

      .. code-block:: bash

          docker-compose build --no-cache

    .. image:: images/dockerbuild.png
        


2. Start the Vulnado Java application with the AppDynamics agent
================================================================


In this task, you will be completing the following activities to onboard application into AppDynamics and enable the agent to send APM data to AppD controller:

    - Start the application services with the AppDynamics agent
    - Verify the application is onboarded in the AppDynamics controller UI
    - Verify the application onboarded into the AppDynamics Secure App dashboard


#. To start the application with the AppD java agent as a docker container. Run the following command in the terminal:

    .. code-block:: bash
    
        docker-compose up &

    .. tip::
        Run this command if the docker is not running ``systemctl start docker``

    You will see the console logs as below:

    .. image:: images/application-start.png
        :align: center

#. Verify that the docker process is running for the application

    .. code-block:: bash
    
        docker ps

    .. tip::
            Hit the enter key if it has not returned to the # prompt.

    .. image:: images/docker-process.png
        :align: center

#. Verify the Vulnado application by accessing the application URL in the following format:

    ``http://your-application-server-ip:1337/login.html`` 
    
    .. note::
        Replace ``your-application-server-ip`` with the IP of the application server VM assigned to your pod. Please refre the lab resource table to find the IP. 

    You can log in to the Vulnado application with the credentials provided by your instructor.


#. As you have started your application with the AppD agent, now you can verify that the application is onboarded into the AppD. 

   Let's `open the AppDynamics controller UI <https://cisco-cx-ps-lab.saas.appdynamics.com/controller/>`_, log in using the AppDynamics account, username and password provided by your instructor, and navigate to the :guilabel:`Applications` tab to confirm that your application appears in the table and the health status shown as green.

    .. image:: images/vulnado-app-list.png
        :align: center

    

    .. note::
        You can double-click the application to view the application flow map. However, please generate some traffic by running the load generator script, and it could take a few minutes for the flow map to appear in the dashboard. 

    .. tip:: 

         Open ssh session in another terminal and run the following command to execute the load generator script that you can find at `/opt/vulnado` directory. 
        
        .. code-block:: bash

            ./loadgen.sh


    



.. sectionauthor:: Ansood Anandan <anananda@cisco.com>
