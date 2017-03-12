# Tesibit

© 2017 Tesibit, Inc. All Rights Reserved.

The goal of this is to make clear how to connect with Tesibit's platform in order to send data and receive commands for your application.

## Prerequisites

A web server to run any of java, C# or Node.js.

## Communication Protocol

The communication can be done through AMQP, MQTT, HTTPS protocols. By default, the Create method creates a DeviceClient instance that uses the AMQP protocol to communicate with IoT Hub. To use the MQTT or HTTP protocol, use the override of the Create method that enables you to specify the protocol.

## Set authentication key
```
string deviceKey = "authentication key";
```
## JSON Schema

Send the data as JSON format according to the following schema

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

To send your data to Tesibit you should import the library and call the function from your web service.

```C#

Import Devices.Client;

deviceClient = DeviceClient.Create(new DeviceAuthenticationWithRegistrySymmetricKey("device_name", deviceKey));
SendData();
.
.
.

	private static async void SendData()
    {
     {
            double WindSpeedValue = "speed data to send" // m/s
            double PowerValue = "Power data to send"//KW
            

            while (true)
            {
               

                var telemetryDataPoint = new

                {
                    
                    sensors = new sensorPoint[]
                    {
                        new sensorPoint {sensor="air",value=WindSpeedValue },
                        new sensorPoint {sensor="power",value=PowerValue }


                    },
                    
                    
                };

                //convert data to json
		var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));
                
                await deviceClient.SendEventAsync(message);
                

               //send data every 1000 milliseconds
               Task.Delay(1000).Wait();
                
            }
        }
```
## Receive Commands

To receive commands from Tesibit you should import the library and simply call the function from your web service.
```C#

Import Devices.Client;


ReceiveCommands();

.
.
.


	private static async void ReceiveCommands()
        {
            
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;                
                Byte command = receivedMessage.GetBytes();
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
```
