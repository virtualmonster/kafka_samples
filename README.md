# kafka_samples

Scenario
A Payment app which takes message from a Kafka topic PaymentRequest and sends back  the response in another topic PaymentResponse
 
	1. Test the application by sending the request and receiving the responses in RIT
	2. Record the messages and create all the resources in RIT
	3. Create the Stub direct and through proxy for the application in RIT
 
Kafka Broker Set-Up
	1. Download Kafka from https://www.apache.org/dyn/closer.cgi?path=/kafka/2.4.0/kafka_2.13-2.4.0.tgz and unzip it
	2. Start the zookeeper from <install_dir>\ bin\windows
			a. zookeeper-server-start.bat ..\..\config\zookeeper.properties
	3. Start the kafka broker from <install_dir>\ bin\windows
			a. kafka-server-start.bat ..\..\config\server.properties
	4. Create the required topics in Kafka
			a. kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic PaymentRequest
			b. kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic PaymentResponse
	5. Check whether the topics were created properly
			a. kafka-topics.bat --list --zookeeper localhost:2181
	6. Try producing the same messages and consuming the same for both the topics to make sure they are created properly
			a. kafka-console-producer.bat --broker-list localhost:9092 --topic PaymentRequest
			b. kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic PaymentRequest  --group <some group name>
		 
RIT Project  - Import the attached project 
 
	1. Payment Application Test
			a. Run the test names “PaymentTest”
			b. Meanwhile publish a message “Success” in the Payment Response – To simulate the response from Payment application
		i. kafka-console-producer.bat --broker-list localhost:9092 --topic PaymentResponse
		ii. The test should pass as it got a valid response
	2. Recording Message (Direct) 
			a. Go to Logical Transport – Recording Studio . Enter PaymentRequest. Add a monitor to this transport and hit recrd button
			b. Try sending messages to either of these topics. The messages should get recorded. Then you can create RIT resources from the recorded events. 
		i. Note that there is no way to tie the requests and responses. So only the publish events will get recorded
			c. Send a message to PaymentRequest topic through a RIT test. It should get recorded by the recording studio. 
	3. Stubbing (Direct Mode)
			a. Run the stub PaymentOperationStub
			b. Try publishing a message to PaymentRequest and the stub should respond with a message”Success” in PaymentResponse topic.
			c. Validate it through the cosole consumer for PaymentResponse topic
		i. kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic PaymentResponse –group <somegroupname>
		 
	4. Recording (Through Proxy)
			a. Add an entry for the Kafka port forwarding in RIT proxy
		i. <forward bind="localhost:2000" destination="localhost:9092" type="kafka" />
			b. Change the Kafka physical transport and enter the proxy host and port and change the recording/subbing mode to use proxy. 
			c. Add a monitor for the transport and start recording
			d. Send a message to PaymentRequest through a RIT test (I am finding some issue in my environment while sending the message to the proxy through the command line producer) . It should get recorded by the recording studio.
 
				 
	5. Stubbing (Proxy Mode)
			a. Add an entry for the Kafka port forwarding in RIT proxy
		i. <forward bind="localhost:2000" destination="localhost:9092" type="kafka" />
			b. Change the Kafka physical transport and enter the proxy host and port change the recording/stubbing mode to use proxy.
			c. Start the PaymentOperation stub
			d. Send a message to PaymentRequest topic through a RIT test. The stub should get triggered. Confirm that the message is not going to the PaymentRequest topic in the broker as the stub is configured to discard the message.
			e. A response from the stub should be sent to the PaymentResponse topic. Validate it through the cosole consumer for PaymentResponse topic
		i. kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic PaymentResponse –group <somegroupname>
![image](https://user-images.githubusercontent.com/9676230/231569247-725472d7-74a1-4a47-b0af-f4ec19fdb6bb.png)
