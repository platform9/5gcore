# 5gcore

This repository allow anyone to deploy free5gc core on platform9.

## Pre requisite

```
Kubernetes cluster created by Platform Management Plane.
KubeVirt should be enabled and create Virtual Machine with Ubuntu 18 with Linux kernel version 5.0.0-23-generic.
Gtp5g Kernel module on Ubuntu Virtual Machine.
SCTP support in Kubernetes Services.
Kubernetes Cluster backed by Calico CNI or any CNI having capability to provide static IP to pods.
```

Kubevirt https://platform9.com/blog/get-up-and-running-with-kubevirt-for-kubernetes-based-vm-management/

Gtp5g https://github.com/PrinzOwO/gtp5g

## Installation
We will be installing 5G components as per 3gpp TS 23.501 5g service based reference architecture

In this blog , UPF which is the data plane, will be installed on Virtual machine managed by Platform Manage Kubevirt solution and control plane components are installed on Platform Managed Kubernetes Cluster.

```
Clone the repo 

Users are required to first clone the repo 5gcore(https://github.com/platform9/5gcore) which has all the required Kubernetes manifest files.

Create the namespace
Create a namespace called free5gc using following command
kubectl create ns free5gc

Change the static IP for SMF and AMF
This project requires static IP for SMF and AMF. This can be achieved via calico annotations.
Adapt/Edit  the free5gc-amf.yaml and free5gc-smf.yaml as per the environment and static IP to be used.
In  the free5gc-configmap.yaml change the amf and smf configuration to point to the same static IP used above.

Install the database first
The first component to install is the mongo database. Install the database using following command
kubectl apply -f free5gc-mongodb.yaml -n free5gc

Mongodb pod should be up and running

ubuntu@5g-core-01:~/pf9-free5gc/database$ kubectl get pods -n free5gc |grep mongo
free5gc-mongodb-0                          1/1     Running   0          2d23h
ubuntu@5g-core-01:~/pf9-free5gc/database$ 

Create the configMap
Each control plane has its configuration which is defined in free5gc-configmap.yaml.
Create configuration using following command
	kubectl apply -f free5gc-configmap.yaml -n free5gc


Configuration Map should be created

ubuntu@5g-core-01:~/pf9-free5gc$ kubectl get cm -n free5gc 
NAME                DATA   AGE
free5gc-configmap   12     2d23h


Install the Network Registry Function (NRF)
The next component to install is NRF. Use the following command to install the NRF
	kubectl apply -f free5gc-nrf.yaml -n free5gc

NRF pod should be up and running

ubuntu@5g-core-01:~/pf9-free5gc$ kubectl get pods -n free5gc |grep nrf
free5gc-nrf-deployment-6dd4bf6dd7-8wmzg    1/1     Running   0          2d23h
ubuntu@5g-core-01:~/pf9-free5gc$

Install UPF in VM
Once the Virtual Machine is created as described as a prerequisite section using Kubevirt on Platform9 install the UPF on that machine following upf-3.0.4. 
Modify the upfcfg.yaml as per the Virtual Machine IP address. Sample upfcfg.yaml is part of cloned repo.

Install Rest of control Plane components
We can now install rest all the control plane components using there corresponding manifest file
SMF
AMF
UDR
UDM
AUSF
PCF

Status of all the pods in free5gc namespace

ubuntu@5g-core-01:~/pf9-free5gc$ kubectl get pods -n free5gc 
NAME                                       READY   STATUS    RESTARTS   AGE
free5gc-amf-deployment-68878d4889-7xgh6    1/1     Running   0          2d23h
free5gc-ausf-deployment-7f6b76dbdd-wqsc6   1/1     Running   0          2d23h
free5gc-mongodb-0                          1/1     Running   0          2d23h
free5gc-nrf-deployment-6dd4bf6dd7-8wmzg    1/1     Running   0          2d23h
free5gc-nssf-deployment-68f7df5fc4-8dw5h   1/1     Running   0          2d23h
free5gc-pcf-deployment-86564779d4-8kp6q    1/1     Running   0          2d23h
free5gc-smf-deployment-56b44b9f8-jfs5j     1/1     Running   0          2d23h
free5gc-udm-deployment-5c84964ff4-vf75j    1/1     Running   0          2d23h
free5gc-udr-deployment-6786894cc7-7m96t    1/1     Running   0          2d23h
ubuntu@5g-core-01:~/pf9-free5gc$ 
For SMF and AMF you can see we have static IP assigned 

ubuntu@5g-core-01:~/pf9-free5gc$ kubectl get pods -n free5gc  -o wide|grep smf
free5gc-smf-deployment-56b44b9f8-jfs5j     1/1     Running   0          2d23h   10.20.0.115   10.128.237.203   <none>           <none>
free5gc-amf-deployment-68878d4889-7xgh6    1/1     Running   0          2d23h   10.20.0.110   10.128.237.202   <none>           <none>
```

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
