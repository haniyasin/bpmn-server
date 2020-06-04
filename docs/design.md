Concepts
=========

Workflow is a state machine. You can imagine it as a flow chart running inside in your application, and when it reach a decision point, you can invoke a method to provide an answer that chooses the right path of execution from there. 

So workflow is a graph of control flow, and activities are its nodes. They can be as simple like an if..then..else branch, or as complex like getting data from a database and do something complicated depending on the results.

A workflow is an application running inside in your application, have its state and variables, and correlated across Node.js cluster and process instances. So if your Node.js application consists of many instances inside a cluster and many clusters across a server farm, a workflow instance will work like a single instance application within those. A workflow application could outlive Node.js applications, they have out-of-the-box persistence support to make them ideal platform to do long running business processes, durable services or scheduled backgound tasks.

Workflow is modeled using BPMN 2 tools and is defined in as an XML string or stream. Each element in the model is calleld a <b>Node</b> connected by <b>Flow</b>

<b>Execution</b> is the entry point of your processing, you start by 

```javascript
		const execution = new Execution(source,customizer, logger);

		execution.start(data,startNode);
```
Execution handles the entire definition, that can contain one or more processes.

Execution object will walk through BPMN definition starting for the startEvent and take various paths accoridng to the logic.

Execution uses tokens to walk the path, a <b>Token</b> represent a branch of the path.

Everytime a Node is executed an <b>Item</b> is created for the Node.

Therefore, you can query the execution of various items it performed:

```javascript

		let items =execution.getItems();
		items.forEach(item=>{
			console.log(`item id: ${item.id} - status: ${item.status});
		});
```

To continue with processing our example, The will invoke the Node 'Buy'

```javascript
		execution.signal({taskId:'Buy'},{
			model:'BMW' , needsCleaning: true ,needsRepairs: false);
```
Let us examine what is happening under the hood:

```javascript
    execution.getItems.forEach(i => {
        console.log(`item: ${i.id} - task Id - ${i.element.id} Type: ${i.element.type} status: ${i.status}`  );
    });

```
# Data Scope (during exectution)
The entire execution will have one data scope object, shared among all nodes except SubProcess and Loops (Multi-instances), 
each will have own item part of the data object

Example: Process input: CaseId
		  Task1 input: amountOwed
			subprocess1:	interest
		  loop instance1:	callDate
			
```javascript
    data: {
		caseId: 3421, amountOwed: 5201 ,
		subprocess1: { interest: 200 } ,
		loop { [{callDate: '01/01/2020' }
			]}
}
```


# Data Store


# Decisions

WF engine makes several decisions based on BPMN rules and definitions, the challenge is there an easy way to communicate those decisions back to the developer/support?


Everytime the engine makes a decision it attaches the appropriate decision object to item impacted

For example; If a gateway decides to select one flow over the other, you should see a record of that in the gateway item
