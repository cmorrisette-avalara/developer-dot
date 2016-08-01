---
layout: default
title: Errors and Outages
date: 2012-05-14 16:40
author: anya.stettler
comments: true
categories: []
product: avatax
doctype: documentation
---
<h2>Handing Errors and Outages when things don't go your way.</h2>
Although Avalara outages have been historically infrequent and short in duration, disruptions in connectivity due to service or internet outages are things that should be considered when designing your AvaTax SDK integration. Avalara sends out service notifications if there is an issue with our service.

Avalara does not queue transactions when the web service is down. Unless a connection is made to the web service, you will only receive an HTTP 500 - Internal Server Error message which means you have lost connection to the AvaTax Web Service and your local system needs to track invoices until the web service is restored.

Consideration must then be given to how you will report taxes back to the customers and/ or users during the outage, as the solution sets used are wide and diverse depending on the ERP / Shopping Cart system. When weighing your options, you should consult your company's tax accountant or attorney. Common solutions are:
<ul>
	<li> Prevent orders from being completed until service is restored and tax is calculated dynamically (e.g. not allow invoices to be posted in an ERP).</li>
	<li>Use a fallback method of calculation and then "make up" the difference when service is restored.</li>
</ul>
Fallback methods may include:
<ul>
	<li><span style="line-height: 13.993056297302246px;">A rate that is higher than any jurisdiction in which you charge tax (e.g. 12%)</span></li>
	<li>A 0% rate</li>
	<li>A local rate table that gives an estimate by state or zip code.</li>
</ul>
Once service is restored, the tax can be transmitted to AvaTax using one of the following calculation methods:
<ul>
	<li><span style="line-height: 13.993056297302246px;">Use a tax override of TaxAmount to reflect the estimated tax.</span></li>
	<li>Use TaxIncluded to back-calculate the correct tax amount from the total (including estimated tax) charged to the customer.</li>
	<li>Allow AvaTax to recalculate the tax amount, and absorb any additional payment of taxes.</li>
</ul>
Transactions can be loaded with the <a href="/avatax/gettax">standard calculation method</a> or by using our <a href="https://help.avalara.com/000_AvaTax_Calc/000AvaTaxCalc_User_Guide/060_Managing_Transactions/030_Importing_Transactions">batch import functionality directly in the Admin Console</a>.
<h2>Messages and Errors</h2>
Understanding and taking appropriate action on error messages is critical to the successful implementation of a custom SDK connector. The errors and warnings that result from web service calls will guide the receiver to the most logical problem resolution path.

If a call fails, the receiver should always iterate through the messages collection that will be part of the result set and check the contents of the Name, Summary, Details and RefersTo fields of each message. Often the contents of these messages will indicate what the problem is.

For example, from a DocumentNotFoundError:
<pre class="prettyprint lang-html">The tax document could not be found.
&lt;Details&gt;SO123456789&lt;/Details&gt;
&lt;RefersTo&gt;Avalara.AvaTax.Services.Tax&lt;/RefersTo&gt;
&lt;SeverityLevel&gt;Error&lt;/SeverityLevel&gt;
</pre>
All API calls should contain a path that will execute for:
<pre class="prettyprint lang-cs">ResultCode = SeverityLevel.Success
ResultCode = SeverityLevel.Warning
ResultCode = SeverityLevel.Error
ResultCode = SeverityLevel.Exception
</pre>
Notes:
Typically, Exception is only returned by a system error from the Avalara web service and will be caused by circumstances outside of the client application’s control (i.e. not having an internet connection will return the error).

In general, the path taken for ResultCode= Exception should be the same as for ResultCode=Error.
The only known circumstance under which ResultCode=Warning would be returned is for the API PostTax, when the TotalAmt or TotalTax fields do not agree with the amounts in the Avalara Database. In this case, the PostTax still completes.

At this time, the only API that may return SeverityLevel.Warning is PostTax. To account for the possibility that this may change in the future, we recommend you have a code path that will execute for ResultCode SeverityLevel.Warning for all API calls. This codepath may initially be the same as the codepath that is executed for a ResultCode of SeverityLevel.Error.

The following error message...
<pre class="prettyprint lang-text">The server was unable to process the request due to an internal error.
For more information about the error, either turn on IncludeExceptionDetailInFaults 
(either from ServiceBehaviorAttribute or from the configuration behavior) 
on the server in order to send the exception information back to the client, 
or turn on tracing as per the Microsoft .NET Framework 3.0 SDK documentation and inspect the server trace logs.</pre>
...is returned frequently when one of the following has occurred:
<ul>
	<li>There is a malformed data element (SOAP) being passed in the GetTax call, for example the date 10/01/2010 vs. 2010-10-01 or DocType= salesinvoice vs. SalesInvoice.</li>
	<li>There is a network / internal (local service) interruption preventing the web service call to contact the AvaTax web service.</li>
	<li>There is a configuration problem with your adapter host settings.</li>
</ul>
<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js"></script>


<hr />

<h2>Related Community Discussions</h2>
<div id="gsfn_list_widget">
<div id="gsfn_content">Loading...</div>
</div>
<script src="https://getsatisfaction.com/avalara/widgets/javascripts/f585970/widgets.js" type="text/javascript"></script><script src="https://getsatisfaction.com/avalara/topics.widget?callback=gsfnTopicsCallback&amp;length=240&amp;limit=5&amp;sort=recently_active&amp;user_defined_code=auth" type="text/javascript"></script>
<div id="getsat-widget-8157"></div>
<script src="https://loader.engage.gsfn.us/loader.js" type="text/javascript"></script><script type="text/javascript">// <![CDATA[
if (typeof GSFN !== "undefined") { GSFN.loadWidget(8157,{"containerId":"getsat-widget-8157"}); }
// ]]></script>