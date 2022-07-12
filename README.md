# h201S5_08JhianpolRamos
Requisito hackaton 2022 - 5toSemestre

# Implementado con :
- Spring Cloud Stream con Apache Kafka Binder
- Aprovechando el modelo de servidor sin bloqueo de Netty
- Primavera WebFlux

Las operaciones que persisten en la base de datos tienen un conjunto de subprocesos dedicado y bien ajustado, ya que puede aislar las llamadas de E/S de bloqueo por separado de la aplicación.

# Diseño de sistemas

<img src ="https://raw.githubusercontent.com/martinsam16/saga-choreography/main/diagram.png" align="center" style="width: 900px"/>

# Flujo de datos
- La aplicación Order Service toma un correo electrónico Ordercomo una solicitud, que crea y envía un correo electrónico OrderPurchaseEvental tema de Kafka ordersque se procesa OrderPurchaseEventHandleren el servicio de pago. 
- OrderPurchaseEventHandlerprocesa el evento y calcula si el usuario tiene suficiente crédito. PaymentEventSi es así, establece el estado generado en APPROVED, de lo contrario DECLINED.
- A PaymentEventse emite al tema de Kafka payments, que PaymentEventHandlerescucha en la aplicación de servicio de pago.
- Si el PaymentEventestado es APPROVED, guarda la transacción en el TransactionRepository. Se TransactionEventemite una A al transactionstema.
- El TransactionEventConsumerlee esto en el servicio de pedidos, si tiene éxito, lo OrderRepositoryguarda como ORDER_COMPLETED, de lo contrarioORDER_FAILED
