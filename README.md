# Tesibit

© 2014 Alfresco Software, Inc. All Rights Reserved. See license.txt.

The goal of this new project is to provide examples on how to extend Alfresco with the Alfresco Maven SDK.

## Prerequisites

A web server to run any of java, C# or Node.js
If you are new to Alfresco and the Alfresco Maven SDK, you should start by reading [Jeff Potts' tutorial on the subject](http://ecmarchitect.com/alfresco-developer-series-tutorials/maven-sdk/tutorial/tutorial.html).

# Running the examples

All examples are selfcontained with the Alfresco Maven SDK, so you'll be able to get up and running quickly. This means that the only thing you will need is JDK and Maven. The SDK will allow you to start up an embedded Tomcat server with an embedded H2 database, so you will be up and running in no time.

Some examples ties into both Alfresco and Share. In those cases there will be an AMP module for each `repo` will be for the repository tier (Alfresco) and `share` will be for Share.

## Running Alfresco AMP examples (repo)

To run the Alfreco AMP examples you enter the `repo` folder and run the "run.sh" script. Alternatively you will need to adjust the JVM memory settings. This is not needed for Share. Enter the `repo` folder and run the following command:

	export MAVEN_OPTS="-Xms1024m -Xmx4096m -XX:PermSize=1024m"
	mvn integration-test -P amp-to-war

Alfresco will be available on [http://localhost:8080/alfresco](http://localhost:8080/alfresco)

## Running Share AMP examples (share)

To run Share AMP examples you run enter the `share` folder and run the "run.sh" script. Alternatively you run this command:

	mvn integration-test -P amp-to-war
	
Share will be available on [http://localhost:8081/alfresco](http://localhost:8081/alfresco) (notice that the port is 8081).

## Send data

To send your data to tesibit you should implement the following code to your web service

```C#
	private static async void SendDeviceToCloudMessagesAsync()
    {
        double avgWindSpeed = 10; // m/s
        Random rand = new Random();

        while (true)
        {
            double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

            var telemetryDataPoint = new
            {
                deviceId = "myFirstDevice",
                windSpeed = currentWindSpeed
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            string levelValue;

            if (rand.NextDouble() > 0.7)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else
            {
                levelValue = "normal";
            }

            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("level", levelValue);

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
```
## Receive Commands
```C#
	private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
 {
     var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
     while (true)
     {
         if (ct.IsCancellationRequested) break;
         EventData eventData = await eventHubReceiver.ReceiveAsync();
         if (eventData == null) continue;

         string data = Encoding.UTF8.GetString(eventData.GetBytes());
         Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
     }
 }
```
