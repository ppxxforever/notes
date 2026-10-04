# C

## _Generic()

_Generic()是C11的泛型选择，根据第二个参数的类型，在编译时决定调用哪个函数 如果是char*或者const char * 就使用单个订阅，esp_mqtt_topic_t 就使用多个订阅，后面(client_handle, topic_type, qos_or_size),就是这些函数的参数
```c
 #define esp_mqtt_client_subscribe(client_handle, topic_type,
 qos_or_size) _Generic((topic_type),
       char *: esp_mqtt_client_subscribe_single,
       const char : esp_mqtt_client_subscribe_single,
       esp_mqtt_topic_t: esp_mqtt_client_subscribe_multiple
     )(client_handle, topic_type, qos_or_size)
```
