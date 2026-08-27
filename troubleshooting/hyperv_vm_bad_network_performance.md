# Hyper-V: Low Network Throughput / Latency on Host with Broadcom NICs
Tags: Hyper-V, Networking, VMQ, Broadcom, Performance

# Situation:
A couple of days after implementing Hyper-V for a customer, users reported slow network performance. Processes running in the ERP clients and files transferred to and from the fileserver were taking longer than usual on a certified 1 Gbps network. 

# Task:
Identify the root cause of the performance problem the servers were experiencing. Find a solution so end users do not complain about lag from the ERP, fileserver or other systems.

# Action: 
As it appeared to be a network issue, I tried the following to diagnose the network:
1.	Pinged the servers to check latency or lost packets. Result: Network latency was normal (under 1 ms) and there were no lost packets.
2.	Transferred a large file from the fileserver to a computer and vice versa to measure NIC transfer speed using the Task Manager. I noticed NIC throughput was low (~10 Mbps) for a 1 Gbps network.
3.	Used the LAN Speed Test tool to confirm the transfer speed of files from the fileserver. This confirmed the previous measurement from the Task Manager. Note: All the previous diagnostic checks were done only on the fileserver, because I thought if I could solve the problem for this server, the others could be solved the same way.
4.	Checked the speed configuration for the NIC on the computer, the fileserver VM and the host. All of them were working at 1 Gbps full-duplex. This made me consider in the possibility that it could be a hypervisor bug or configuration problem as it was the first time deploying Hyper-V for a client and I had never had a situation like this with other hypervisors.
After performing the previous checks, I also checked the configuration of the virtual switch and the physical NICs of the Hyper-V server and found that everything was correctly configured.
The next step was researching on the web to see if there had been reported network performance issues with Hyper-V. Found several users pointing to Virtual Machine Queues (VMQ) as the cause, especially when using Broadcom NICs. https://community.spiceworks.com/t/slow-hyper-v-network/820817

I found out that VMQ is a Hyper-V feature that should improve network performance by offloading packet filtering and distribution to the physical NIC, but in this case, it was not helping at all. I also found reported cases from Microsoft regarding the same situation with Broadcom NIC version 57xx series. https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/vm-lose-network-connectivity-broadcom


I checked the vendor of the host’s NICs and confirmed they were Broadcom. Then I proceeded to test the hypothesis by disabling VMQ in the NICs. This can be done using the following  PowerShell command or the GUI.

### Powershell command

Set-NetAdapterVmq -Name "NIC Name" -Enabled $False

### Disable VMQ on Individual Virtual Machines
1.	Open Hyper-V Manager.
2.	Right-click the affected virtual machine and select Settings.
3.	Expand the network adapter settings and click on Hardware Acceleration.
4.	Uncheck the box labeled Enable virtual machine queue.
5.	Click Apply and OK.

### VMQ could be disable entirely via Device Manager on the server
1.	Press Windows Key + X and select Device Manager.
2.	Expand Network adapters and right-click your physical network card, then choose Properties.
3.	Go to the Advanced tab.
4.	Scroll down the list of properties to find Virtual Machine Queue (or VMQ).
5.	Change the value from Enabled to Disabled.


# Result: 
After disabling the VMQ feature on Hyper-V, I tested the fileserver with the Lan Speed Test again and the files were transferring using the whole network bandwidth. I also tried uploading and downloading a large file manually and asked the end user to also do some tests both with the fileserver and the ERP system. The tests were all successful, and with that, the case was closed.

# Learnings: 
When deploying a hypervisor, one should check the physical specifications of the host, including the vendor and model of the NICs, to see if there could be any compatibility issues before implementing the solution.
As this was a data link layer (NIC) problem caused by a configuration in the application layer (hypervisor), troubleshooting this case was difficult, but previous reports of the same problem and the knowledge base from the hypervisor’s vendor (Microsoft) helped find the solution quickly.
