Monitors a USB connected UPS.

Running this as a K8s container.  

- Notes:
  - ServiceAccount:
    - Because the Pod needs to access the hardware, it needs to run in privilege mode.  It is better to create another serviceaccount and give the correct permissions for that, then to give elevated permissions to the default service account.  See the YAML under the service account folder for the code to create the account.
    - Once created give the correct permissions to the account
      ``` kubectl create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=default:nut-upsd-svcacct ```
  - Serial Number:
    - For APC and some other UPS manfacturers, you will need to include the Seriral Number.  Without this you will see an error in the container logs.
  - Secret:
    - The secret is used for the API_PASSWORD parameter.  
    - The secret itself needs to be net-upsd-password. If not the containers entrypoint will fail with cat: read error: Is a directory
    - to create the secret
      ``` kubectl create secret generic nut-upsd-password --from-literal=password='SomePassword' ```

Finally, to integrate with HA, you need a username and password.  Use these
- UserName = upsmon
- Password = your Secret

#### USB Passthrough
- VMWare USB passthrough
  - https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vm_admin.doc/GUID-68A08879-1744-4FF9-A856-D66C4AAB68AB.html
  - Note: on my physical server te left most USB port doesn't seem to allow passthrough (I haven't dug into why)

# Official Docs
https://networkupstools.org/

### Sample manifest
- https://github.com/instantlinux/docker-tools/blob/main/images/nut-upsd/kubernetes.yaml

### Image
- https://hub.docker.com/r/instantlinux/nut-upsd

# Troubleshooting
- Connected to UPS [ups]: usbhid-ups-ups
   0.000000     Ignoring invalid pid number 0
   0.002759     Using power down flag file /etc/killpower
   0.002835     Unable to use old-style MONITOR line without a username
   0.002838     Convert it and add a username to upsd.users - see the documentation
   0.002840     Fatal error: unusable configuration
  - This error in the container log indicates you are missing a secret password.

