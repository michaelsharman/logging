# Logging.cfc

A generic ColdFusion class to abstract application logging (to server log files) to a single point. You can also log long running requests to help diagnose problem areas in your application.

The abstraction means an application can log to different files and be easily changed or even turned off from within a single file. The recommendation for this is to be used as a singleton.

## Log groups

Liberal logging of system events and information is a good practise for your application development to assist in running diagnostics when required.

Specific logging can be turned on and off by the use of _groups_. This means that you can add log commands throughout your codebase and control if the message is actually written by changing a property of the logGroup. 

The logGroup format is a simple ColdFusion structure, defined either in the constructor (not recommended) or in your application bootstrap location (recommended) like onApplicationStart().

Eg

```ColdFusion
logGroup = {
	myLogArea = true,
	myOtherLogArea = false,
	anotherLogArea = true
}
```

Pass that into Logging.cfc on instantiation so you can reference a specific group to log to:

```ColdFusion
Logging = new Logging(logGroup=logGroup),
```

Now if you were to log a message like this:

```ColdFusion
Logging.write(text="a sample message", group="myOtherLogArea");
```

Nothing would appear in the system log file because that group is set to _false_. However if you logged the following:

```ColdFusion
Logging.write(text="another sample message", group="myLogArea");
```

If would log (because the group is set to _true_), with the group prefixed in the message.

	[MYLOGAREA] another sample message

This makes it simple to grep in a log file.	

## Logging long running requests

You can run a check to see how long any request took, if it took longer than a certain amount (defined in the Logging options)…log a message so developers can investigate.

For example in a _before_ hook you could set a start time:

```ColdFusion
rc.requestStartTime = getTickCount();
```

Then in an _after_ hook you could call the following:

```ColdFusion
Logging.requestTime(startTime=val(rc.requestStartTime), action=rc.action, type="warning");
```
That will log a warning if the request took longer than _n_ seconds (defined when you instantiate the Logging class) and do nothing if it didn't.

Here is a full example for instantiation:

```ColdFusion
Logging = new Logging(logLongRequest=true, longRequestLimit=250, logGroup=logGroup),
```

Read the comments in Logging.cfc for more.
