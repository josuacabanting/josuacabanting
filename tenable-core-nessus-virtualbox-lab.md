# ![Tenable Core and Nessus installation lab](./tenable-lab-header-logo.svg)

This lab documents the deployment of Tenable Core with Nessus on Oracle Linux 8 in Oracle VirtualBox. It covers virtual machine creation, appliance installation, network configuration, scanner setup, validation, and recovery.

The goal is to prepare a vulnerability scanner for a controlled lab environment. This guide does not include scan results or claim that any target has been assessed.

## Environment

| Component | Lab configuration |
| :--- | :--- |
| Hypervisor | Oracle VirtualBox |
| Guest | Tenable Core with Nessus on Oracle Linux 8, x86_64 |
| CPU | 4 vCPU initially; 8 vCPU if host capacity allows |
| Memory | 8 GB initially; 16 GB if host capacity allows |
| Virtual disk | 100 GB dynamically allocated VDI |
| Appliance administration | HTTPS on TCP 8000 |
| Nessus administration | HTTPS on TCP 8834 |

These allocations describe this lab, rather than universal minimum requirements. Check the requirements for the image and scanner version you intend to deploy. VirtualBox is the hypervisor used in this walkthrough; consult Tenable's supported platforms before choosing a production deployment.

## Values to supply locally

Use your own values wherever a placeholder appears. Angle brackets indicate placeholders and should not be entered literally. Configuration examples use color highlighting: the quoted values are the settings you replace. These examples show field values, not commands to run.

| Placeholder | Purpose |
| :--- | :--- |
| `<LAB_SCANNER_IP>` | Address assigned to the lab appliance |
| `<LAB_PREFIX_LENGTH>` | Network prefix for the lab subnet |
| `<LAB_GATEWAY>` | Gateway for the lab network, if needed |
| `<LAB_DNS_SERVER>` | Resolver reachable from the appliance |
| `<LOCAL_ADMIN_USERNAME>` | Administrator account created for your lab |
| `<LOCAL_ADMIN_PASSWORD>` | Unique password kept in your password manager |
| `<SCANNER_LINKING_KEY>` | Private linking key, only when using a managed deployment |

No working credentials, usernames, keys, addresses, or customer identifiers are supplied in this guide. The screenshots below have sensitive fields blurred and image metadata removed. They illustrate the recorded installation; screen versions, defaults, and setup order may differ. Follow the written lab configuration when reproducing the procedure.

## 1 Prepare the installer

1. Obtain the Tenable Core with Nessus ISO from [Tenable Downloads](https://www.tenable.com/downloads/tenable-appliance).
2. Select the image appropriate for your architecture and deployment. This walkthrough follows an Oracle Linux 8 ISO installation.
3. Verify the download against the publisher's available integrity information.
4. Save the ISO where VirtualBox can access it.


<details>
<summary>Download screenshots</summary>

**Tenable Core download page with account details blurred**

![Tenable Core download page with account details blurred](./nessus-lab-05.png)

**Selecting the installation ISO**

![Selecting the installation ISO](./nessus-lab-06.png)

**Installer download license review dialog**

![Installer download license review dialog](./nessus-lab-07.png)

</details>

## 2 Create the virtual machine

1. In VirtualBox, select **New** and use a generic name such as `nessus-lab`.
2. Select **Linux** and the matching **Oracle Linux 64-bit** guest type.
3. Attach the installation ISO. Select **Skip Unattended Installation** when available so the appliance installer controls setup.
4. Allocate the CPU, memory, and disk capacity listed above, subject to the requirements of your selected image.
5. Create a dynamically allocated VDI disk dedicated to this lab VM.


<details>
<summary>Virtual machine setup screenshots</summary>

**VirtualBox Manager**

![VirtualBox Manager](./nessus-lab-08.png)

**Virtual machine name and operating system with local path blurred**

![Virtual machine name and operating system with local path blurred](./nessus-lab-10.png)

**Selecting the ISO with personal paths blurred**

![Selecting the ISO with personal paths blurred](./nessus-lab-11.png)

**Skipping unattended installation with local paths blurred**

![Skipping unattended installation with local paths blurred](./nessus-lab-12.png)

**Memory and processor allocation**

![Memory and processor allocation](./nessus-lab-13.png)

**Virtual disk configuration with local path blurred**

![Virtual disk configuration with local path blurred](./nessus-lab-14.png)

</details>

## 3 Configure lab networking

Use a dedicated lab network that permits administration and access to explicitly authorized test systems. The source installation workflow uses a bridged adapter; select the intended physical interface if reproducing that setup. A bridged VM joins that network, so choose a suitable lab segment.

For an isolated setup, use host-only or internal networking as appropriate, with a separate controlled route for updates if needed. NAT can affect scanner visibility and behavior, so account for that when choosing the topology.

Keep promiscuous mode at its default unless a specific lab requirement calls for it. Do not expose the administration interfaces to the public internet.


<details>
<summary>Network adapter screenshot</summary>

**VirtualBox network adapter selection**

![VirtualBox network adapter selection](./nessus-lab-15.png)

</details>

## 4 Install Tenable Core

1. Start the VM and select **Install Tenable Core**.
2. Open **Installation Destination** and select only the new virtual disk created for this lab.
3. Use the partition layout appropriate for your image and security requirements.
4. If the installer requests space reclamation, verify the selected disk belongs to this disposable lab VM before removing any partitions. Do not select host disks or disks containing data you need.
5. Resolve installation warnings before continuing. Review security-profile partition requirements rather than dismissing them automatically.
6. Begin installation and allow it to complete without powering off the VM.
7. Reboot, detach the ISO from the virtual optical drive, and boot from the installed disk.


<details>
<summary>Installation screenshots</summary>

**Tenable Core boot menu**

![Tenable Core boot menu](./nessus-lab-16.png)

**Installer boot messages**

![Installer boot messages](./nessus-lab-17.png)

**Installation summary**

![Installation summary](./nessus-lab-18.png)

**Selecting the installation disk**

![Selecting the installation disk](./nessus-lab-19.png)

**Space reclamation prompt**

![Space reclamation prompt](./nessus-lab-20.png)

**Reviewing installation options**

![Reviewing installation options](./nessus-lab-21.png)

**Beginning installation**

![Beginning installation](./nessus-lab-22.png)

**Installation progress**

![Installation progress](./nessus-lab-23.png)

**Virtual optical drive configuration with local path blurred**

![Virtual optical drive configuration with local path blurred](./nessus-lab-24.png)

</details>

## 5 Configure the appliance address

Record the address displayed on the appliance console. If the lab needs a static address, open the console's network configuration workflow and edit the appropriate interface.

Supply the following values for your lab:

```yaml
IPv4 address: "<LAB_SCANNER_IP>/<LAB_PREFIX_LENGTH>"
Gateway:      "<LAB_GATEWAY>"
DNS server:   "<LAB_DNS_SERVER>"
```

Save the configuration and reconnect or reboot if required by the appliance. Confirm that your administration workstation can reach the configured lab address.


<details>
<summary>Appliance networking screenshots</summary>

**First boot console with host and login details blurred**

![First boot console with host and login details blurred](./nessus-lab-25.png)

**Static address setup prompt with host and login details blurred**

![Static address setup prompt with host and login details blurred](./nessus-lab-26.png)

**Selecting the network connection**

![Selecting the network connection](./nessus-lab-28.png)

**Editing IPv4 configuration with network identifiers blurred**

![Editing IPv4 configuration with network identifiers blurred](./nessus-lab-29.png)

</details>

## 6 Configure administrative access

Open the appliance interface:

```yaml
Tenable Core URL: "https://<LAB_SCANNER_IP>:8000"
```

Complete the first-login workflow described for your image. If the appliance presents a self-signed certificate, verify the endpoint and certificate through a trusted channel before establishing trust.

Create a dedicated administrator account with your own `<LOCAL_ADMIN_USERNAME>` and `<LOCAL_ADMIN_PASSWORD>`. Store the password securely and complete any required first-login credential changes. Avoid publishing credentials in notes, screenshots, or command history.

```yaml
Administrator username: "<LOCAL_ADMIN_USERNAME>"
Administrator password: "<LOCAL_ADMIN_PASSWORD>"
```


<details>
<summary>Appliance access screenshots</summary>

**Appliance login page with account and host details blurred**

![Appliance login page with account and host details blurred](./nessus-lab-31.png)

**Appliance administrator login with account and host details blurred**

![Appliance administrator login with account and host details blurred](./nessus-lab-33.png)

</details>

## 7 Set up Nessus

Open the Nessus interface:

```yaml
Nessus URL: "https://<LAB_SCANNER_IP>:8834"
```

Select the deployment mode appropriate for your lab and entitlement. For a scanner managed by Tenable Vulnerability Management:

1. In your own Tenable account, open the scanner or sensor management workflow.
2. Retrieve the linking key through that account's authorized workflow.
3. Select the managed-scanner option in Nessus and enter `<SCANNER_LINKING_KEY>` locally.
4. Confirm that the scanner connects to the intended manager.

```yaml
Scanner linking key: "<SCANNER_LINKING_KEY>"
```

A linking key is a secret. Do not put the actual value in this repository. For standalone deployments, follow the applicable activation workflow instead; a managed-scanner linking key and a standalone activation code serve different purposes.

Allow plugins and initial configuration to finish before assessing scanner readiness.


<details>
<summary>Nessus setup and linking screenshots</summary>

**Tenable workspace navigation**

![Tenable workspace navigation](./nessus-lab-01.png)

**Settings and sensor management**

![Settings and sensor management](./nessus-lab-02.png)

**Scanner management page with account data blurred**

![Scanner management page with account data blurred](./nessus-lab-03.png)

**Linking key location with the key and command values blurred**

![Linking key location with the key and command values blurred](./nessus-lab-04.png)

**Nessus account creation with credentials blurred**

![Nessus account creation with credentials blurred](./nessus-lab-34.png)

**Nessus setup welcome page**

![Nessus setup welcome page](./nessus-lab-35.png)

**Choosing a managed deployment**

![Choosing a managed deployment](./nessus-lab-36.png)

**Managed scanner linking form**

![Managed scanner linking form](./nessus-lab-38.png)

**Linking key entry with the secret blurred**

![Linking key entry with the secret blurred](./nessus-lab-39.png)

</details>

## 8 Validate the installation

Use this checklist when reproducing the lab. These are validation steps, not a record of tests performed by this public guide.

- [ ] Tenable Core administration loads on TCP 8000 from the permitted workstation.
- [ ] Nessus administration loads on TCP 8834 from the permitted workstation.
- [ ] The dedicated administrator account works.
- [ ] The network address, route, DNS, and system time are correct.
- [ ] Required updates and plugins have completed successfully.
- [ ] The intended scan templates are available for the selected deployment.
- [ ] A managed scanner appears in the intended manager, if linking was configured.
- [ ] Access to the management interfaces is restricted to the intended lab users and network.

Only scan systems you own or have explicit authorization to assess. Define the target scope before starting a scan.


<details>
<summary>Scanner validation screenshot</summary>

**Linked scanner validation with hostnames and addresses blurred**

![Linked scanner validation with hostnames and addresses blurred](./nessus-lab-40.png)

</details>

## 9 Recovery

After validating a clean installation, power off the VM and create a clearly named baseline snapshot. Snapshots can contain credentials and other secrets; keep them private.

If installation fails, preserve any logs needed for troubleshooting, then rebuild only the disposable lab VM with the correct image and sufficient resources. If connectivity fails, check the VirtualBox adapter, guest address, prefix, gateway, DNS, routing, and firewall rules before changing settings.

Restore or rebuild according to the lab's recovery needs. A VM snapshot does not necessarily reverse scanner registration or other changes made in an external management service.

## References

- [Tenable](https://www.tenable.com/). The Tenable logo is used to identify the product vendor. This is an independent lab write-up.

- [Tenable Nessus documentation](https://docs.tenable.com/Nessus.htm)
- [Tenable Nessus system requirements](https://docs.tenable.com/nessus/Content/SystemRequirements.htm)
- [Tenable sensor security and administration ports](https://docs.tenable.com/vulnerability-management/Content/Settings/Sensors/SensorSecurity.htm)
- [Oracle VirtualBox documentation](https://www.virtualbox.org/wiki/Documentation)
