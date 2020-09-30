Create a new package for the kafka producer and consumer.
`mkdir -p /root/devonfw/workspaces/main/devon4j-kafka/KafkaSampleApplication/core/src/main/java/com/devonfw/application/employee/employeemanagement/service/impl/kafka/`{{execute}

<pre class="file" data-filename="devonfw/workspaces/main/devon4j-kafka/KafkaSampleApplication/core/src/main/java/com/devonfw/application/employee/employeemanagement/service/impl/kafka/DeleteEmployeeMessageConsumer.java">
package com.devonfw.application.employee.employeemanagement.service.impl.kafka;

import javax.inject.Inject;
import javax.inject.Named;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.support.Acknowledgment;

import com.devonfw.module.kafka.common.messaging.api.config.MessageReceiverConfig;
import com.devonfw.module.kafka.common.messaging.retry.api.client.MessageRetryOperations;
import com.devonfw.module.kafka.common.messaging.retry.api.config.MessageDefaultRetryConfig;

/**
 * A Listener class with {@link KafkaListener} listens the message for the given topic and group name. This class uses
 * the configuration of {@link MessageReceiverConfig} and also retry pattern of devon kafka to process the consumed
 * message. The retry configuration is from {@link MessageDefaultRetryConfig}.
 *
 * @param <K> the key type
 * @param <V> the value type
 *
 */
@Named
public class DeleteEmployeeMessageConsumer<K, V> {

  private static final Logger LOG = LoggerFactory.getLogger(DeleteEmployeeMessageConsumer.class);

  @Inject
  private MessageRetryOperations<K, V> messageRetryOperations;

  @Inject
  private DeleteEmployeeMessageProcessor<K, V> deleteEmployeeMessageProcessor;

  /**
   * This method is used to listen the message in kafka broker for the given topic and group name in
   * {@link KafkaListener} and also to process the consumed message, to delete the employee exists in the DB.
   *
   * @param consumerRecord the consumed {@link ConsumerRecord}
   * @param acknowledgment the {@link Acknowledgment} to acknowledge the listener that message has been processed.
   */

  @KafkaListener(topics = "employeeapp-employee-v1-delete", groupId = "${messaging.kafka.consumer.groupId}", containerFactory = "kafkaListenerContainerFactory")
  public void consumer(ConsumerRecord<K, V> consumerRecord, Acknowledgment acknowledgment) {

    try {
      processMessageAndAcknowledgeListener(consumerRecord, acknowledgment);
    } catch (Exception e) {
      LOG.error("The Error while processing message: {} ", e);
    }
  }

  private void processMessageAndAcknowledgeListener(ConsumerRecord<K, V> consumerRecord,
      Acknowledgment acknowledgment) {

    this.messageRetryOperations.processMessageWithRetry(consumerRecord, this.deleteEmployeeMessageProcessor);

    // Acknowledge the listener.
    acknowledgment.acknowledge();
  }

}

</pre>

<pre class="file" data-filename="devonfw/workspaces/main/devon4j-kafka/KafkaSampleApplication/core/src/main/java/com/devonfw/application/employee/employeemanagement/service/impl/kafka/DeleteEmployeeMessageProcessor.java">
package com.devonfw.application.employee.employeemanagement.service.impl.kafka;

import javax.inject.Inject;
import javax.inject.Named;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.LoggerFactory;

import com.devonfw.application.employee.employeemanagement.logic.api.Employeemanagement;
import com.devonfw.module.kafka.common.messaging.retry.api.client.MessageProcessor;

import ch.qos.logback.classic.Logger;

/**
 * This is an implementation class for {@link MessageProcessor}. Here in this sample application the consumed message
 * from {@link DeleteEmployeeMessageConsumer} is used as an information for
 * {@link Employeemanagement#deleteEmployee(long)} to delete an employee already exists in the DB.
 *
 * @param <K> the key type.
 * @param <V> the value type.
 *
 */
@Named
public class DeleteEmployeeMessageProcessor<K, V> implements MessageProcessor<K, V> {

  private static final Logger LOG = (Logger) LoggerFactory.getLogger(DeleteEmployeeMessageProcessor.class);

  @Inject
  private Employeemanagement employeemanagement;

  @Override
  public void processMessage(ConsumerRecord<K, V> message) {

    long employeeId = 0L;
    try {
      employeeId = Long.parseLong(message.value().toString());
    } catch (Exception e) {
      LOG.warn("Message conversion failed and it will be ignored", e);
    }

    this.employeemanagement.deleteEmployee(employeeId);
  }

}

</pre>