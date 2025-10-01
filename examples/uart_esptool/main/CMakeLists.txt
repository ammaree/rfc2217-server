idf_component_register(
    SRCS "uart_esptool_main.c"
    PRIV_INCLUDE_DIRS "."
    PRIV_REQUIRES esp_system lwip nvs_flash esp_netif esp_event esp_driver_uart esp_driver_gpio)
