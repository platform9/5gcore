# 5gcore

This repository allow anyone to deploy free5gc core on platform9.

## Pre requisite
•	Kubernetes cluster created by Platform Management Plane.
•	KubeVirt should be enabled and create Virtual Machine with Ubuntu 18 with Linux kernel version 5.0.0-23-generic.
•	Gtp5g Kernel module on Ubuntu Virtual Machine.
•	SCTP support in Kubernetes Services.
•	Kubernetes Cluster backed by Calico CNI or any CNI having capability to provide static IP to pods.
![image](https://user-images.githubusercontent.com/42035996/115346427-c925d700-a1cd-11eb-9f46-0ac32a5f1a01.png)



## Installation


## Testing
```
root@ubuntu18-upf:~/free5gc/src/test# go test -v -run TestRegistration -args noinit
MongoDB Set
=== RUN   TestRegistration
cmac value: 0xeede03a4
err <nil>
2021-04-20T05:43:23Z [INFO][NAS][Message] Encode ExtendedProtocolConfigurationOptions in EncodePDUSessionEstablishmentRequest
--- PASS: TestRegistration (2.50s)
PASS
ok      free5gc/src/test        2.525s
```
