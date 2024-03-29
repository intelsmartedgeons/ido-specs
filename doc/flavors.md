```text
SPDX-License-Identifier: Apache-2.0
Copyright (c) 2020 Intel Corporation
```
<!-- omit in toc -->
# Smart Edge Open Deployment Flavors
This document introduces the supported deployment flavors that are deployable through the Converged Edge Experience Kits (CEEK).

- [Smart Edge Open Experience Kit Minimal Flavor](#smart-edge-open-experience-kit-minimal-flavor)
- [Smart Edge Open Experience Kit Access Edge Flavor](#smart-edge-open-experience-kit-access-edge-flavor)
- [Smart Edge Open Experience Kit Media Analytics Flavor](#smart-edge-open-experience-kit-media-analytics-flavor)
- [Smart Edge Open Experience Kit Media Analytics Flavor with VCAC-A](#smart-edge-open-experience-kit-media-analytics-flavor-with-vcac-a)
- [Smart Edge Open Experience Kit CDN Transcode Flavor](#smart-edge-open-experience-kit-cdn-transcode-flavor)
- [Smart Edge Open Experience Kit CDN Caching Flavor](#smart-edge-open-experience-kit-cdn-caching-flavor)
- [Smart Edge Open Experience Kit Core Control Plane Flavor](#smart-edge-open-experience-kit-core-control-plane-flavor)
- [Smart Edge Open Experience Kit Core User Plane Flavor](#smart-edge-open-experience-kit-core-user-plane-flavor)
- [Smart Edge Open Experience Kit Untrusted Non3gpp Access Flavor](#smart-edge-open-experience-kit-untrusted-non3gpp-access-flavor)
- [Smart Edge Open Experience Kit 5G Near Edge Flavor](#smart-edge-open-experience-kit-5g-near-edge-flavor)
- [Smart Edge Open Experience Kit 5G Central Office Flavor](#smart-edge-open-experience-kit-5g-central-office-flavor)
- [Central Orchestrator Flavor](#central-orchestrator-flavor)
- [Smart Edge Open Experience Kit SD-WAN Edge Flavor](#smart-edge-open-experience-kit-sd-wan-edge-flavor)
- [Smart Edge Open Experience Kit SD-WAN Hub Flavor](#smart-edge-open-experience-kit-sd-wan-hub-flavor)

## Smart Edge Open Experience Kit Minimal Flavor

The pre-defined *minimal* deployment flavor provisions the minimal set of configurations for bringing up the Smart Edge Open network edge deployment.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `minimal`
    ```yaml
    ---
    all:
      vars:
        cluster_name: minimal_cluster
        flavor: minimal
    ... 
    ```
3. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:
* Node feature discovery
* The default Kubernetes CNI: `calico`
* Telemetry

To customize this flavor we recommend creating additional file in converged-edge-experience-kits that will override any variables used in previous configuration. This file should be placed in location: `converged-edge-experience-kits/inventory/default/group_vars/all` and filenames should start with number greater than highest value currently present (e.g. `40-overrides.yml`).


## Smart Edge Open Experience Kit Access Edge Flavor

The pre-defined *flexran* deployment flavor provisions an optimized system configuration for vRAN workloads on Intel® Xeon® platforms. It also provisions for deployment of Intel® FPGA Programmable Acceleration Card (Intel® FPGA PAC) N3000 tools and components to enable offloading for the acceleration of FEC (Forward Error Correction) to the FPGA.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Configure the flavor file to reflect desired deployment.
   - Configure the CPUs selected for isolation and OS/K8s processes from command line in files [controller_group.yml](https://github.com/smart-edge-open/ido-converged-edge-experience-kits/blob/master/flavors/flexran/controller_group.yml) and [edgenode_group.yml](https://github.com/smart-edge-open/ido-converged-edge-experience-kits/blob/master/flavors/flexran/edgenode_group.yml) - please note that in single node mode the edgenode_group.yml is used to configure the CPU isolation.
   - Configure which CPUs are to be reserved for K8s and OS from K8s level with `reserved_cpu` flag in [all.yml](https://github.com/smart-edge-open/ido-converged-edge-experience-kits/blob/master/flavors/flexran/all.yml) file.
   - Configure whether the FPGA or eASIC support for FEC is desired or both in [all.yml](https://github.com/smart-edge-open/ido-converged-edge-experience-kits/blob/master/flavors/flexran/all.yml) file.

3. Provide necessary files:
   - Create the `ido-converged-edge-experience-kits/ceek/biosfw` directory and copy the `syscfg_package.zip` file to the directory (can be disabled with `ne_biosfw_enable` flag).
   - Create the `ido-converged-edge-experience-kits/ceek/opae_fpga` directory and copy the OPAE_SDK_1.3.7-5_el7.zip to the directory (can be disabled with `ne_opae_fpga_enable` flag)
   - Create the `ido-converged-edge-experience-kits/ceek/nic_drivers` directory and copy the `ice-1.3.2.tar.gz` and `iavf-4.0.2.tar.gz` files to the directory (can be disabled with `e810_driver_enable` flag).

4. Update the `inventory.yaml` file by setting the deployment flavor as `flexran`
    ```yaml
    ---
    all:
      vars:
        cluster_name: flexran_cluster
        flavor: flexran
    ... 
    ```

5. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```
This deployment flavor enables the following ingredients:
* Node Feature Discovery
* SRIOV device plugin with FPGA configuration
* Calico CNI
* Telemetry
* FPGA remote system update through OPAE
* FPGA configuration
* eASIC ACC100 configuration
* E810 and IAVF kernel driver update
* RT Kernel
* Topology Manager
* RMD operator

## Smart Edge Open Experience Kit Media Analytics Flavor

The pre-defined *media-analytics* deployment flavor provisions an optimized system configuration for media analytics workloads on Intel® Xeon® platforms. It also provisions a set of video analytics services based on the [Video Analytics Serving](https://github.com/intel/video-analytics-serving) for analytics pipeline management and execution.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `media-analytics`
    ```yaml
    ---
    all:
      vars:
        cluster_name: media_analytics_cluster
        flavor: media-analytics
    ... 
    ```
3. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

> **NOTE:** The video analytics services integrates with the Smart Edge Open service mesh when the flag `ne_istio_enable: true` is set.
> **NOTE:** Kiali management console username can be changed by editing the variable `istio_kiali_username`. By default `istio_kiali_password` is randomly generated and can be retirieved by running `kubectl get secrets/kiali -n istio-system -o json | jq -r '.data.passphrase' | base64 -d` on the Kubernetes controller.
> **NOTE:** Istio deployment can be customized using parameters in the `flavor/media-analytics/all.yaml` (parameters set in the flavor file override default parameters set in `inventory/default/group_vars/all/10-default.yml`).

This deployment flavor enables the following ingredients:
* Node feature discovery
* The default Kubernetes CNI: `calico`
* Video analytics services
* Telemetry
* Istio service mesh - conditional
* Kiali management console - conditional

## Smart Edge Open Experience Kit Media Analytics Flavor with VCAC-A

The pre-defined *media-analytics-vca* deployment flavor provisions an optimized system configuration for media analytics workloads leveraging Visual Cloud Accelerator Card for Analytics (VCAC-A) acceleration.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Add the VCA host name in the `edgenode_vca_group:` group in `inventory.yml` file of the CEEK, e.g:
    ```yaml
    edgenode_vca_group:
      hosts:
        vca-node01.openness.org:
        ansible_host: 172.16.0.1
        ansible_user: openness
    ```
    > **NOTE:** The VCA host name should *only* be placed once in the `inventory.yml` file and under the `edgenode_vca_group:` group. 

3. Update the `inventory.yaml` file by setting the deployment flavor as `media-analytics-vca`
    ```yaml
    ---
    all:
      vars:
        cluster_name: media_analytics_vca_cluster
        flavor: media-analytics-vca
    ... 
    ```

4. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

> **NOTE:** At the time of writing this document, *Weave Net*\* is the only supported CNI for network edge deployments involving VCAC-A acceleration. The `weavenet` CNI is automatically selected by the *media-analytics-vca*. 
> **NOTE:** The flag `force_build_enable` (default true) supports force build VCAC-A system image (VCAD) by default, it is defined in flavors/media-analytics-vca/all.yml. By setting the flag as false, CEEK will not rebuild the image and re-use the last system image built during deployment. If the flag is true, CEEK will force build VCA host kernel and node system image which will take several hours.

This deployment flavor enables the following ingredients:
* Node feature discovery
* VPU and GPU device plugins
* HDDL daemonset
* The `weavenet` Kubernetes CNI
* Telemetry

## Smart Edge Open Experience Kit CDN Transcode Flavor

The pre-defined *cdn-transcode* deployment flavor provisions an optimized system configuration for Content Delivery Network (CDN) transcode sample workloads on Intel® Xeon® platforms.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `cdn-transcode`
    ```yaml
    ---
    all:
      vars:
        cluster_name: cdn_transcode_cluster
        flavor: cdn-transcode
    ... 
    ```
3. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:
* Node feature discovery
* The default Kubernetes CNI: `calico`
* Telemetry

## Smart Edge Open Experience Kit CDN Caching Flavor

The pre-defined *cdn-caching* deployment flavor provisions an optimized system configuration for CDN content delivery workloads on Intel® Xeon® platforms.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `cdn-caching`
    ```yaml
    ---
    all:
      vars:
        cluster_name: cdn_caching_cluster
        flavor: cdn-caching
    ... 
    ```
3. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:
* Node feature discovery
* The `kube-ovn` and `sriov` Kubernetes CNI
* Telemetry
* Kubernetes Topology Manager policy: `single-numa-node`

## Smart Edge Open Experience Kit Core Control Plane Flavor

The pre-defined Core Control Plane flavor provisions the minimal set of configurations for 5G Control Plane Network Functions on Intel® Xeon® platforms.

The following are steps to install this flavor:

1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `core-cplane`
    ```yaml
    ---
    all:
      vars:
        cluster_name: core_cplane_cluster
        flavor: core-cplane
    ... 
    ```
3. Run ido-CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:

- Node feature discovery
- The default Kubernetes CNIs: calico, sriov
- Telemetry
- Smart Edge Open 5G Microservices
- OAM(Operation, Administration, Maintenance) and AF(Application Function) on the Smart Edge Open Controller/K8S Master.
- Reference NEF(Network Exposure Function) and CNTF(Core Network Test Function) on the Smart Edge Open Edge Nodes/K8S Node.
- Istio service mesh
- Kiali management console

> **NOTE:** It is an expectation that the `core-cplane` deployment flavor is done for a setup consisting of *at least one* Smart Edge Open edge node, i.e: the `inventory/default/inventory.ini` must contain at least one host name under the `edgenode_group` section.

> **NOTE:** For a real deployment with the 5G Core Network Functions the NEF and CNTF can be uninstalled using helm charts. Refer to [Smart Edge Open using CNCA](applications-onboard/using-smartedge-open-cnca.md)

> **NOTE:** Istio service mesh is enabled by default in the `core-cplane` deployment flavor. To deploy 5G CNFs without Istio, the flag `ne_istio_enable` in `flavors/core-cplane/all.yml` must be set to `false`.

## Smart Edge Open Experience Kit Core User Plane Flavor

The pre-defined Core Control Plane flavor provisions the minimal set of configurations for a 5G User Plane Function on Intel® Xeon® platforms.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `core-uplane`
    ```yaml
    ---
    all:
      vars:
        cluster_name: core_uplane_cluster
        flavor: core-uplane
    ... 
    ```
3. Run ido-CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:

- Node feature discovery
- Kubernetes CNI: calico and SRIOV.
- CPU Manager for Kubernetes (CMK) with 4 exclusive cores (1 to 4) and 1 core in shared pool.
- Kubernetes Device Plugin
- Telemetry
- HugePages of size 1Gi and the amount of HugePages as 8G for the nodes

> **NOTE**: For a reference UPF deployment, refer to [5G UPF Edge App](https://github.com/smart-edge-open/edgeapps/tree/master/network-functions/core-network/5G/UPF)

## Smart Edge Open Experience Kit Untrusted Non3gpp Access Flavor

The pre-defined Untrusted Non3pp Access flavor provisions the minimal set of configurations for a 5G Untrusted Non3gpp Access Network Functions like Non3GPP Interworking Function(N3IWF) on Intel® Xeon® platforms.

The following are steps to install this flavor:

1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `untrusted-non3pp-access`
    ```yaml
    ---
    all:
      vars:
        cluster_name: untrusted_non3pp_access_cluster
        flavor: untrusted-non3pp-access
    ... 
    ```
3. Run ido-CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:

- Node feature discovery
- Kubernetes CNI: calico and SR-IOV.
- Kubernetes Device Plugin
- Telemetry
- HugePages of size 1Gi and the amount of HugePages as 10G for the nodes

## Smart Edge Open Experience Kit 5G Near Edge Flavor

The pre-defined Smart Edge Open Experience Kit Near Edge flavor provisions the required set of configurations for a 5G Converged Edge Reference Architecture for Near Edge deployments on Intel® Xeon® platforms.

The following are steps to install this flavor:
1. Configure the CEEK under Smart Edge Open Experience Kit repository as described in the [Converged Edge Reference Architecture Near Edge](https://github.com/smart-edge-open/ido-specs/blob/master/doc/reference-architectures/Smartedge-Open-Experience-Kit-Near-Edge.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `cera_5g_near_edge`
    ```yaml
    ---
    all:
      vars:
        cluster_name: cera_5g_near_edge_cluster
        flavor: cera_5g_near_edge
        single_node_deployment: true
    ... 
    ```
3. Run ido-CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:

- Kubernetes CNI: Calico and SRIOV.
- SR-IOV support for kube-virt
- Virtual Functions
- CPU Manager for Kubernetes (CMK) with 16 exclusive cores and 1 core in shared pool.
- Kubernetes Device Plugin
- BIOSFW feature
- Telemetry
- HugePages of size 1Gi and the amount of HugePages as 8G for the nodes
- RMD operator

## Smart Edge Open Experience Kit 5G Central Office Flavor

The pre-defined Smart Edge Open Experience Kit 5g Central Office flavor provisions the required set of configurations for a 5G Converged Edge Reference Architecture for Core Network applications deployments on Intel® Xeon® platforms.

The following are steps to install this flavor:
1. Configure the CEEK under Smart Edge Open Experience Kit repository as described in the [Converged Edge Reference Architecture Near Edge](reference-architectures/Smartedge-Open-Experience-Kit-Near-Edge.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `cera_5g_central_office`
    ```yaml
    ---
    all:
      vars:
        cluster_name: cera_5g_central_office_cluster
        flavor: cera_5g_central_office
        single_node_deployment: true
    ... 
    ```
3. Run ido-CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:

- Kubernetes CNI: Calico and SRIOV.
- SRIOV device plugin
- Virtual Functions
- Kubernetes Device Plugin
- BIOSFW feature
- HugePages of size 8Gi and the amount of HugePages as 40G for the nodes

## Central Orchestrator Flavor

Central Orchestrator Flavor is used to deploy EMCO.  

The pre-defined *orchestration* deployment flavor provisions an optimized system configuration for emco (central orchestrator) workloads on Intel Xeon servers. It also provisions a set of central orchestrator services for [edge, multiple clusters orchestration](building-blocks/emco/smartedge-open-emco.md).

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Update the `inventory.yaml` file by setting the deployment flavor as `central_orchestrator`
    ```yaml
    ---
    all:
      vars:
        cluster_name: central_orchestrator_cluster
        flavor: central_orchestrator
    ... 
    ```
3. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This deployment flavor enables the following ingredients:
* Harbor Registry
* The default Kubernetes CNI: `calico`
* EMCO services

## Smart Edge Open Experience Kit SD-WAN Edge Flavor

Smart Edge Open Experience Kit SD-WAN Edge flavor is used to deploy SD-WAN on the Smart Edge Open cluster acting as an Edge platform. This Smart Edge Open Experience Kit flavor only supports single-node Smart Edge Open deployments. It provides configuration that supports running SD-WAN CNFs on the Smart Edge Open cluster, enables hardware accelerators with the HDDL plugin, and adds support for service mesh and node feature disovery to aid other applications and services runing on the Edge node. This Smart Edge Open Experience Kit flavor disbless EAA, Kafka adn Edge DNS services for platform optimization.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Configure the CNF as described in [Converged Edge Reference Architecture for SD-WAN](reference-architectures/smartedge-open-experience-kit_sdwan.md#ewo-configuration).
3. Update the `inventory.yaml` file by setting the deployment flavor as `sdewan-edge`
    ```yaml
    ---
    all:
      vars:
        cluster_name: sdewan_edge_cluster
        flavor: sdewan-edge
        single_node_deployment: true
    ... 
    ```
4. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This Smart Edge Open Experience Kit flavor enables the following deployment configuration:
* Istio servise mesh on the default namespace
* Node Feature Discovery
* The primamary K8s CNI: 'calico'
* The secondary K8s CNI: 'ovn4nfv'
* HDDL support
* Telemetry
* Reserved CPUs for K8s and OS daemons
* Kiali management console

This Smart Edge Open Experience Kit flavor disables the following deployment configuration:
* EAA service with Kafka
* Edge DNS

## Smart Edge Open Experience Kit SD-WAN Hub Flavor

Smart Edge Open Experience Kit SD-WAN Hub flavor is used to deploy SD-WAN on the Smart Edge Open cluster acting as a Hub for Edge clusters. It only supports single-node Smart Edge Open deployments. This Smart Edge Open Experience Kit flavor disabless EAA, Kafka and EAA services for platform optimization.

The following are steps to install this flavor:
1. Configure the CEEK as described in the [Smart Edge Open Getting Started Guide for Network Edge](getting-started/smartedge-open-cluster-setup.md).
2. Configure the CNF as described in [Converged Edge Reference Architecture for SD-WAN](reference-architectures/smartedge-open-experience-kit_sdwan.md#ewo-configuration).
3. Update the `inventory.yaml` file by setting the deployment flavor as `sdewan-hub`
    ```yaml
    ---
    all:
      vars:
        cluster_name: sdewan_hub_cluster
        flavor: sdewan-hub
        single_node_deployment: true
    ... 
    ```
4. Run CEEK deployment script:
    ```shell
    $ python3 deploy.py
    ```

This Smart Edge Open Experience Kit flavor enables the following deployment configuration:
* The primamary CNI 'calico'
* The secondary CNI 'ovn4nfv'
* Telemetry
* Reserved CPUs for K8s and OS daemons
* Kiali management console


This Smart Edge Open Experience Kit flavor disables the following deployemnt configuration:
* Node Feature Discovery
* EAA service with Kafka
* Edge DNS
* HDDL support
