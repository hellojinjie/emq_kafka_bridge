
# emq_kafka_bridge

This is a plugin for the EMQ broker that sends all messages received by the broker to kafka.

## Build the EMQ broker

1. Clone emq-relx project

   We need to clone the EMQ-x project [GITHUB](https://github.com/emqtt/emq-relx)

```shell
  git clone https://github.com/emqtt/emq-relx.git
```

2. Add EMQ Kafka bridge as a DEPS
   Adding EMQ kafka bridge as a dependency in the Makefile.

   1. search for `DEPS +=` and add to the end
      > emq_kafka_bridge

   2. search for
     ```text
     # COVER = true
     #NO_AUTOPATCH = emq_elixir_plugin
     include erlang.mk
     ```
     add the following line before the above lines
     >dep_emq_kafka_bridge = git https://github.com/iotblue/emq_kafka_bridge.git master

3. Add load plugin in relx.config
   >{emq_kafka_bridge, load},

4. Build
   ```shell
   cd emq-relx && make
   ```

Configuration
----------------------
You will have to edit the configurations of the bridge to set the kafka Ip address and port.

Edit the file emq-relx/deps/emq_kafka_bridge/etc/emq_kafka_bridge.config
```
[
  {emqttd_kafka_bridge, [{values, [
	  %%edit this to address and port on which kafka is running
      {bootstrap_broker, {"127.0.0.1", 9092} },
	  %% partition strategies can be strict_round_robin or random
      {partition_strategy, strict_round_robin},
      %% Change the topic to produce to kafka. Default topic is "Kafka". It is on this topic that the messages will be sent from the broker to a kafka consumer
	  {kafka_payload_producer_topic, <<"kafka">>}
    ]}]}
].
```

Start the EMQ broker and load the plugin 
-----------------
1) cd emq-relx/_rel/emqttd
2) ./bin/emqttd start
3) ./bin/emqttd_ctl plugins load emqttd_kafka_bridge

Test
-----------------
Send a MQTT message on a random topic from a MQTT client to your EMQ broker.

The following should be received by your kafka consumer :

  {"topic":"yourtopic", "message":[yourmessage]}
This is the format in which kafka will receive the MQTT messages

If Kafka consumer shows no messages even after publishing to EMQTT - ACL makes the plugin fail, so please remove all the ACL related code to ensure it runs properly. We will soon push the updated (Working) code to the repository. 

## License

This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details

