# Cluster Setup

## OS Security Benchmarking
- CIS = [Center for Internet Security](https://www.cisecurity.org/)
- community-driven non-profit organization
- mission to make connected world a safer place by developing, validating, promoting timely best practice solutions
- 100+ vendor-neutral configuration guides (Azure, AWS, GCP, Linux, Windows Server, etc.)
- [CIS-CAT®Pro](https://www.cisecurity.org/cybersecurity-tools/cis-cat-pro) is a tool to automate OS security assessment and reporting

### CIS-CAT®Pro - Installation

**NOT IMPORTANT FOR EXAM**

TODO

### CIS-CAT®Pro - Usage

Display information:

	$ /bin/sh <assessor-cli-shell-path>.sh -h
> **-h** flag to show usage information

Run tool using some handy options:

	$ /bin/sh <assessor-cli-shell-path>.sh -i -rd <output-directory-path> -nts -rp <output-filename>
> **-i** flag to proceed interactively

> **-rd** flag to specify output directory path

> **-nts** flag not to include timestamp in output filename

> **-rp** flag to override default output report filename

## Kubernetes Cluster Security Benchmarking

- kube-bench is an open source tool from Aqua Security that checks whether Kubernetes is deployed securely by running the checks documented in the [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/)

### kube-bench - Installation
- as a Docker container
- as a pod in your Kubernetes cluster
- install binaries
- compile from source

[See kube-bench documentation for installation instructions](https://github.com/aquasecurity/kube-bench/blob/main/docs/installation.md#download-and-install-binaries)

### kube-bench - Usage
Run tool:

	$ ./kube-bench --config-dir <path-to-config-directory> --config <path-to-config-file>.yaml

## Network Security Policies

- a pod is non-isolated for ingress and egress by default; all in- and outbound connections are allowed
- pods are isolated from ingress (incoming traffic) or egress (outgoing traffic) if there is any NetworkPolicy that selects the pod and has `Ingress:` or `Egress:` in its `policyTypes:` field
- network policies are implemented by the network plugin; you must be using a plugin which supports NetworkPolicy
- network policies can define ip address ranges (`ipBlock:` field) to limit in- or outgoing traffic
- `namespaceSelector:` field to target namespace(s) the network policy should apply to
	- applies to the namespace the NetworkPolicy is created in by default if no `namespaceSelector:` field defined
- `podSelector:` field to target pod(s) the network policy should apply to
	- applies to all pods within the target namespace by default if no `podSelector:` field defined

[See Kubernetes documentation for more information on network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

## Ingress Objects With Security Control

- Ingress Controllers route traffic to your cluster via Ingress Resources
	- have built-in logic logic to monitor cluster for Ingress Resources
- Ingress Controllers consist of a few components:
	- Deployment
		- makes sure the controller server (e.g. nginx pod) is always running
	- Service
		- direct traffic to controller server
	- ConfigMap
		- makes it easy to configure the controller server (e.g. error log path, SSL protocols, ...)
	- Service Account
		- provide authentication
- Ingress Resources
	- set of rules & configuration which are applied to the Ingress Controller
	- can be created imperatively and declaratively
	- paths refer to a service, which refer to pod(s)
	- `rewrite-target` option
		- e.g. we want `http://<ingress-service>:<ingress-port>/watch` to redirect to `http://<watch-service>:<port>/`
			- by default this would go to `http://<watch-service>:<port>/watch`
			- specify `nginx.ingress.kubernetes.io/rewrite-target: /` to fix this

## Protect Node Metadata & Endpoints

- What is `kubelet`?
	- 'captain of the ship' on every node
	- point of contact for controlplane
	- loads & unloads containers on/off nodes
	- monitors the node and its pods, reports to `kube-apiserver`
	- registers node in the cluster to become part of it
	- not deployed by `kubeadm`
	- does not run as a pod, but as a native Linux service
	- every call to `kubelet` API must go through authentication and then authorization
- `kubelet` configuration is located at `/var/lib/kubelet/config.yaml` by default
	- check `kubelet` service `--config` flag for path to config file
	- you can override config file settings by passing them in as flags on `kubelet` service
- `kubelet` exposes HTTPS endpoints which grant powerful control over the node and containers
	- anonymous authentication is enabled by default
		- set `authentication:` `anonymous:` `enabled: false` in config file
		- or set `--anonymous-auth=false` flag on `kubelet` service
	- authorization is set to `AlwaysAllow` by default
		- `mode: AlwaysAllow` setting in config file
		- `kubelet` service flag `--authorization-mode=AlwaysAllow`
	- `kubelet` API server port 10250 allows full access
		- inspect node health, monitor containers, run containers, get pods list, exec into containers, ...
	- `kubelet` API server port 10255 allows read-only access to metrics (https://localhost:10255/metrics)
		- set `readOnlyPort: 0` in config file to disable endpoint
- recommended way to authenticate to `kubelet` API is X509 Certificates
	- set `authentication:` `x509:` `clientCAFile:` to certificate path in config file
	- `kube-apiserver` must have path to these certificates in `--kubelet-client-certificate` and `--kubelet-client-key` parameters
- remember to restart `kubelet` service after making configuration changes

## Restrict Access To Kubernetes Dashboard

- Kubernetes Dashboard is a web-based Kubernetes user interface
- can be used to get an overview, deploy and manage resources
- must be installed separately
- dashboard supports logging in with Bearer Token and KubeConfig
- create separate service account with limited rights to auth into dashboard
- deployed with ClusterIP service
    - not accessible outside of cluster by default on purpose
    - kubectl proxy and kubectl port-forward can be used to access a service not exposed to the outside world
    - OAuth2 authentication proxy can also be configured to access dashboard (out of scope here)

[See Kubernetes documentation for installation instructions](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

## Verify Platform Binaries

- attackers can intercept downloaded files and change them
- to counter this, software documentation may list hashes of files to download for installation
- generate hash of downloaded files and compare to hash in documentation so any change in the binaries won't go unnoticed
- Kubernetes lists sha512 hash for its binaries per version in its [changelog](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.30.md#client-binaries)

### Commands

Generate checksum:

	$ sha512sum <path-to-file>

# Cluster Hardening

## Restrict Access To Kubernetes API

## RBAC

## Service Accounts Best Practices

## Update Kubernetes Frequently
- frequent updates ensure that these vulnerabilities are patched promptly, reducing the risk of security breaches
- Kubernetes supports only up to the recent 3 minor versions at any time
	- e.g. v1.30, v1.29 and v1.28
- recommended approach is to upgrade Kubernetes one minor version at a time
- Kubernetes components can be at different release versions at a time
	- no component may be at a higher version than `kube-apiserver`
- control plane node(s) going down do not affect worker node pods
	- but cannot perform administrative tasks, deploy new pods, remove existing ones, etc.
- worker node upgrade strategies
	- Strategy 1
		- upgrade all worker nodes at once
			- requires downtime
	- Strategy 2
		- upgrade one node at a time
	- Strategy 3
		- provision new node with upgraded version, move workload, delete old node
			- mostly used in cloud environments where nodes can be provisioned dynamically

### Commands

Check kubeadm version:

	$ kubeadm version
	
Check kubectl version:

	$ kubectl version

Check node (kubelet) versions:

	$ kubectl get nodes

Safely evict pods from a node and mark as unschedulable:

	$ kubectl drain <node-name>

Mark node as schedulable:

	$ kubectl uncordon <node-name>

Mark node as unschedulable:

	$ kubectl cordon <node-name>

[See Kubernetes documentation for instructions on upgrading a cluster using kubeadm](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)

# System Hardening

## Minimize Host OS Footprint

## Minimize IAM Roles

## Minimize External Network Access

## Seccomp

- seccomp = secure computing mode
- security feature in Linux kernel to sandbox applications by restricting syscalls
	- syscalls = interface between application (software) and Linux kernel to system resources (hardware)
	- an application having complete access to all syscalls is a security risk and increases attack surface
		- e.g. Dirty COW vulnerability in 2016 to gain root access
	- Linux kernel will (in general) allow any syscall invoked by applications by default
- correctly determining the syscalls a container needs and blocking all others improves K8s cluster security
- 3 seccomp modes:
	- `DISABLED` (mode 0)
		- all syscalls allowed
	- `STRICT` (mode 1)
		- only allows  `read()`, `write()`, `exit()` and `sigreturn()` syscalls
	- `FILTERED` (mode 2)
		- syscalls are explicitly allowed or denied by a pre-defined list
- 2 types of seccomp profiles when choosing `FILTERED` mode:
	- whitelist type seccomp profile
		- denies every syscall by default
		- define which syscall(s) to allow
		- highly secure
		- highly restrictive
			- application will fail if syscall not in whitelist is invoked
		- hard to create as all syscalls need to be identified
	- blacklist type seccomp profile
		- allows every syscall by default
		- define which syscalls to deny
		- easier to create

### Commands

Check if Linux kernel version supports seccomp (look for `CONFIG_SECCOMP=(y|n)`):

	$ grep -i seccomp /boot/config-$(uname -r)

Check seccomp mode of a process on system/in a container:

1. Identify process id:

		$ ps -ef

2. Check seccomp mode:

		$ "grep seccomp /proc/<process-id>/status

## Seccomp In Docker

- docker has built-in seccomp filter which it applies on containers by default, provided host has seccomp enabled
	- blocks about 60 syscalls containers shouldn't be using
- profile can be extended to add/remove syscalls by passing `--security-opt seccomp=<path-to-seccomp-profile>.json` flag to the container when calling `docker run`
- default seccomp filter can be disabled by passing `--security-opt seccomp=unconfined` flag when calling `docker run`
	- should not be used
	- additional security gates provided by docker will still prevent some syscalls even if disabled

## Seccomp In Kubernetes

- seccomp is disabled by default (`Unconfined`)
- seccomp profiles are defined in pod manifests through securityContext
	- e.g. `securityContext:` `seccompProfile:` `type: Localhost` `localhostProfile: <seccomp-profile-name>`
		- profile path is relative to `/var/lib/kubelet/seccomp` by default
- set seccomp profile to default allowed/denied syscalls of container runtime by using type `RuntimeDefault`
	- e.g. `securityContext:` `seccompProfile:` `type: RuntimeDefault`
- used seccomp profiles should be copied to all K8s nodes
- to determine syscalls used by a container, create a seccomp profile with `defaultAction:` `SCMP_ACT_LOG` and check `/var/log/syslog`
- also best practice to disable privilege escalation by setting `allowPrivilegeEscalation: false` in pod `securityContext`

[See Kubernetes documentation for more information on seccomp](https://kubernetes.io/docs/tutorials/security/seccomp/)

## AppArmor

- Linux (kernel) security module
- installed by default in most Linux distributions
- used to confine programs to a limited set of resources
- while seccomp can disable `mkdir`, it cannot only disable it only for a certain path
- works with profiles, they define what Linux capabilities and resources can be used by an application
- 3 AppArmor profile modes:
	- `ENFORCE`
		- AppArmor will monitor and enforce rules on any application that fits the profile
	- `COMPLAIN`
		- AppArmor will allow, but events will be logged
	- `UNCONFINED`
		- Apparmor will allow, no logs

### Commands

Check if AppArmor is enabled:

	$ cat /sys/module/apparmor/parameters/enabled

Check AppArmor service status:

	$ systemctl status apparmor

Check which AppArmor profiles are loaded:

	$ cat /sys/kernel/security/apparmor/profiles

Display more information about loaded AppArmor profiles:

	$ aa-status

Switch AppArmor profile to `ENFORCE` mode:

	$ aa-enforce /etc/apparmor.d/<profile-filename>

Switch AppArmor profile to `COMPLAIN` mode:

	$ aa-complain /etc/apparmor.d/<profile-filename>

Switch AppArmor profile to `UNCONFINED` mode:

	$ aa-unconfined /etc/apparmor.d/<profile-filename>

## Creating AppArmor Profiles

**NOT IMPORTANT FOR EXAM**

Install AppArmor utility `app-armor-utils` to create profiles easier:

	$ apt-get install -y app-armor-utils

Generate security profile for an application:

1. Run command:

		$ aa-genprof <application-path>

2. Run application in new window
3. Press S for AppArmor event logs
4. Events are presented by AppArmor utility
5. Choose an action per event (e.g. Allow, Deny, ...)
6. Press S to save and F to finish
7. Newly generated profile will now run and is enforced on our application

## Loading AppArmor Profiles

- default AppArmor profiles directory is `/etc/apparmor.d/`

### Commands

Load AppArmor profile:

	$ apparmor_parser /etc/apparmor.d/<profile-filename>

Unload AppArmor profile:

	$ apparmor_parser -R /etc/apparmor.d/<profile-filename>

## AppArmor In Kubernetes

- AppArmor must be enabled on K8s nodes
- Container Runtime used by K8s must support AppArmor
- is supported by K8s version >= 1.4
	- still in beta right now
- as of Kubernetes v1.30, AppArmor profiles are defined in pod manifests through securityContext
	- e.g. `securityContext:` `appArmorProfile:` `type: Localhost` `localhostProfile: <apparmor-profile-name>`
- before Kubernetes v1.30, AppArmor profiles were defined in the annotation field
	- e.g. `metadata:` `annotations:` `container.apparmor.security.beta.kubernetes.io/<pod-name>: localhost/<apparmor-profile-name>`

[See Kubernetes documentation for more information on AppArmor](https://kubernetes.io/docs/tutorials/security/apparmor/)

# Microservice Vulnerabilities

## Setup Appropriate OS-level Security Domains

## Managing Kubernetes Secrets

- create secrets imperatively using `kubectl create secret`
- create secrets declaratively by base-64 encoding the secret content
	- e.g. using `base64` command
- secret content is NOT encrypted but encoded (base 64)
- best practice to enable encryption at rest in ETCD
- anyone able to create pods/deployments in a namespace can access its secrets
- a secret is only sent to a node if a pod on that node requires it
- Kubelet stores secrets into a tmpfs so that the secret is not written to disk storage
- once a Pod that depends on a secret is deleted, `kubelet` will delete its local copy of the secret data as well
- consider 3rd party secret providers
	- e.g. Azure Key Vault, GCP Provider, HashiCorp Vault, ...

## Container Runtime Sandboxing

- containers usually run on top of hardware infrastructure > host OS > hypervisor (e.g. VMware) > container
- cloud providers may host containers of multiple customers on the same infrastructure (server)
	- multi-tenant environment
- how good (secure) is this isolation?
- containers run one or more processes
- processes are isolated in their own namespace
	- host can see all processes of all containers
	- killing a process will wipe out the container
- containers make syscalls to the host OS to make use of system resources (hardware)
	- virtual machines have a dedicated OS kernel
	- containers share the same OS kernel as the host
	- security risk as exploits like Dirty COW can be used to break out of container into host
- sandboxing = general concept of isolating something from the rest
- seccomp and AppArmor profiles can provide a way to create secure, restricted containers
	- fine if you only run a handful different kind of containers
	- very tedious to create profiles for every kind of container when you can have thousand different ones running on your system
		- in come 3rd party tools
	- security is always a trade-off
	- security has advantages & disadvantages
		- the price to pay
		- no single way that is best for everything

## gVisor

- tool from Google to provide additional layer between container and OS kernel
- syscalls will now actually make a call to gVisor instead of the kernel
	- container > syscall > gVisor > OS kernel
- each container has its own dedicated gVisor kernel to talk to
	- resulting in each container being isolated in its own sandbox, drastically decreasing attack surface
- not all apps work with gVisor
	- test for yourself
- more cpu instructions since there is now a middle man
	- applications may run slower
- 2 major components:
	- Sentry
		- independent application-level kernel dedicated for containers
		- intercept and respond to syscalls made by containerized applications
		- designed with containers in mind
		- supports far fewer functionality than the actual OS kernel
			- reduces possibilities of flaws to be exploited by attackers
	- Gofer
		- file proxy which implements logic needed for containerized apps to access system files
		- middle man sitting in between container and OS
			- container > syscall > sentry > gofer > OS kernel

## Using Different Runtimes In Docker

- traditional `docker run <image>` flow:
	- Docker CLI > rest API > Docker daemon > containerd > containerd-shim > runC > namespace, CGroups, ...
- using `runC run <image>`
	- runC CLI > runC > namespace, CGroups, ...
	- very hard to manage container lifecycle, hence why Docker is used
- different runtimes other than runC can be used
	- kata containers uses `kata-runtime` instead of `runC`
	- gVisor uses `runsc` instead of `runC`
	- both runtimes are OCI compliant, which means they are compatible with Docker
		- `docker run --runtime kata <image>` command
		- `docker run --runtime runsc <image>` command

## Using Different Runtimes In Kubernetes

- create new `RuntimeClass` resource
	- handler field specifies runtime class
- specify `runTimeClassName` field in pod with `RuntimeClass` name
- container will no longer be visible as a process on Kubernetes node
	- the runtime however will now be visible as a process

## Kata Containers

- Kata separates each containers into its own virtual machine
- each container will now talk to the VM kernel instead of the OS kernel
	- container > syscall > VM kernel > syscall > OS kernel
- VM instances created by kata are lightweight and optimized for performance
	- still a performance reduction compared to traditional containers
	- each one will need more memory and cpu
- kata containers need hardware virtualization support
- cloud providers may not support it as cloud compute instances are virtual machines already
	- vm's running in vm's = nested vm's
		- very poor performance
		- not supported by many cloud providers
			- GCP has support but has to be enabled manually
	- can use kata containers on dedicated physical servers or bare metal servers (in the cloud or on prem)
		- better performance

## Pod-to-Pod Encryption Using mTLS

- One way SSL (TLS) vs Mutual SSL (mTLS)
	- one way
		- 1 entity verifies with the CA if the certificate of the target it connects to
			- e.g. user connects to web server (social media, bank website, ...)
	- mutual
		- 2 entities verify eachothers certificates to establish a secure connection
			- e.g. connection between two servers
		- entity A sends request to entity B to get its certificate
		- entity B sends certificate public key to entity A and also ask for its cert
		- entity A validates entity B's certificate, generates a symmetric key used for the future communication, and sends its own public key cert
		- entity B validates entity A's cert and keeps the symmetric key for future communication
		- mTLS has now been established and traffic is now encrypted and secure
- mTLS for Pod-to-Pod Encyption
	- by default, data transmitted between pods in a cluster is not encrypted
		- attacker sniffing network can intercept traffic
			- e.g. authentication data of a user
	- instead of making applications encrypt their data, we can use 3rd party solutions
		- called "service mesh"
			- provided by e.g. Istio, Linkerd
	- example implementing mTLS using Istio:
		- Istio inserts a sidecar container into each pod
		- sidecar intercepts message of main container before it leaves
		- sidecar encrypts message
		- sidecar in receiving pod decrypts message and passes to main container
		- 2 modes:
			- `STRICT`
				- only allow mTLS traffic
				- may cause application communication to break (especially to external services not supporting mTLS)
			- `PERMISSIVE`
				- will allow both encrypted and unencrypted data traffic
				- for example when data is sent from an external service not supporting mTLS (not part of our cluster) into a pod

# Supply Chain Security

## Minimize Base Image Footprint

- base vs parent image
	- dockerfile `debian:buster-slim` (BASE image)
		- is based on `FROM scratch`
	- dockerfile `httpd` (PARENT image)
		- is based on `FROM debian:buster-slim`
	- dockerfile `my-application`
		- is based on `FROM httpd`
- best practice to make modular images
	- build a different image for your web app, database, ...
		- each image should solve 1 problem
		- each image should only have its own libraries
	- multiple images deployed as containers can together form an application
		- each image can be scaled up/down independently
- best practice not to store state (data) inside containers
	- should be able to destroy and recreate containers
	- store data in external volume or caching service
- Docker Hub has an `OFFICIAL IMAGE` label for images from official sources
- check last update time: frequently updated images are less likely to have vulnerabilities
- slim/minimal images
	- most likely multiple versions of an image exist
	- find the slimmest image suited for your needs
		- faster to pull image
		- better performance
		- cheaper
		- more secure as attack surface is decreased and less possible vulnerabilities
		- more lightweight containers
			- minimizing the footprint to spin up multiple instances
- only install necessary packages in your image
	- remove shells/package managers/tools
- maintain different images for different environments
	- dev/test may contain debug tools while production image is lean
- Google Distroless Docker images
	- only contains application and runtime dependencies
	- does not contain package managers, shells, network tools, text editors, ...
	- e.g. gcr.io/distroless/base-debian10

## Whitelist Allowed Container Registries

- specifying `image: <image-name>` in pod definition will make it pull the image from the default container runtime repository
	- Docker Hub is the default repository for Docker, this means `image: docker.io/library/<image-name>`
- of course images can be pulled from private container registries
	- e.g. `image:<container-registry>.azurecr.io/<image>:<tag>`
	- create docker-registry secret in K8s
	- reference secret in pod manifest using `imagePullSecrets:` `name: <secret-name>` field
- using any container registry in the world to pull images from is a big security risk
	- running a malicious container image could compromise whole cluster
- set governance in place to allow pulling images from approved registries only
	- create your own Admission Controller
	- or deploy OPA service using a `.rego` policy to only allow certain registries
	- or enable the built-in `ImagePolicyWebhook` Admission Controller
		- create a config file that references a kubeconfig formatted file which sets up the connection to the backend
			- e.g. `/etc/kubernetes/pki/admission_kube_config.yaml`
			- required to communicate over TLS
			- `defaultAllow: true` means if the admission webhook server does not explicitly deny the request, it goes through
				- if the server is unavailable, the request will be allowed
			- `defaultAllow: false` means if the admission webhook server does not explicitly allow the request, it will be denied
				- if the server is unavailable, the request will be denied
		- create `AdmissionConfiguration` manifest using `ImagePolicyWebhook` plugin
			- e.g. `/etc/kubernetes/pki/admission_configuration.yaml`
			- must reference path to newly created kubeconfig file `admission_kube_config.yaml`
		- add `ImagePolicyWebhook` to `--enable-admission-plugins` in `kube-apiserver` manifest
		- add `AdmissionConfiguration` object file path to `--admission-control-config-file` in `kube-apiserver` manifest

## Sign & Validate Container Images

## Static User Workload Analysis

- review and enforce policies earlier in the development cycle
	- even before any `kubectl` command runs
- `kubesec` tool helps analyze a Kubernetes resource definition file and returns a score along with details to improve
- install `kubesec` binaries and run as a server locally, deploy in container or cURL request to `kubesec.io/scan` with `.yaml` file as data binary

### Installing kubesec Binaries

1. Go to https://kubesec.io/
2. [Find GitHub page](https://github.com/controlplaneio/kubesec/releases)
3. Download binaries:

		$ curl -L https://github.com/controlplaneio/kubesec/releases/download/v2.13.0/kubesec_linux_amd64.tar.gz -o <output-path>

4. Extract:

		$ tar -xvf  kubesec_linux_amd64.tar.gz

5. Move to binary folder:

		$ mv kubesec /usr/bin/

### Commands

Scan file using local server:

	$ kubesec scan <path-to-file>.yaml

Scan file using remote server:

	$ curl -sSX POST --data-binary @"<path-to-file>.yaml" https://v2.kubesec.io/scan

## Scan Container Images For Known Vulnerabilities

- when bad guys find vulnerabilities, they exploit them
- when good guys find vulnerabilities, they are reported and fixed
- Common Vulnerabilities and Exposures (CVE) is a publicly listed catalog of known security threats
	- with extra details such as a score how bad it is and what (version of) software is affected
- Trivy is a tool used to find CVE threats in container images
- best practices:
	- continuously re-scan images (integrate into CI/CD)
	- kubernetes Admission Controllers to scan images
	- have your own repository with pre-scanned images ready to go

### Commands

Scan container image for `CRITICAL` level vulnerabilities:

	$ trivy image --severity CRITICAL <image>:<tag>

Scan container image for `CRITICAL` and `HIGH` level vulnerabilities:

	$ trivy image --severity CRITICAL,HIGH <image>:<tag>

Scan container image and ignore unfixed vulnerabilities:

	$ trivy image --ignore-unfixed <image>:<tag>

Scan local file:

	$ docker save <image>:<tag> > <path-to-file>
	$ trivy image --input <path-to-file>

# Monitoring, Logging & Runtime Security

## Behavorial Analytics Of Syscalls

- even if we put all safeguards in place, no guarantee we will always be safe
- theoretically always a way for an attacker to get inside
	- important to monitor
	- prepare for scenario when system is compromised
	- the sooner we find out we have been hacked, the better
- monitoring syscalls of all nodes running 10s or 100s or 1000s pods is an impossible task
- Falco: cloud native runtime security tool for Linux, created to monitor syscalls and generate events based on pre-defined rules
	- designed to detect and alert abnormal behavior and potential security threats in real-time
		- e.g. anomalies like removing (parts of) logs or someone accessing files where passwords are stored
- created by Sysdig
- can send notifications to various channels
	- e.g. Slack, Teams, e-mail, etc.

### Falco Architecture

- Falco needs to see what syscalls are coming through from user space > kernel space
- therefore needs to insert itself into kernel to sit in the middle
	- does this by making use of its own kernel module: Falco Kernel Module
		- pretty intrusive
	- not all managed K8s services allow this
		- can also interact with kernel through eBPF (extended Berkeley Packet Filter) module
	- Falco will analyze the syscalls to see if they are suspicious using its own libraries in the user space
		- has rules defined which the Policy Engine processes

### Falco Installation

- installing Falco as a service
	- using Falco as a service will make sure it is isolated from Kubernetes
		- continue to detect suspicious behaviour in case cluster is compromised
	- installing Falco package will also automatically install Falco Kernel Module
- installing Falco as a DaemonSet in Kubernetes
	- deploy Helm chart

[See Falco documentation for more information on installation](https://falco.org/docs/install-operate/installation/)

### Falco Configuration

- default main config file path `/etc/falco/falco.yaml`
	- path can be modified by changing `-c` flag on Falco service
	- `rules_file` field specifies all yaml rule files
		- `/etc/falco/falco_rules.yaml` are built-in rules
			- do not change built-in rules in this file as a package update will overwrite them
			- create a `falco_rules.local.yaml` file instead and change (override) the rule there
		- if a rule is defined multiple times, rule in last file will override and apply
	- `json_output` field indicates if logs should be in json
	- `log_level` field indicates what Falco logs (not events) should generate
	- `priority` field specifies the minimum required level to create an event
- Falco needs to restart after making config changes
- Falco logs events to stdout by default by setting `stdout_output:` `enabled: true`
	- multiple output channels can be enabled
		- e.g. to a specific .txt file using `file_output:` `enabled: true` `filename: <path-to-file>.txt`
- `program_output` field can enable sending events to various channels or HTTP endpoint

### Commands

Check if service is running:

	$ systemctl status falco

Check events generated by Falco service:

	$ journalctl -fu falco

Hot reload service after configuration change:

	$ cat /var/run/falco.pid
	$ kill -1 <process-id>

## Container Immutability

- manually updating server infrastructure can be complex and cause configuration drift
	- you don't know what has changed compared to other infrastructure, generating different (unwanted) results
	- instead, infrastructure can be deployed from code with scripts/tools such as Ansible to set the state of the infrastructure for all servers
- containers should not be able to change during runtime
	- attackers could exploit this by executing into the container and change stuff around
- changing a container should be done by editing the Dockerfile or image reference and spinning up a new one
- make containers immutable by specifying `securityContext:` `readOnlyRootFilesystem: true` in pod manifest
	- pods may still need to write files to certain directories
		- check pod logs if it fails to run
		- create volumes to specific paths to make pod work again
- running containers with `privileged: true` flag can be used to still change files and persist to host server, even if `readOnlyRootFilesystem: true`
	- big security risk

## Audit Logs To Monitor Access

- Kubernetes auditing provides a security-relevant, chronological set of records documenting the sequence of actions in a cluster
	- auditing activities generated by users, by applications that use the Kubernetes API, and by the control plane itself
- allowing cluster admins to answer questions:
	- what, when, who, on what, where it was observed, from where initiated, to where it is going
- each request sent to `kube-apiserver` goes through different stages
- each request generates an audit event in each stage, audit `Policy` object defines which events should be kept
	- we do not want to log everything, but rather e.g. only deletion of pods in a certain namespace
	- currently log files and webhooks are supported
		- webhooks allow logs to be sent via Slack, Teams, your own http endpoint, ...
- request stages:
	1. `RequestReceived`
		- generated as soon as the audit handler received the request, and before it is delegated down the handler chain
	2. `ResponseStarted`
		- once response headers are sent, but before response body.
		- only generated for long-running requests (e.g. `watch`)
	3. `ResponseComplete`
		- response body has been completed
	4. `Panic`
		- generated when the request was invalid
- audit levels:
	- `None`
		- don't log events that match this rule
	- `Metadata`
		- log request metadata (requesting user, timestamp, resource, verb, etc.) but not request or response body
	- `Request`
		- log event metadata and request body but not response body. This does not apply for non-resource requests
	- `RequestResponse`
		- log event metadata, request and response bodies. This does not apply for non-resource requests

### Configure Auditing
- set `--audit-policy-file` flag on `kube-apiserver` to path of `Policy` object

### Log backend
- `--audit-log-path` specifies the log file path that log backend uses to write audit events. Not specifying this flag disables log backend. `-` means standard out
- `--audit-log-maxage` defines the maximum number of days to retain old audit log files
- `--audit-log-maxbackup` defines the maximum number of audit log files to retain
- `--audit-log-maxsize` defines the maximum size in megabytes of the audit log file before it gets rotated

[See Kubernetes documentation for more information on auditing](https://kubernetes.io/docs/tasks/debug/debug-cluster/audit/)