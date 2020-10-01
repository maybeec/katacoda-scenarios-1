Create a new package for the kafka producer and consumer classes.
`mkdir -p /root/devonfw/workspaces/main/devon4j-kafka/core/src/main/java/com/devonfw/application/employee/employeemanagement/service/impl/kafka/`{{execute}}

Copy the following classes into the IDE by clicking 'Copy to editor'.

The first class contains the consumer, which creates a new employee. The @KafkaListener annotation configures on which topic the consumer have to listen. When the consumer gets a message, the message will be processed using the SaveEmployeMessageProcessor class.

<pre class="file" data-filename="devonfw/workspaces/main/devon4j-kafka/core/src/main/java/com/devonfw/application/employee/employeemanagement/service/impl/kafka/SaveEmployeeConsumer.java">
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
public class SaveEmployeeConsumer<K, V> {

  private static final Logger LOG = LoggerFactory.getLogger(SaveEmployeeConsumer.class);

  @Inject
  private MessageRetryOperations<K, V> messageRetryOperations;

  @Inject
  private SaveEmployeMessageProcessor<K, V> saveEmployeeMessageProcessor;

  /**
   * This method is used to listen the message in kafka broker for the given topic and group name in
   * {@link KafkaListener} and also to process the consumed message, to create an employee in the DB.
   *
   * @param consumerRecord the consumed {@link ConsumerRecord}
   * @param acknowledgment the {@link Acknowledgment} to acknowledge the listener that message has been processed.
   */

  @KafkaListener(topics = "employeeapp-employee-v1-add", groupId = "${messaging.kafka.consumer.groupId}", containerFactory = "kafkaListenerContainerFactory")
  public void consumer(ConsumerRecord<K, V> consumerRecord, Acknowledgment acknowledgment) {

    try {
      processMessageAndAcknowledgeListener(consumerRecord, acknowledgment);
    } catch (Exception e) {
      LOG.error("The Error while processing message: {} ", e);
    }
  }

  private void processMessageAndAcknowledgeListener(ConsumerRecord<K, V> consumerRecord,
      Acknowledgment acknowledgment) {

    this.messageRetryOperations.processMessageWithRetry(consumerRecord, this.saveEmployeeMessageProcessor);

    // Acknowledge the listener.
    acknowledgment.acknowledge();
  }
}
</pre>

The SaveEmployeMessageProcessor class processes a message. It first converts the message into an employee object. Then the employee will be saved to the database.

<pre class="file" data-filename="devonfw/workspaces/main/devon4j-kafka/core/src/main/java/com/devonfw/application/employee/employeemanagement/service/impl/kafka/SaveEmployeMessageProcessor.java">
package com.devonfw.application.employee.employeemanagement.service.impl.kafka;

import javax.inject.Inject;
import javax.inject.Named;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.LoggerFactory;

import com.devonfw.application.employee.employeemanagement.logic.api.Employeemanagement;
import com.devonfw.application.employee.employeemanagement.logic.api.to.EmployeeEto;
import com.devonfw.module.kafka.common.messaging.retry.api.client.MessageProcessor;
import com.fasterxml.jackson.databind.ObjectMapper;

import ch.qos.logback.classic.Logger;

/**
 * This is an implementation class for {@link MessageProcessor}. Here in this sample application the consumed message
 * from {@link DeleteEmployeeMessageConsumer} is used as an information for
 * {@link Employeemanagement#saveEmployee(EmployeeEto)} to save as an employee in the DB.
 *
 * @param <K> the key type.
 * @param <V> the value type.
 *
 */
@Named
public class SaveEmployeMessageProcessor<K, V> implements MessageProcessor<K, V> {

  private static final Logger LOG = (Logger) LoggerFactory.getLogger(SaveEmployeMessageProcessor.class);

  @Inject
  private Employeemanagement employeemanagement;

  @Override
  public void processMessage(ConsumerRecord<K, V> message) {

    EmployeeEto convertedValue = null;
    try {
      convertedValue = new ObjectMapper().readValue(message.value().toString(), EmployeeEto.class);
    } catch (Exception e) {
      LOG.warn("Message conversion failed. Message will be ignored.", e);
    }
    if (convertedValue != null) {
      this.employeemanagement.saveEmployee(convertedValue);
    }
  }

}

</pre>