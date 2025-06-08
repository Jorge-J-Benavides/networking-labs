# Multi-Switch LAG Configuration Lab

This Ansible project demonstrates the deployment and configuration of Link Aggregation Groups (LAGs) across multiple Cisco switches using different EtherChannel protocols including LACP, PAgP, and static bundling.

## Lab Topology

The lab consists of three Cisco switches interconnected with redundant links:

```
     SW1 (Root Bridge)
    /              \
   /                \
LACP              Static
 /                    \
SW2 ============== SW3
      PAgP Bundle
```

### Switch Roles
- **SW1**: Primary switch, STP root bridge (priority 4096)
- **SW2**: Secondary switch, STP secondary root (priority 8192)
- **SW3**: Tertiary switch (priority 12288)

### EtherChannel Protocols Used
- **LACP (Link Aggregation Control Protocol)**: Between SW1 and SW2
- **PAgP (Port Aggregation Protocol)**: Between SW2 and SW3
- **Static/Manual**: Between SW1 and SW3

## Network Configuration

### Management Network
- **Network**: 192.168.2.0/24
- **Gateway**: 192.168.2.1
- **Management VLAN**: 99

### Switch IP Addresses
- **SW1**: 192.168.2.11/24
- **SW2**: 192.168.2.12/24
- **SW3**: 192.168.2.13/24

### VLANs
| VLAN ID | Name       | Purpose           |
|---------|------------|-------------------|
| 10      | Users      | End user devices  |
| 20      | Servers    | Server network    |
| 30      | Voice      | VoIP traffic      |
| 99      | Management | Out-of-band mgmt  |

## Port Channel Configuration

### SW1 Configuration
- **Port-Channel 1**: LACP Active to SW2 (Gi0/1, Gi0/2)
- **Port-Channel 3**: Static bundle to SW3 (Gi0/3, Gi1/0)

### SW2 Configuration
- **Port-Channel 1**: LACP Active to SW1 (Gi0/1, Gi0/2)
- **Port-Channel 2**: PAgP Desirable to SW3 (Gi0/3, Gi1/0)

### SW3 Configuration
- **Port-Channel 2**: PAgP Auto to SW2 (Gi0/1, Gi0/2)
- **Port-Channel 3**: Static bundle to SW1 (Gi0/3, Gi1/0)

## Prerequisites

### Software Requirements
- Ansible 2.9+
- `cisco.ios` collection
- Python 3.6+

### Hardware Requirements
- 3 Cisco switches (supporting EtherChannel)
- Management network connectivity
- Console access for initial setup

### Network Requirements
- Management network (192.168.2.0/24) configured
- SSH access enabled on all switches
- Basic IP connectivity between switches

### Initial Switch Configuration

Before running the Ansible playbook, each switch must be configured with basic connectivity settings. Apply this template configuration via console connection to each switch, replacing `XX` with the appropriate switch number (11 for SW1, 12 for SW2, 13 for SW3) and password:

```cisco
hostname SW-XX
username admin privilege 15 password 7 XXXXXXXXXXXXXXXXX
ip domain-name local.lab.com
crypto key generate rsa usage-keys label sshkey modulus 2048
interface GigabitEthernet0/0
 switchport access vlan 99
 switchport mode access
 negotiation auto
!
interface Vlan99
 description Management Interface
 ip address 192.168.2.XX 255.255.255.0
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 192.168.2.1
ip ssh version 2
line vty 0 4
 login local
 transport input ssh
```

**Configuration Notes:**
- Replace `XX` with `11` for SW1, `12` for SW2, `13` for SW3
- The encrypted password `7 XXXXXXXXXXXXXXX` decodes to the password used in the Ansible vault
- This creates the management VLAN 99 and enables SSH access
- RSA keys are generated for SSH connectivity
- Default route points to the management network gateway

**Per-Switch IP Addresses:**
- **SW1**: 192.168.2.11/24
- **SW2**: 192.168.2.12/24
- **SW3**: 192.168.2.13/24


### Key Files Structure
```
lab02-multi-switch-lag/
├── README.md
├── inventory.yml           # Switch inventory and connection details
├── deploy.yml             # Main deployment playbook
├── cleanup.yml            # Configuration cleanup playbook
├── group_vars/
│   └── switches.yml       # Common switch variables
└── host_vars/
    ├── SW1.yml           # SW1-specific configuration
    ├── SW2.yml           # SW2-specific configuration
    └── SW3.yml           # SW3-specific configuration
```

### Variable Files
- **group_vars/switches.yml**: Common configuration for all switches
- **host_vars/[SWITCH].yml**: Switch-specific settings including port channels and priorities

## Usage

### Deploy Configuration
```bash
# Deploy to all switches
ansible-playbook -i inventory.yml deploy.yml

# Deploy to specific switch
ansible-playbook -i inventory.yml deploy.yml --limit SW1

# Use vault password file
ansible-playbook -i inventory.yml deploy.yml --vault-password-file .vault_pass
```

### Verification Commands

After deployment, verify the configuration using these commands on each switch:

```cisco
# Check EtherChannel status
show etherchannel summary
show etherchannel port-channel

# Verify LACP status
show lacp neighbor
show lacp interface

# Verify PAgP status
show pagp neighbor
show pagp interface

# Check spanning tree
show spanning-tree
show spanning-tree vlan 10

# Verify VLAN configuration
show vlan brief
show interfaces trunk
```

## EtherChannel Protocols Explained

### LACP (Link Aggregation Control Protocol)
- **Standard**: IEEE 802.3ad
- **Configuration**: SW1-SW2 connection
- **Modes**: Active (both sides actively negotiate)
- **Benefits**: Standards-based, automatic failover, load balancing

### PAgP (Port Aggregation Protocol)
- **Standard**: Cisco proprietary
- **Configuration**: SW2-SW3 connection
- **Modes**: Desirable (SW2) and Auto (SW3)
- **Benefits**: Cisco-specific features, automatic negotiation

### Static/Manual EtherChannel
- **Standard**: No protocol negotiation
- **Configuration**: SW1-SW3 connection
- **Mode**: "On" (forced bundling)
- **Benefits**: No protocol overhead, deterministic behavior

## Load Balancing

The configuration uses **source-destination IP** load balancing method (`src-dst-ip`) for optimal traffic distribution across EtherChannel members.

## Spanning Tree Protocol

- **Mode**: Rapid PVST+ (rapid-pvst)
- **Root Bridge**: SW1 (priority 4096 for all VLANs)
- **Secondary Root**: SW2 (priority 8192 for all VLANs)
- **Tertiary**: SW3 (priority 12288 for all VLANs)

## Troubleshooting

### Common Issues

1. **EtherChannel not forming**:
   - Verify matching configurations on both sides
   - Check interface status (`show interfaces status`)
   - Ensure VLAN configurations match

2. **LACP not negotiating**:
   - Verify both sides are in active or active/passive mode
   - Check for mismatched speeds/duplex

3. **PAgP not working**:
   - Ensure one side is "desirable" and other is "auto" or "desirable"
   - Verify PAgP is supported on both switches

4. **Static bundle issues**:
   - Confirm both sides are set to "on" mode
   - Check for configuration mismatches

### Debug Commands
```cisco
debug etherchannel
debug lacp all
debug pagp all
show logging
```

## Security Considerations

- SSH version 2 is enforced
- VTY lines configured for SSH-only access
- Management VLAN isolated (VLAN 99)
- Encrypted passwords stored in Ansible vault

## License

This project is provided as-is for educational purposes. Please ensure compliance with your organization's networking policies.
