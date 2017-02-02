#Dependencies collection in Application Insights

Dependency data collecion is very important feature of Application Insights that raises many quesions. This is a single issue where I want to accumulate the knowledge about dependency colleciton.

There are three main features of dependency collection:
- Track dependency calls and duraiton
- Correlate components by setting `target` and `source` fields
- Correlate transactions by passing identifiers

##Resources
Documentation on Azure docs https://docs.microsoft.com/en-us/azure/application-insights/app-insights-asp-net-dependencies

Dependencies schema https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Bond/RemoteDependencyData.bond

How status monitor works http://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/

Source code in:
- .NET https://github.com/Microsoft/ApplicationInsights-dotnet-server/tree/develop/Src/DependencyCollector
- node.js https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/AutoCollection/ClientRequests.ts
- JavaScript https://github.com/Microsoft/ApplicationInsights-JS/tree/master/JavaScript/JavaScriptSDK/ajax
- Java https://github.com/Microsoft/ApplicationInsights-Java/blob/63da3641ffdc91476993e738eed3adedb4acd117/core/src/main/java/com/microsoft/applicationinsights/internal/agent/CoreAgentNotificationsHandler.java

##Issues
###Correctness
- https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/285 (Include the port name into dependency target)
- https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/202 (2.2 Beta Sdks are overzealously truncating http dependency names)
- https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/108 (SQL Dependency duration recorded incorrectly)
- https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/62 (Support chunked request and response http dependency tracking)

###Gaps
- https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/193 (SQL/Stored Procedure - Parameters and Values)

###Correlation
- https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/283 (Allow custom code to override request.source and dependency.target fields)
- https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/113 (Correlate async dependencies with requests)
- https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/57 (Azure Storage SDK "chunking" affects AI context tracking)


##.NET dependencies collection

.NET SDK has the most sophisticated dependencies collection logic so far. With the .NET Core introduction our dependency collection story degraded drammatically.

###Framework 4.0-4.5 HttpWebRequest & HttpClient
BCL -> System.Net.dll 

HttpClient is using HttpWebRequest as a transport. We can improve HttpClient collection by instrumenting wrapping method, not only transport

###Framework 4.0 HttpWebRequest Running on FW 4.5.2+
BCL -> System.Net.dl
We can improve data collection by forcedly load FW 4.5.2+ collector

###WCF not over http
netTCP, others

See https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/302

###WCF over http
Dependency name is not calculated as customer would expect (name of the method)

###Azure ServiceBus
Azure SDK
Service Bus can use three kinds of transport. See the [docs](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-performance-improvements)
1. AMQP 
2. SBMP    
3. HTTP   
SBMP is default for the .NET SDK 

See https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/208     |

###Azure DocumentDB
Azure SDK
Two transport layers:
1. Gateway mode (Http)
2. Direct Connectivity

See https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/125

##node.js dependencies collection

Only http dependencies are collected out of the box.

Problems
1. Lack of request to dependency correlation
2. No cross component transaction id propogation
3. No client-side http parsing into Azure Storage


##JavaScript dependencies (AJAX) collection

Only http dependencies are supported

Problems
1. No cross component correlation (target/source)
2. No client-side http parsing into Azure Storage
3. No sampling on dependencies from a single page, only hard limit on count