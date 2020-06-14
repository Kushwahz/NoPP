# NoPP | Automation Tool

###### Timeline | 2019-20 (Working with Amazon Web Services)

AWS Premium Support has several different teams that assist their customers on distinct AWS Services like EC2, S3, Lambda, CloudFormation, etc. For a Support team in AWS, every day comes with its own set of new challenges. Every team has a Profile Primary role (PP) which keeps on rotating among the engineers of that particular team. Whoever becomes the profile primary of the day, has several responsibilities & tasks allocated. I have been assigned this role several times during my tenure with AWS Premium Support. Across the globe, every day thousands of Support cases via Call, Chat, or Email are being opened by the AWS customers. Being a Profile Primary (PP) means you are the first point of contact for any escalation during your shift time and it is not only from a particular site instead of from all the active sites globally.

## Content
- **[Challenges](https://github.com/Kushwahz/Parivartan20015/blob/master/README.md#challenges)**
- **[Devised Solution](https://github.com/Kushwahz/Parivartan20015/blob/master/README.md#devised-solution)**
- **[Technology Stack](https://github.com/Kushwahz/Parivartan20015/blob/master/README.md#Technology-Stack)**
- **[Implemented Concepts](https://github.com/Kushwahz/Parivartan20015/blob/master/README.md#Implemented-Concepts)**
- **[Functioning](https://github.com/Kushwahz/Parivartan20015/blob/master/README.md#functioning)**
- **[Usage and Impact](https://github.com/Kushwahz/Parivartan20015/blob/master/README.md#Usage-and-Impact)**

## Challenges
On a usual day, Profile Primary (PP) is very much engaged with many tasks that involve human cognitive skills to decide what to do or what to not. And there are also some tasks which don’t require such skills at all like -

- Creating and sharing the morning/afternoon standup summary which includes lots of information from different sources. To create the desired summary, PP (Profile Primary) has to manually go after these sources, gather the data, compile it, and then post it to the team members.

- Every team has its cases queue from where all Support Engineers are assigned their tasks to work upon. Now PP has the responsibility for monitoring this queue to facilitate support engineers to not miss out on high severity cases or live contacts Calls, Chat, etc. They keep updating the team with high severity incoming case volumes, reminding the case SLA time, notifying any Call or Chat waiting for some time in the queue. Also, many other internal processes require PP attention and handful assistance like
  - Sharing team meeting reminder with meeting details
  - Generating available engineers status report
  - Sharing PP shift start and end Summary with a detailed report of the accomplished tasks during the shift
  - Providing other relevant notifications & updates to the team
  - And many other similar tasks that require PP attention and manual efforts.

All the above tasks are facilitated by **[Amazon Chime](https://aws.amazon.com/chime/?chime-blog-posts.sort-by=item.additionalFields.createdDate&chime-blog-posts.sort-order=desc)** which is a communication service that lets you meet, chat, and place business calls inside and outside your organization, all using a single application. So, PP uses the Amazon Chime to communicate all the required information to the team.

## Devised Solution
To perform the above tasks, I created an automation browser script. This script ‘NoPP’ mainly focuses on all those actions that can be automated so to reduce these extra manual monitoring efforts and can utilize the same time to effectively work on other important PP action items. 

## Technology Stack
- Languages | JavaScript and Markdown
- IDE | Sublime Text
- Database | Browser Local Storage
- Graphics Design | System Emoticons

## Implemented Concepts
- [Web scraping internal Amazon sites](https://en.wikipedia.org/wiki/Web_scraping)
- [Webhooks for Amazon Chime](https://docs.aws.amazon.com/chime/latest/dg/webhooks.html)
- JavaScript Components & Controls
	- [Fetch API](https://developers.google.com/web/updates/2015/03/introduction-to-fetch)
	- [Map & Set](https://javascript.info/map-set)
	- [Scheduling: setTimeout and setInterval](https://javascript.info/settimeout-setinterval)
	- [XMLHttpRequest](https://javascript.info/xmlhttprequest)
	- [Local & Session Storage](https://javascript.info/localstorage)
	- [Date and Time Objects](https://javascript.info/date)
	- [String Manipulation](https://www.w3schools.com/js/js_string_methods.asp)

## Functioning
‘NoPP’ script is built using JavaScript and can be installed over GreaseMonkey and TamperMonkey extensions on any compatible browser like Google Chrome, Firefox, etc. It works with AWS Support internal tools and web services. The concerned services trigger the script and then it starts scraping the internal sites for the required information which is finally processed and posted to the respective support team Chime rooms. 

#### Due to company NDA and data confidentiality concerns, I won't be able to share the internal details however I am outlining the underlying concepts and logic behind.

- Created a JavaScript that has the @includes/@matches expressions for the web pages where the script will run.
```
// ==UserScript==
// @name         NoPP
// @namespace    http://tampermonkey.net/
// @version      0.1
// @author       Vishal Singh
// @match        <required site URL>
// @grant        GM_xmlhttpRequest
// @run-at      document-idle
// ==/UserScript==
```
- Provided those internal webpages from where the PP has to gather the required information like the dashboard where all the Support cases for a particular team are listed.

- Now using the HTML DOM Document methods, it retrieves the desired information and then processes it as per the requirement.
```
var liveContact = document.getElementById('accordion').getElementsByClassName('glyphicon')[k].nextSibling.innerHTML;
var engineersTable = document.getElementById("single-agent-availability-table").getElementsByTagName('tbody')[0];
```

- Using the string manipulations & other desired modifications, it generates the required data to be published. For example, in the below code snippet method, it retrieves Case ID from an URL that has this information available.

```
function GetCaseIdFromUrl(url) {
    url = url.split('caseId');
    var caseId = url[1].replace('=', '');
    return caseId;
}
```

- Once the data is ready, the script pushes it to the concerned team chime webhook URL using the below XMLHttpRequest POST request.

```
function PushToChime(chimeMessage) {
    var http = new XMLHttpRequest();
    http.open('POST', ChimeWebHook, true);
    http.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
    if(chimeMessage != null) http.send(JSON.stringify({"Content": chimeMessage}));
}
```

- Also, this script uses the JavaScript Scheduling methods: setTimeout and setInterval, for enabling it to trigger the required action at a particular time of the shift like creating and sharing the standup summary in the morning and evening. For example in the below code snippet, the time interval was created for the morning summary to trigger at 0700 AM - 

```
var startTime = new Date();
var timeTo0700AM = new Date(startTime.getFullYear(), startTime.getMonth(), startTime.getDate(), loginSummary[0], loginSummary[1], 0, 0) - startTime;

$(document).ready(function(){ setTimeout(function () {
    if(timeTo0725AM >= 0) PushToChime(GetStandupSummary());
}, (timeTo0725AM))});
```
- Similarly all the above tasks method which require manual PP efforts can be automated and doesn't require any PP intervention to be posted in the concerned team Chime rooms.

## Usage and Impact
Currently this script has been deployed and being used by several AWS Premium Support teams in Bangalore and Hyderbad site in India. Other sites globally would like to review this too that is in the Roadmap.
