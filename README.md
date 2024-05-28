# Code Generation: Rule Engine
The purpose of this repository is to provide an example of how a **Rule Engine** can be implemented in [InterSystems Caché](http://www.intersystems.com/our-products/cache/cache-overview/) using [Object Generators](http://docs.intersystems.com/cache20152/csp/docbook/DocBook.UI.Page.cls?KEY=GOBJ_generators#GOBJ_methodgen).

## Implementation
* Code generation is an excellent way of increasing performance moving run-time calculations to compile-time.
* The intention of this example is to explain how something as complex as a Rule Engine can be modelled in InterSystems Caché.
* [InterSytems Ensemble](http://www.intersystems.com/our-products/ensemble/ensemble-overview/) provides a very powerful Rule Engine that is way much better than this homemade example. 

### Goal
1. Describe a **rule** using a human-readable XML.
2. Compile the rule and generate the code to evaluate that rule in run-time.
![](https://dl.dropboxusercontent.com/u/2198214/ISC/images/CodeGen_ObjectGen_RuleEngine_Goal.png)

### Classes
![](https://dl.dropboxusercontent.com/u/2198214/ISC/images/CodeGen_ObjectGen_RuleEngine_Classes.png)

## Installation
```
set path="C:\Temp\cache-iat-ruleengine-master\cache"
do $system.OBJ.ImportDir(path,"*.inc","ck",.error,1)
do $system.OBJ.ImportDir(path,"*.xml","ck",.error,1)
```

## Run the code
* The example provides a patient alert rule.
* After compiling the classes of the project, we can run the example:
  * Create a context to evaluate the rule: this context contains all needed data to evaluate the rule, in this case a patient object. 
  * Evaluate the rule.
* To run the example, simply execute *##class(IAT.RuleEngine.Test.Examples).Run()* method
```
Class IAT.S01.Rules.Test.Example Extends %RegisteredObject
{

ClassMethod Run() As %Status
{
  set ret = $$$OK
  try {
    // create a patient
    set p = ##class(Patient).%New()
    set p.MRN="1234", p.Name="John", p.Surname="Snow", p.DOB=$zdh("1975-05-07",3)

    // create a rule context, set data
    set context = ##class(PatientContext).%New()
    set context.Patient = p

    // evaluate Patient Alerts Rule
    set ruleEngine = ##class(IAT.S01.Rules.Engine).%New()
    $$$TOE(sc, ruleEngine.Evaluate("IAT.S01.Rules.Test.PatientAlertsRule", context, .log))

    // print log
    write !,"Rule log:",!
    zwrite log
  } catch ex {
    set ret = ex.AsStatus()
    do $system.Status.DisplayError(ret)
  }
  quit ret
}

}
```

Execute the method from a Terminal session:
```
do ##class(IAT.RuleEngine.Test.Example).Run()
 
=======================
SendEmail
To:test@server.com
Body:
Patient is so old!
=======================
 
=======================
ShowObject
+----------------- general information ---------------
|      oref value: 1
|      class name: IAT.RuleEngine.Test.Patient
| reference count: 3
+----------------- attribute values ------------------
|                DOB = 49069
|                MRN = 1234
|               Name = "John"
|            Surname = "Snow"
=======================
 
Rule log:
log=4
log(1)="[2016-02-22 16:29:44] Rule: Not young anymore!"
log(2)="[2016-02-22 16:29:44] Action: 1"
log(3)="[2016-02-22 16:29:44] Action: 2"
log(4)="[2016-02-22 16:29:44] Action: 3"
```

# Developer Community
Have a look at [InterSystems Developer Community](https://community.intersystems.com/) to learn about InterSystems technology, sharing solutions and staying up-to-date on the latest developments.

This example was published in https://community.intersystems.com/post/object-generators-homemade-ruleengine

# Contributions
## %XGEN.AbstractDocument implementation
Dmitry Maslennikov (https://github.com/daimor) has uploaded a very interesting alternative approach which uses *%XGEN.AbstractDocument* class to process the XData.

The code is available in [/pr/xgen](https://github.com/intersystems-ib/cache-iat-ruleengine/tree/pr/xgen) branch.

Additional information is available here:
https://community.intersystems.com/post/xdata-studio-asist

do ##class(IAT.RuleEngine.Test.Example).Run()

## Docker    
Container build and start runs ALL installation steps.    
It is immedeatly ready for use as decsribed    

### Prerequisites
Make sure you have [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [Docker desktop](https://www.docker.com/products/docker-desktop) installed.
### Installation
Clone/git pull the repo into any local directory
```
$ git clone https://github.com/rcemper/PR_iat-ruleengine.git
```
```
$ docker compose up -d && docker compose logs -f
```
Quick REST access to MASTER:   
http://localhost:42773/synchmaster/rest/v1/customers   

Quick REST access to CLIENT:    
http://localhost:42773/synchclient/rest/v1/employees   


To open IRIS Terminal do:   
```
$ docker-compose exec iris iris session iris 
USER>do ##class(IAT.RuleEngine.Test.Example).Run()   
- - - -
```
or using **WebTerminal**     
http://localhost:42773/terminal/      

To access IRIS System Management Portal   
http://localhost:42773/csp/sys/UtilHome.csp    

