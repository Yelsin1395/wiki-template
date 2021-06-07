Before this new change, every bus application that needed to run was created as a new host application (copy and paste). And within this host, the configuration of which queues and topics and how many subscribers were running was embedded.

This was a very tight-coupled logic and also was a misuse of the Azure Service Bus Topics technology (which is a publisher-subscriber model).

This caused that our bus processing performance and logic was very rigid and hard to scale. Therefore to solve this, we decided to make the following changes.

# New Host.Bus repository and application
Now we will have a single code to run a host for processing the bus (queues and topics). For this to run, in the build script we would download the latest version and include in that as part of the published artifact and deployment. This unique Host.Bus would be configured per command line to which dll contains the handler that will pick up the message and process it.

# A single host is a single subscriber
Before, every subscriber would be within a single host and even more, several topics were introduced in the same host. It was a gigantic bus host that processed N topics with M subscriptions all causing a lot of thread-switching causing more harm than good.
The new model with the single host bus would simplify everything and force that one instance of the host would only allowed to run a single message type and a single subscriber.
If you want more subscriptions, just create another run.bat file and install it as a second webjob.

# A single queue for a single message type
Linked to the prior item, many message types were included in the same queue or topic. Therefore in 2019-07 we saw that the queues were getting overflown with several thousands of messages and this halted the entire Juntoz operation because the same queue to publish to the web was used to update the stocks in the catalog, etc. From now on, we will have a single queue for one message type only.

# Topic publisher does not know who subscribed
In the past, when the topic was created, the system "automatically" created 10-12 subscribers, all running in the same host (which was also a major blocker in performance). Each named mytopicsubs-1, mytopicsubs-2, etc.

How was this balanced across the subscriptions? Who generated the message assigned a MessageNumber=X property and each subscription had an automatic filter assigned to be the same MessageNumber.
In the end, the topic publisher assigned which subscriber read the message, which beats the whole purpose of this model (P-S). The publisher should not know who is going to read. The decision of who should be within each subscriber.

# No one will know which topic to use
We will centralize this logic in a new component called Bus Orchestrator. Every publisher to any topic or queue will be changed to push to the orchestrator topic alone.
This orchestrator topic will pick up the message and analyze its contents and route them to the correct topic.
This helps in the flexibility and scalability because this will be in code, therefore we can route in whatever logic we want.

# Future
The future is moving every handler to Azure Functions, they scale infinitely and do not require any host and are not limited by physical hardware.

# How to debug a bus message
1. Go to Azure Devops > Pipelines > Host.Bus > Go to the last successful build > Artifacts > Download
2. Unzip the file into the .\host folder
It should look like:
.\host\Eagle.Bus.Host.exe
.\host\Eagle.Bus.Host.config
.\host\ ... dlls ...
3. Modify these lines in Eagle.Bus.Host.config :
    <add key="apiConfigUrl" value="xxx" />
    <add key="WhitelabelStoreIds" value="xxx" />

Replace the "xxx" by the correct values.
For staging,
use apiConfigUrl: "https://api-config.juntoz-staging.com/"
use WhitelabelStoreIds: "20,22,23,1034,1036,1141,1249,1823,1827,2277,2285,2355,2369,3175"

4. Go to folder of the job to debug e.g. \continuous\catalog_algolia_product
5. Open run.bat in text editor.

Modify these lines
if "%debug%"=="1" (
set bh=..\..\host
set bt=xxx
)

Set the paths to the host and the task dll.
The path to the host is the one you did in step 2.
The path to the task dll is probable to be: ..\..\..\..\..\Eagle.Bus.Catalog.SyncProduct.Algolia\bin\debug

Modify this line
--buscs "xxx"

where you need to put the connection string to the Azure Service Bus server.

6. Open a command line and go to the path where the run.bat is.
7. First enter this command `set debug=1` and hit Enter. This will set the env variable needed so your paths are used.
8. Enter `run.bat` and hit Enter, and this will start running the application.
9. In order to debug it, go to Visual Studio, open the project where the handler is, go to Debug Menu/Attach to process, look for Eagle.Bus.Host.exe and click on Attach. Make sure that the code you are seeing is the version that is compiled and running. Otherwise you will see breakpoints not stopped on, or mismatch in lines of code.
