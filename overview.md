#Dependencies collection in Application Insights

Dependency data collecion is very important feature of Application Insights that raises many quesions. This is a single issue where I want to accumulate the knowledge about dependency colleciton.

##Resources
https://docs.microsoft.com/en-us/azure/application-insights/app-insights-asp-net-dependencies
https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Bond/RemoteDependencyData.bond

##Issues
###Correctness
https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/285 (Include the port name into dependency target)
https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/202 (2.2 Beta Sdks are overzealously truncating http dependency names)
https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/108 (SQL Dependency duration recorded incorrectly)
https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/62 (Support chunked request and response http dependency tracking)

###Gaps
https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/193 (SQL/Stored Procedure - Parameters and Values)

###Correlation
https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/283 (Allow custom code to override request.source and dependency.target fields)
https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/113 (Correlate async dependencies with requests)
https://github.com/Microsoft/ApplicationInsights-dotnet-server/issues/57 (Azure Storage SDK "chunking" affects AI context tracking)


##Technologies

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
