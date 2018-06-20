# Loan Approval WS-BPEL Business Process

This project implements the Loan Approval Process using WS-BPEL 2.0 and WSDL 1.1. The implementation is deployable in a BPEL engine. I have developed and tested the process with [Apache ODE 1.3.x](http://ode.apache.org).

![](loanapproval-wsbpel.jpg)

The implementation under the folder `resources` consists of a `.bpel` file that describes the main business logic of the process and a number of `.wsdl` files that specifies various functions provided by different Web services.

### Requirements

  - [Apache ODE 1.3.x](http://ode.apache.org) (please see NOTE) or 2.0-beta
  - [Apache Ant](https://ant.apache.org)

### Deploying/Running

The local path to the Apache ODE repository is defined in the file `ant.properties`. This value must reflect the actual location of Apache ODE in your host/computer.

##### Compiling

```sh
ant process.compile
```

##### Deploying
```sh
ant process.deploy
```

##### Packaging

```sh
ant process.package
```

### Sidenotes

  - The legal delay before the customer makes a decision is current set at 5 seconds.

```xml
<bp:wait
    name="LegalDelayForThinking">
    <bp:for>'P0Y0M0DT0H0M5S'</bp:for>
</bp:wait>
```

  - The legal time-out is currently set at 30 seconds. 

```xml
<!-- Legal time-out -->
<bp:onAlarm>
	<bp:for>'P0Y0M0DT0H0M30S'</bp:for>
```

Those values are used for testing purposes only. 

###### Apache ODE versions

Apache ODE version 1.3.3 has a severe bug with Axis2: If the process has multiple consecutive invocations to a service, that might cause an `axis2.AxisFault` of _"two services cannot have same name"_ (Apache ODE 1.3.3)
(see https://issues.apache.org/jira/browse/ODE-647 for more information). Applying the patch and rebuilding Apache ODE from its source will fix the issue.