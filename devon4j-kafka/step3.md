## Download the Kafka server
`cd /root/`{{execute}}

`curl http://apache.mirror.iphh.net/kafka/2.6.0/kafka_2.13-2.6.0.tgz --output /root/kafka.tgz`{{execute}}

`mkdir kafka`{{execute}}

`tar -xzf kafka.tgz -C kafka/ --strip-components=1`{{execute}}