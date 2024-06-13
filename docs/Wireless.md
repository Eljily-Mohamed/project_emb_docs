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

Typing ....