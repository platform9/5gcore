# 5gcore

This repository allow anyone to deploy free5gc core on platform9.

## Pre requisite



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
