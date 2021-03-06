


If you can't access an application running on an Azure virtual machine, this article describes a methodical approach for isolating the source of the problem and correcting it.

> [AZURE.NOTE]  For help in connecting to an Azure virtual machine, see [Troubleshoot Remote Desktop connections to a Windows-based Azure Virtual Machine](../articles/virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) or [Troubleshoot Secure Shell (SSH) connections to a Linux-based Azure virtual machine](../articles/virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

If you need more help at any point in this article, you can contact the Azure experts on [the MSDN Azure and the Stack Overflow forums](https://azure.microsoft.com/support/forums/). Alternatively, you can also file an Azure support incident. Go to the [Azure Support site](https://azure.microsoft.com/support/options/) and click on **Get Support**.


There are four main areas to troubleshoot the access of an application that is running on an Azure virtual machine.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1.	The application running on the Azure virtual machine.
2.	The Azure virtual machine.
3.	Azure endpoints for the cloud service that contains the virtual machine (for virtual machines in classic deployment model), inbound NAT rules (for virtual machines in Resource Manager deployment model), and Network Security Groups.
4.	Your Internet edge device.

For client computers that are accessing the application over a site-to-site VPN or ExpressRoute connection, the main areas that can cause problems are the application and the Azure virtual machine.
To determine the source of the problem and its correction, follow these steps.

## Step 1: Can you access the application from the target VM?

Try to access the application with the appropriate client program from the VM on which it is running. Use the local host name, the local IP address, or the loopback address (127.0.0.1).

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

For example, if the application is a web server, run a browser on the VM and try to access a web page hosted on the VM.

If you can access the application, go to [Step 2](#step2).

If you cannot access the application, verify the following:

- The application is running on the target virtual machine.
- The application is listening on the expected TCP and UDP ports.

On both Windows and Linux-based virtual machines, use the **netstat -a** command to show the active listening ports. Examine the output for the expected ports on which your application should be listening. Restart the application or configure it to use the expected ports as needed.

## <a id="step2"></a>Step 2: Can you access the application from another virtual machine in the same virtual network?

Try to access the application from a different VM but in the same virtual network, using the VM's host name or its Azure-assigned public, private, or provider IP address. For virtual machines created using the classic deployment model, do not use the public IP address of the cloud service.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

For example, if the application is a web server, try to access a web page from a browser on a different VM in the same virtual network.

If you can access the application, go to [Step 3](#step3).

If you cannot access the application, verify the following:

- The host firewall on the target VM is allowing the inbound request and outbound response traffic.
- Intrusion detection or network monitoring software running on the target VM is allowing the traffic.
- Network Security Groups are allowing the traffic.
- A separate component running in your VM in the path between the test VM and your VM, such as a load balancer or firewall, is allowing the traffic.

On a Windows-based virtual machine, use Windows Firewall with Advanced Security to determine whether the firewall rules exclude your application's inbound and outbound traffic.

## <a id="step3"></a>Step 3: Can you access the application from a computer that is outside the virtual network, but not connected to the same network as your computer?

Try to access the application from a computer outside the virtual network as the VM on which the application is running, but is not on the same network as your original client computer.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

For example, if the application is a web server, try to access the web page from a browser running on a computer that is not in the virtual network.

If you cannot access the application, verify the following:

- For VMs created using the classic deployment model, that the endpoint configuration for the VM is allowing the incoming traffic, especially the protocol (TCP or UDP) and the public and private port numbers. For more information, see [How to Set Up Endpoints to a Virtual Machine](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).
- For VMs created using the classic deployment model, that access control lists (ACLs) on the endpoint are not preventing incoming traffic from the Internet. For more information, see [How to Set Up Endpoints to a Virtual Machine](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).
- For VMs created using the Resource Manager deployment model, that the inbound NAT rule configuration for the VM is allowing the incoming traffic, especially the protocol (TCP or UDP) and the public and private port numbers.
- That Network Security Groups are allowing the inbound request and outbound response traffic. For more information, see [What is a Network Security Group (NSG)?](../articles/virtual-network/virtual-networks-nsg.md).

If the virtual machine or endpoint is a member of a load-balanced set:

- Verify that the probe protocol (TCP or UDP) and port number are correct.
- If the probe protocol and port is different than the load-balanced set protocol and port:
	- Verify that the application is listening on the probe protocol (TCP or UDP) and port number (use **netstat –a** on the target VM).
	- The host firewall on the target VM is allowing the inbound probe request and outbound probe response traffic.

If you can access the application, ensure that your Internet edge device is allowing:

- The outbound application request traffic from your client computer to the Azure virtual machine.
- The inbound application response traffic from the Azure virtual machine.

## Troubleshooting Endpoint Connectivity problems

If you have problems when connecting to an Endpoint such as Remote Desktop  Endpoint, you can try the following general troubleshooting steps:

- Restart virtual machine
- Recreate Endpoint
- Connect from different location
- Resize virtual machine
- Recreate virtual machine

For more information, see [Troubleshooting Endpoint Connectivity (RDP/SSH/HTTP, etc. failures)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).



## Additional resources

[Troubleshoot Remote Desktop connections to a Windows-based Azure Virtual Machine](../articles/virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md)

[Troubleshoot Secure Shell (SSH) connections to a Linux-based Azure virtual machine](../articles/virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md)
