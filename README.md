# icinga2_cluster

This role sets up the cluster communication between Icinga 2 nodes. Currently communication is always established bidirectional.  
The role assumes **icinga2** to already be present and does **not** install the software itself.  

## Requirements

None yet.

## Role Variables

`icinga2_cluster_port`: The port on which all nodes should generally listen (default: `5665`).  
`icinga2_cluster_ca_node`: Ansible's inventory hostname of the host that is supposed to be the Icinga CA host.  
`icinga2_cluster_ca_force`: Forces the re-creation of the Icinga CA (default: `false`).  
`icinga2_cluster_cert_force`: Forces the re-creation of certificates for all nodes (default: `false`).  
`icinga2_cluster_global_zones`: A list of additional global zones to be deployed (default: `[]`).  
`icinga2_cluster_zones`: A list of dictionaries that describes your zone hierarchy (see [Example Playbook](#example-playbook)).  


## Example Playbook

```yaml
---

- hosts:
    - icinga-master.localdomain
    - icinga-satellite1.localdomain
    - icinga-satellite2.localdomain
    - icinga-satellite3.localdomain

    icinga2_cluster_ca_node: icinga-master.localdomain # This is Ansible's inventory hostname for that host
    icinga2_cluster_zones:
      - name: master # This is the name of your icinga zone
        endpoints:
          - host: icinga-master.localdomain # This is Ansible's inventory hostname for that host
            name: master # This is the endpoint name in the icinga context, defaulting to Ansible's inventory hostname
            ip: "10.10.10.10" # This is the IP address over which the child nodes should connect to this node
            port: 1234 # This is the port over which API communication should happen
      - name: satellite1
        parent: master # This is this zone's parent zone, thus establishing the hierarchy
        endpoints:
          - host: icinga-satellite1.localdomain
            ip: "10.10.10.11"
      - name: layer3
        parent: satellite1
        endpoints:
          - host: icinga-satellite2.localdomain
            name: layer3-satellite1
          - host: icinga-satellite3.localdomain
            name: layer3-satellite2
    icinga2_cluster_global_zones:
      - "director-global"

  roles:
    - icinga2_cluster
```
