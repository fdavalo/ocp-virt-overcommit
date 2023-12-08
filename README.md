# Openshift Virtualization OverCommit

## I will focus on CPU overcommit

CPU Overcommit is used frequently on virtualization platforms and can be done also with Openshift Virtualization

## Openshift Virtualization provides cpu overcommits when no cpu resources (requests, limits) is set for a VM

  * By default, the container requests (1/cpuAllocationRatio) CPU per vCPU. The number of vCPUs is socketscoresthreads, defaults to 1.
cpuAllocationRatio defaults to 10 but can be changed in the CR.

  * If a CPU limit is manually set on the VM(I) and no CPU request is, the CPU requests on the container will match the CPU limits
    
  * Manually setting CPU requests on the VM(I) will override all of the above and be the CPU requests for the container

  * There are options to force a limit on your Virtual Machine (cf Auto CPU limits)

## Operator Cluster Resource Override can also be used to specify a request to limit ratio 

  * First install the Operator from OperatorHub and instantiate a cluster instance of CRD ClusterResourceOverride

            apiVersion: operator.autoscaling.openshift.io/v1
            kind: ClusterResourceOverride
            metadata:
            -   name: cluster
            spec:
               **cpuRequestToLimitPercent: 15**

 * Label your namespace to be handled by the operator

            kind: Project
            apiVersion: project.openshift.io/v1
            metadata:
              name: nico-demo-vm
              labels:
                **clusterresourceoverrides.admission.autoscaling.openshift.io/enabled: 'true'**
              ...
  
 * When you specify a limit on your Virtual Machine, the cpu resource request will be changed according to the ratio (here 15%) in the virtual launcher pod associated with the VM

              containers:
                - volumeDevices:
                    - name: rootdisk
                      devicePath: /dev/rootdisk
                  resources:
                    limits:
                      cpu: '2'
                    requests:
                      cpu: 300m
   
 
