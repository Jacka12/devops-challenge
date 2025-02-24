Provide your solution here:

AWS Global Accelerator: Low-latency global traffic distribution.

Application Load Balancer (ALB): Efficiently routes HTTP/HTTPS traffic to backend services.

API Gateway (REST & WebSocket): Secure API management, request validation, and WebSocket support.

Trade Engine: Auto-scalable container orchestration for trading logic.

Kafka: Message Broker  
    Trade Engine Sent data to Kafka Topic as Producer  
    Other Trade Engine Service as Consumer read data in topic and handle process, update to DB

Redis Search: Consumer all data in Kafka topic, storage data as key value in memory 

WebSocket API: Search engine get data from Redis via Redis Search  Module






