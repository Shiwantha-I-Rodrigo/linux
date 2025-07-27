# ORCHESTRATING THE ENVIRONMENT

Balncing and coordinating to acieve consistency.

- a few processes that require orchestration might be,
    + Application development.
    + Configuration management.
    + Disaster recovery.
    + Server monitoring.
    + Security.

## DEVOPS

One of the popular IT orchestration processes.

- devops components.
    + Continuous integration.
    + Continuous testing.
    + Continuous delivery.
    + Infastructure as code.
    + Infastructure automation.
    + Monitoring and logging.

- Continual App Processing.
    + using git features to provide continuous small updates.
- Controlling App Environment.
    + matching development and production environments.
    + include OS / Hardware / Drivers / Libraries.
- Defining App Environment.
    + Development and production environments have predefined specifications.
    + include OS / Hardware / Software Packages / Security Measures.
- Deploying App Environment.
    + App is moved to production environment in a continual manner.
    + Ansible is used for DevOps deployment (infastructure as code automation).
- Monitoring App Environment.
    + App operations needs to be monitored and logged.
    + Performance metrics must be monitored to ensure requirements are met.
    + logged data can be used for future desicion making.
    + monitoring provide alerts to potential failures.

### ANALYSING ATTRIBUTES

- Virtualization assist in DevOps.
    + static environment.
        * environment does not change through time.
        * no sudo updates.
    + version controling.
        * prior to moving the container image to production.
        * it is registered with a version controlling system.
    + replace not update.
        * production container is stopped.
        * new container is moved into production and started.
    + high availability.
        * with the replication of containers.
        + easier to replace each one, without shutting down the whole production.

**A Real World Example**

1. development and production container is created.
2. container is registered into a version control system.
3. production container is replicated using **Kubernetes**.
4. development containers config and security policies are set with **Puppet** or **SaltStack**.
5. development team create small incremntal changes to the code.
6. these small changes are tested automatically with **Jenkins** or **CircleCI**.
7. after testing, the image is moved into version control.
8. current production image is stopped and development container takes it's place.
9. new image is monitored, and logged.

## PROVISIONING INFASTRUCTURE

Much like physical infastructure, virtual infastructure needs to be managed and controlled.

1. Determine the infastructure.
    + Operating system.
    + Libraries.
    + Services.
    + Security configuration.
    + Supporting software.
    + Network utilities.

2. Document the infastructure.
    + configuration and policy as code settings are loaded into infastructure as code portal.
    + called *automated configuration management*, used later in *build automation*.

3. Provide revision control.
    + infastructure as code is also inserted into an orchestration tool registry.

4. Troubleshoot the infastructure.
    + tech ops and software developers troubleshoot any problems.
    + documented configuration is compared to production using orchestration tools.

## AUTOMATING THE INFASTRUCTURE

### AUTOMATION TOOLS

- Ansible
    + by redhat.
    + remotely controled servers ( **no agent software required** ).
    + uses openSSH and Python to communicate using JSON with remote servers.
- Chef
    + ruby.
    + define server configurations.
- Puppet
    + own language.
    + define system configuration for remote servers.
    + has remote agent software in server.
- SaltStack
    + by VMware.
    + store server configuration in YAML.
- Terraform
    + own language.
    + JSON data format.
    + graph all resources, other operators can access.

#### Agent Vs Agentless

- Agent Monitoring
    + tools are ochestration utilities that require software (agent) in the app container.
    + send event (container crashing) and analysis data to other locations (monitor server).
    + ( con claims ) performance hit.
- Agentless Monitoring
    + uses preexisting / embedded software to conduct monitoring.
    + ( con claims ) inflexible.
    + ( con claims ) unnessecery hassle to maintain.

### INVENTORY

- when app containers shuts down, this triggers an event and the **desired state** is no longer met.
- **desired state** is predetermined setting of how many containers should be running.
- many orchestration utilities employ **self healing** ( auto deploy additional containers ).

### CONTAINER ORCHESTRATION TOOLS

- Kubernetes
    + by GOOGLE.
    + **k8s** term is used to refer to Kubernetes.
    + components
        * Cluster service : use YAML to deploy and manage pods.
        * Pod : contain one or more containers.
        * Worker : pod host system that uses kublet agent to communicate with cluster service.
        * YAML file : contain app containsers automated configurations and desired state settings.
    + this distributed component system allows flexibility and scalability.

- Docker Swarm
    + by DOCKER.
    + **cluster** is a group of docker containers.
    + monitor cluster health and return the cluster to desired state.
    + faster than kubernetes.

- Mesos
    + not an ochestration system.
    + distributed system kernal.
    + can create containers.

<img src="images/ci_cd_1.png">
<img src="images/ci_cd_2.png">
