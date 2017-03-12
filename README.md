# Tesibit

© 2017 Tesibit, Inc. All Rights Reserved.

The goal of this is to make clear how to connect with Tesibit's platform in order to send data and receive commands for your application.

## Prerequisites

A web server to run any of java, C# or Node.js.

## Communication Protocol

The communication can be done through AMQP, MQTT, HTTPS protocols

## Set authentication key
```
string deviceKey = "authentication key";
```
## JSON Schema

Send the data as JSON format according to following schema

```JSON
{
	"sensors": [
		{
			"sensor": "air",
			"value": 9.59782450534302
		},
		{
			"sensor": "power",
			"value": 709.9300515884208
		}
	]
}

```
## Send Data

To send your data to Tesibit you should import the library to your web service  to  implement the following code to your web service

```C#
	private static async void SendData()
    {
        double WindSpeed = 10;
        

        while (true)
        {
            

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
