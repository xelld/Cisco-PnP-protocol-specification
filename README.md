# Cisco PnP protocol specification
Saved from developer.cisco.com and web archives:
- [learn-open-pnp-protocol](https://developer.cisco.com/site/open-plug-n-play/learn/learn-open-pnp-protocol)
- [network-plug-n-play](https://developer.cisco.com/docs/network-plug-n-play/)

## DNS Discovery
- Networking device is cabled and powered on. Since the startup configuration in NVRAM is empty, PnP agent is triggered and sends “Cisco PnP” in DHCP Option 60 in DHCP DISCOVER message.
- Since DHCP server is not configured to recognize “Cisco PnP” in Option 60, it ignores Option 60. DHCP server assigns an IP address and sends DHCP offer along with configured domain name and DNS server IP address.
- PnP agent reads domain name and formulates fully qualified PnP server hostname by appending domain name to the string “pnpserver”. If the domain name is “xyz.com”, fully qualified hostname of PnP server would be “pnpserver.xyz.com”. PnP agent resolves “pnpserver.xyz.com” for its IP address with the DNS server received in the DHCP options.

Example DHCP config:
```
default-lease-time 600;
max-lease-time 7200;
ddns-update-style none;
authoritative;
subnet 172.24.172.0 netmask 255.255.255.0 {
    range 172.24.172.200 172.24.172.254;
    option subnet-mask 255.255.255.0;
    option routers 172.24.172.1;
    option domain-name-servers 172.24.172.24;
    option domain-name "zone.lab";
    default-lease-time 600000;
    max-lease-time 720000;
}
```
Example DNS zone config:
```
$TTL 5m    ; default TTL for zone
$ORIGIN zone.lab. ; base domain-name
@         IN      SOA   ns1.zone.lab. hostmaster.zone.lab. (
                                2003080800 ; serial number
                                12h        ; refresh
                                15m        ; update retry
                                3w         ; expiry
                                5m         ; minimum
                                )
           IN      NS      ns1.zone.lab.
ns1        IN      A       172.24.172.24
pnpserver  IN      A       172.24.172.24
```

## Work Request
__Purpose:__ Work Request message is initiated by the PnP agent to check with the PnP server for any new work requests. \
PnP agent sends this message after discovering the server or when a PnP profile is configured and also at the beginning of every retry. \
This is applicable only when the transport protocol is HTTP and HTTPS.
__Type:__ info
__URL:__ pnp/WORK-REQUEST
__HTTP method:__ POST
Example:
```
<pnp xmlns="urn:cisco:pnp" version="1.0" udi="PID:WS-C3750X-24T-E,VID:V04,SN:FDO1703P2EB">
  <info xmlns="urn:cisco:pnp:work-info" correlator="CiscoPnP-1.0-1465-25A1212C">
   <deviceId>
    <udi>PID:WS-C3750X-24T-E,VID:V04,SN:FDO1703P2EB</udi>
    <hostname>Router</hostname>
    <authRequired>true</authRequired>
    <viaProxy>false</viaProxy>
    <securityAdvise>Password in clear text in unsecured transport</securityAdvise>
   </deviceId>
  </info>
</pnp>
```

## Bye
__Purpose:__ Bye message is sent by the PnP server to acknowledge the receipt of PnP response and also signal the end of the transaction. \
This is applicable only when the transport protocol is HTTP and HTTPS.
__Type:__ info
__URL:__ 200 OK for pnp/WORK-RESPONSE
__HTTP method:__ POST
Example:
```
<pnp xmlns="urn:cisco:pnp" version="1.0" udi="PID:WS-C3750X-24T-E,VID:V04,SN:FDO1703P2EB">
  <info xmlns="urn:cisco:pnp:work-info" correlator="CiscoPnP-1.0-1465-25A1212C">
        <workInfo>
    <bye/>
   </workInfo>
  </info>
</pnp>
```

# Cisco PnP Schema files
| Service | Files | Description |
|---|---|---|
| Backoff | pnp_backoff_body.xsd | Request the agent to back off from sending more work-request for a period of time. |
|| pnp_backoff_svc.xsd |
| Capability | pnp_capability_body.xsd | Request to query what services are supported by the agent. |
|| pnp_capability_svc.xsd |
| Certificate Install | pnp_certificate_install_body.xsd | Request the agent to install a trustpoint or trustpool certificate into the devices. |
|| pnp_certificate_install_svc.xsd |
| CLI Config | pnp_cli_config_body.xsd | Request to execute a configuration CLI. |
|| pnp_cli_config_svc.xsd |
| CLI Exec | pnp_cli_exec_body.xsd | Request to execute a exec level CLI. |
|| pnp_cli_exec_svc.xsd |
| Config Upgrade | pnp_config_upgrade_body.xsd | Upgrade the device’s configuration to a new config. |
|| pnp_config_upgrade_svc.xsd |
| Device Authentication | pnp_device_auth_body.xsd | Request to authenticate the device. |
|| pnp_device_auth_svc.xsd |
| Device Info | pnp_device_info_body.xsd | Request to obtain device’s profile which includes device UDI, filesytem, hardware info and etc. |
|| pnp_device_info_svc.xsd |
| File Transfer | pnp_file_transfer_body.xsd | Request to copy a file. |
|| pnp_file_transfer_svc.xsd |
| Image Install | pnp_image_install_body.xsd | Request the agent to install a new image. |
|| pnp_image_install_svc.xsd |
| Licensing | pnp_licensing_body.xsd | Request to install and configure new license. |
|| pnp_licensing_svc.xsd |
| Redirection | pnp_redirection_body.xsd | Request to redirect PnP Profile to use another PnP server. |
|| pnp_redirection_svc.xsd |
| Reload | pnp_reload_body.xsd | Request the agent to reload. |
|| pnp_reload_svc.xsd |
| Script | pnp_script_body.xsd | Request to execute a script. |
|| pnp_script_svc.xsd |
| SMU | pnp_smu_body.xsd | Request to install a SMU. |
|| pnp_smu_svc.xsd |
| Topology | pnp_topology_body.xsd | Request topology of the device. |
|| pnp_topology_svc.xsd |
| Work Info | pnp_work_info_body.xsd | Start the request. |
|| pnp_work_info_svc.xsd |
