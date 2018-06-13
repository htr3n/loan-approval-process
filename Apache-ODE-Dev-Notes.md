# Apache ODE Process Development Notes

### Scenario 1: Creating new WS-BPEL business processes

1. Add two attributes at the root element (i.e., <process>)

* ```xml
  <process  
  	queryLanguage="urn:oasis:names:tc:wsbpel:2.0:sublang:xpath2.0"
  	expressionLanguage="urn:oasis:names:tc:wsbpel:2.0:sublang:xpath2.0"
      ... >
  </process>
  ```
2. Enforce qualified XML schema elements in your XSDs by specifying `elementFormDefault="qualified"`

* ```xml
  <xsd:schema ... elementFormDefault="qualified">
  </xsd:schema>
  ```

3. Define `<binding>` and `<service>` in the Web service description (`.wsdl`) of the WS-BPEL process. The conventional endpoint address of a BPEL process in Apache ODE is `http://<host>:8080/ode/processes/<processName>`. An example of the WSDL endpoint is

* ```xml
  <ws:service name="LoanApproval">
      <ws:port name="LoanApprovalPort" binding="tns:LoanApprovalSOAP">
          <soap:address location="http://localhost:8080/ode/processes/LoanApproval" />
      </ws:port>
  </ws:service>
  ```


  This requirement is a bit strange because Apache ODE will internally uses [Apache Axis 2](http://axis.apache.org/axis2/java/core/index.html) to generate the bindings and endpoints anyway.

4. The BPEL compiler (`bpelc`) should be used to compile the WS-BPEL process and make sure it error-free before deploying it to Apache ODE.

5. Must include necessary resource files including WS-BPEL descriptions (`*.bpel`), referenced Web services (`*.wsdl`), XML data schema (`*.xsd`), and the deployment description `deploy.xml`.


### Scenario 2: Adding Tasks Invoking Web services 

1. Process's  Web service interface description `LoanApproval.wsdl`

  a) Add the namespace prefix corresponding to the Web service at the root element  (i.e., `<definitions>`)

  b) Add a `<partnerLinkType>` for the corresponding Web service

2. Business Process Description `LoanApproval.bpel`

  a) Add an `<import>` for the WSDL and another `<import>` for XSD (if any)
  b) Add the namespace prefix corresponding to the WSDL and XSD (if any) at the root element (i.e., `<process>`)
  c) Add a `<partnerLink>` corresponding to the `<partnerLinkType>` defined in the previous step
  d) Add input and output variables for invoking the Web services. The `messageType` attribute of each variable must be the message defined in the Web service's WSDL.
  e) Add an `<invoke>` element to define the service invocation as following

  ```xml
  <invoke name="NCName" 
          partnerLink="NCName" 
          operation="NCName" 
          portType="QName" 
          inputVariable="NCName"
          outputVariable="NCName" />
  ```

  f) Must add an `<assign>` to initialize the input variables, otherwise, Apache ODE will throw a fault _'uninitializedVariable'_. 

  g) Numeric elements (e.g. float, integer, etc.,) of XML types must be assigned values within `<![CDATA[value-here]]>`. Otherwise, Apache ODE will throw an _'Unmarshalling'_ exception. 

3. Deployment Configuration `deploy.xml`

   a) Add the namespace prefix corresponding to the WSDL at the root element (i.e., `<deploy>`)

   b) Add an `<invoke>` element, specify the `partnerLink` attribute which is identical to that in step 2(c).

4. ```xml
  <invoke partnerLink="non-qualified-partnerLink-name">
  ...
  </invoke>
  ```
  c) Add a `<service>` element which is sub-element of the `<invoke>` in 3(b), specify precisely the service and port names:

  ```xml
    <invoke partnerLink="non-qualified-partnerLink-name">
        <service name="qualified-service-name" port="non-qualified-port-name" />
    </invoke>
  ```

### Scenario 3: Receiving/Replying

Defining tasks that receive from or reply to customers (i.e., these tasks are correspondent to the operations that are visible in the Web service description of the process) 
