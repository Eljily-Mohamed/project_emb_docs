# Wireless Connection

# Zigbee

Zigbee is a wireless communication protocol designed for low-power, low-data-rate applications. It operates on the IEEE 802.15.4 standard and is commonly used in home automation, industrial control, and sensor networks. Zigbee networks can support hundreds of devices, making it suitable for applications requiring long battery life and reliable communication over short to medium distances.

## UART1 Configuration Steps

To configure UART1 in the .config file and define different pins and GPIO configurations:

```cpp

  // USART1
#define USE_USART1
#define USART1_GPIO_PORT    _GPIOB
#define USART1_GPIO_PINS    PIN_3
#define USART1_GPIO_CFG     PIN_MODE_ALTFUNC | PIN_OPT_AF7

// USART2
#define USE_USART2
#define USART2_GPIO_PORT    _GPIOA
#define USART2_GPIO_PINS    PIN_2 | PIN_3 | PIN_9
#define USART2_GPIO_CFG     PIN_MODE_ALTFUNC | PIN_OPT_AF7

```
For more details, check the documentation, and you need to be familiar with using the ENIB Kit for Embedded Systems

## UART1 Initialization and Callback

```cpp
  
  int uart_init(USART_t *u, uint32_t baud, uint32_t mode, OnUartRx cb)
{
    IRQn_t irq_number;
    uint32_t irq_priority;
    
    if (u == _USART1) {
#ifdef USE_USART1
        // Enable USART clocking
        _RCC->APB2ENR |= (1 << 4); 
        // Configure Tx/Rx pins
        io_configure(USART1_GPIO_PORT, USART1_GPIO_PINS, USART1_GPIO_CFG, NULL);
        usart1_cb = cb;
        irq_number = USART1_IRQn;
        irq_priority = USART1_IRQ_PRIORITY;
        // Configure USART speed
        u->BRR = sysclks.apb2_freq / baud;
#else
        return -1; // USART1 not enabled
#endif
    }
    
    /*Reset usart conf */
    u->CR1&=~(1<<12); //nb de bit par data raz
    u->CR1&=~(7<<8);  //parité raz
    u->CR2&=~(3<<12); //bit stop

	u->GTPR = 0;
	u->CR3 = 0;
    /*conf à partir mode */
    /*  mode = b10-b9-b8-x-x-b5-b4-x-x-x-b0
        b0 = 8/9 bits dans CR1 b12
        b5/b4 = nb bit stop dans CR2 b13/b12
        b10/b9/b8 = parité , 10 controle parité (1 activé/0 désactivé), 9 type parité (Odd/Even) , 8 PEIE parity Error interrupt enable
    */
        u->CR1 |=((mode & 0x1)<<12) | (mode & 0x700) | (3<<2) | (1<<13);  //conf + tx/rx activé + usart enable
        u->CR2 |=((mode & 0x30)<<8); //bit stop positionné en b12/b13 de CR2
                
        // Setup NVIC
        if (cb) {
            /* A COMPLETER */
            NVIC_SetPriority(irq_number, irq_priority);
            NVIC_EnableIRQ(irq_number);
            u->CR1 |= (1<<5);// Receiver Not Empty Interrupt Enable

        }
}

```

## Test 
In the main file, we need to add a callback function to handle communication via Zigbee:

```cpp

static void on_zigbee_command_received(char c) {
    command = c;
    uart_printf(UART_TO_USE, "\r\nZigbee command received: %c\r\n", c);
}

// Initialization required
uart_init(_USART2, 115200, UART_8N1, on_command_received);
uart_init(_USART1, 115200, UART_8N1, on_zigbee_command_received);

```

Now that we have completed these steps, we can verify if everything functions correctly by conducting the same test mentioned in the section above, but using the 'tty/USB0' port.

# Bluetooth Low Energy (BLE)

Bluetooth Low Energy (BLE) is a wireless communication technology designed for short-range communication with minimal power consumption. It is commonly used in applications such as wearable devices, smart home products, and health monitoring systems.

## Hardware Requirements

- **X-NUCLEO-IDB05A2**: This is a Bluetooth Low Energy (BLE) expansion board based on the SPBTLE-RF module. It can be used with STM32 Nucleo development boards to add BLE functionality to your projects.

## Configuring Our Services

### Temperature Service

To create a BLE service for temperature data, we use the characteristic UUID `0x2A6E`.

```cpp
// TEMPERATURE CHAR : 0x2A6E
primary_short_uuid[0] = 0x6E;
primary_short_uuid[1] = 0x2A;
ret = aci_gatt_add_char(HWServW2STHandle, UUID_TYPE_16, primary_short_uuid, 2,
                        CHAR_PROP_NOTIFY | CHAR_PROP_READ,
                        ATTR_PERMISSION_NONE,
                        GATT_NOTIFY_READ_REQ_AND_WAIT_FOR_APPL_RESP,
                        16, 0, &TemperatureCharHandle);

if (ret != BLE_STATUS_SUCCESS) {
    goto fail;
}
HAL_Delay(100);

```

### Color Temperature Service

To create a BLE service for color temperature data, we use the characteristic UUID 0x2A63.

```cpp
// Color temp 0x2A63
primary_short_uuid[0] = 0x63;
primary_short_uuid[1] = 0x2A;
ret = aci_gatt_add_char(HWServW2STHandle, UUID_TYPE_16, primary_short_uuid, 2,
                        CHAR_PROP_NOTIFY | CHAR_PROP_READ,
                        ATTR_PERMISSION_NONE,
                        GATT_NOTIFY_READ_REQ_AND_WAIT_FOR_APPL_RESP,
                        16, 0, &TempColorCharHandle);

if (ret != BLE_STATUS_SUCCESS) {
    goto fail;
}
HAL_Delay(100);

return BLE_STATUS_SUCCESS;

fail:
return BLE_STATUS_ERROR;

```

### Updating Environmental Data

The function Environmental_Update updates the temperature and color temperature values

```cpp

tBleStatus Environmental_Update(int16_t Temp, int16_t color_temp)
{
    tBleStatus ret;
    uint8_t buff_Temp[2];
    uint8_t buff_color_temp[2];

    STORE_LE_16(buff_Temp, Temp);
    ret = aci_gatt_update_char_value(HWServW2STHandle, TemperatureCharHandle, 0, 2, buff_Temp);
    if (ret != BLE_STATUS_SUCCESS) {
        return BLE_STATUS_ERROR;
    }

    STORE_LE_16(buff_color_temp, color_temp);
    ret = aci_gatt_update_char_value(HWServW2STHandle, TempColorCharHandle, 0, 2, buff_color_temp);
    if (ret != BLE_STATUS_SUCCESS) {
        return BLE_STATUS_ERROR;
    }

    return BLE_STATUS_SUCCESS;
}

```

### Sending Data
After receiving a notification, you can send the updated values

```cpp

void SendEnvironmentalData(void)
{
    static float temperature, humidity;
    uint16_t red, green, blue, clear, color_temp;
    float x, y;
    int32_t TempToSend = 0;
    int32_t decPart, intPart;
    int16_t ret;
    ret = sht4x_measure_blocking_read(&temperature, &humidity);
    tcs34725_read_color(&red, &green, &blue, &clear);
    color_temp = calculateColorTemperature(red, green, blue, &x, &y);
    MCR_BLUEMS_F2I_1D(temperature, intPart, decPart);
    TempToSend = intPart * 10 + decPart;
    printf("\r\ntemperature: %d.%d C", intPart, decPart);
    printf("\r\ntemperature en kelvin: %d K", color_temp);
    Environmental_Update(TempToSend, color_temp);
    HAL_Delay(1000);
}

```

## Summary

In this project, we successfully set up a Bluetooth Low Energy (BLE) communication system using the X-NUCLEO-IDB05A2 expansion board. We configured services for both temperature and color temperature data, updated these values, and sent the data using BLE notifications.

