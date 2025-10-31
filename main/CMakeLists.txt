idf_component_register(
    SRCS "main.c"
         "sensirion_i2c_hal.c"
         "sensirion_i2c.c"
         "sensirion_common.c"
         "sen5x_i2c.c"
    INCLUDE_DIRS "."
    REQUIRES freertos esp_wifi esp_event nvs_flash esp_netif mqtt driver
)
