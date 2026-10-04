# DC01 Network Configuration

## Overview

As part of the BluePeak Technologies IT Home Lab, DC01 was configured with a descriptive computer name and a static IPv4 address.

A static IP address is important for a domain controller because Active Directory and DNS clients need a consistent address to locate domain services.

## Computer Name Configuration

Windows Server initially assigned the server the automatically generated computer name:

`WIN-SAGOS582FM2`

The server was renamed to:

`DC01`

The server was restarted after the name change, and Server Manager was used to verify that the new computer name was successfully applied.

## Hyper-V Network

DC01 is connected to the following Hyper-V virtual network:

- Virtual Switch: BluePeak-Lab
- Switch Type: Internal
- Server: DC01

The BluePeak-Lab network is being used as the private network for the lab environment.

## Static IPv4 Configuration

DC01 was configured with the following IPv4 settings:

- IP Address: 10.10.10.10
- Subnet Mask: 255.255.255.0
- CIDR Prefix: /24
- Network: 10.10.10.0/24
- Default Gateway: None
- Preferred DNS Server: 10.10.10.10
- Alternate DNS Server: None

No default gateway was configured because the BluePeak-Lab Hyper-V Internal network does not currently have routing or NAT configured.

## DNS Configuration

DC01 was configured to use:

`10.10.10.10`

as its preferred DNS server.

This points DC01 to itself in preparation for installing Active Directory Domain Services and the DNS Server role.

## Verification

The network configuration was verified from Command Prompt.

The following command was used to verify the IPv4 configuration:

```cmd
ipconfig
ipconfig /all