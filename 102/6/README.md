# 6. Linux as a virtualization guest

Should understand the implications of virtualization and cloud computing on a Linux guest system.

## Key knowledge areas

* Understand general concept of virtual machines and containers.
* Understand common elements of virtual machines in an IaaS cloud, such as computing instances, block sotrages and networking.
* Understand unique properties of a Linux system which have to be changed when a system is cloned or used as a template.
* Understand how system images are used to deploy virtual machines, cloud instances and containers.
* Understand Linux extensions which integrate Linux with a virtualization product.
* * Awareness of cloud-init.

## Partial list of used files, terms and utilities

* Virtual machine
* Linux container
* Application container
* Guest drivers
* SSH host keys
* D-Bus machine id

## TOC

* Virtualization
* Hypervisor or VMM (Virtual Machine Monitor)
* Virtual machine properties
* System images / templates
  * machine-id
* Containers (Docker / LXC)
* IaaS (Infrastructure as a Service)
* cloud-init

### Virtualization

Virtualization relies on software to simulate hardware functionality and create a virtual computer system. This enables IT organizations to run more than one virtual system – and multiple operating systems and applications – on a single server. The resulting benefits include economies of scale and greater efficiency.

A virtual computer system is known as a “virtual machine” (VM): a tightly isolated software container with an operating system and application inside. Each self-contained VM is completely independent. Putting multiple VMs on a single computer enables several operating systems and applications to run on just one physical server, or “host.”

### Hypervisor / VMM

A thin layer of software called a “hypervisor” decouples the virtual machines from the host and dynamically allocates computing resources to each virtual machine as needed.

### Virtual machine properties

* __Partitioning__
  * Run multiple operating systems on one physical machine.
  * Divide system resources between virtual machines.
* __Isolation__
  * Provide fault and security isolation at the hardware level.
  * Preserve performance with advanced resource controls.
* __Encapsulation__
  * Save the entire state of a virtual machine to files.
  * Move and copy virtual machines as easily as moving and copying files.
* __Hardware Independence__
  * Provision or migrate any virtual machine to any physical server.

### System images / templates

A system image backup is basically an exact copy ("image") of a drive -- in other words, you can use a system image to completely restore your computer, settings and all, in the event of a PC disaster. In the case of virtual machines and containers, you can crete multiple instances of your systems, with their specific configuration, easily and fast. In the case of Linux systems, there are some system identifiers that must be unique, such as MAC addresses, system id and d-bus id.

#### machine-id

This ID uniquely identifies the host. It should be considered "confidential", and must not be exposed in untrusted environments, in particular on the network. If a stable unique identifier that is tied to the machine is needed for some application, the machine ID or any part of it must not be used directly. Instead the machine ID should be hashed with a cryptographic, keyed hash function, using a fixed, application-specific key.

### Containers

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

Container images become containers at runtime and in the case of Docker containers - images become containers when they run on Docker Engine. Available for both Linux and Windows-based applications, containerized software will always run the same, regardless of the infrastructure. Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging.

#### Container vs Virtual Machine

Containers are an abstraction at the app layer that packages code and dependencies together. Multiple containers can run on the same machine and share the OS kernel with other containers, each running as isolated processes in user space. Containers take up less space than VMs (container images are typically tens of MBs in size), can handle more applications and require fewer VMs and Operating systems.

Virtual machines (VMs) are an abstraction of physical hardware turning one server into many servers. The hypervisor allows multiple VMs to run on a single machine. Each VM includes a full copy of an operating system, the application, necessary binaries and libraries - taking up tens of GBs. VMs can also be slow to boot.

### IaaS

Infrastructure as a service (IaaS) is an instant computing infrastructure, provisioned and managed over the internet. It’s one of the four types of cloud services, along with software as a service (SaaS), platform as a service (PaaS), and serverless.

IaaS quickly scales up and down with demand, letting you pay only for what you use. It helps you avoid the expense and complexity of buying and managing your own physical servers and other datacenter infrastructure. Each resource is offered as a separate service component, and you only need to rent a particular one for as long as you need it.

### cloud-init

Cloud images are operating system templates and every instance starts out as an identical clone of every other instance. It is the user data that gives every cloud instance its personality and cloud-init is the tool that applies user data to your instances automatically.

Cloud-init is the industry standard multi-distribution method for cross-platform cloud instance initialization. It is supported across all major public cloud providers, provisioning systems for private cloud infrastructure, and bare-metal installations.
