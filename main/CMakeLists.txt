idf_component_register(SRCS "main.c"
                        INCLUDE_DIRS "." "../include")
add_prebuilt_library(quarklink_client "../lib/libquarklink-client-esp32-s3-ds-v1.4.3-debug.a"
                    PRIV_REQUIRES nvs_flash esp_http_client esp_https_ota app_update mbedtls)
                                          
target_link_libraries(${COMPONENT_LIB} PRIVATE quarklink_client)