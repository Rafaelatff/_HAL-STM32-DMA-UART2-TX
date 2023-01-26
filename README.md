# _HAL-STM32-DMA-UART2-TX

This repository was create to follow the content of the course 'ARM Cortex M Microcontroller DMA Programming Demystified'.

I am using:
* NUCLEO-F401RE Board 
* STM32Cube FW_F4 V1.27.1 
* STM32CubeIDE version 1.10.1

# Code

We generated the code using the STM32CubeMX, configured the IRQHandles for the EXT15_10, DMA1_Stream6 and Systick. We set the option to use FIFO.

In code we created a new source file with the name *data_stream.c* and coded the following:

```c
#include <stdint.h>

uint8_t data_stream [] = "This repository was create to follow the content of the course 'ARM Cortex M Microcontroller DMA Programming Demystified'.\
I am using:\
NUCLEO-F401RE Board\
STM32Cube FW_F4 V1.27.1\
STM32CubeIDE version 1.10.1";

uint32_t data_len = sizeof(data_stream);

```

In *stm32fxx_it.c* we extern the variables that will be used in here, such as:

```c
/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */
	//We need to extern our variables
	extern UART_HandleTypeDef huart2;
	extern uint8_t data_stream[];
	extern uint32_t data_len;
/* USER CODE END 0 */
```

In the Button Habdler (EXT15_10_IRQHandler) we call the UART transmit DMA:

```c
void EXTI15_10_IRQHandler(void)
{
  /* USER CODE BEGIN EXTI15_10_IRQn 0 */
	// Here we will send the UART_TX request to the DMA controller
	HAL_UART_Transmit_DMA(&huart2, data_stream, (uint16_t *) data_len);
  /* USER CODE END EXTI15_10_IRQn 0 */
  HAL_GPIO_EXTI_IRQHandler(B1_Pin);
  /* USER CODE BEGIN EXTI15_10_IRQn 1 */

  /* USER CODE END EXTI15_10_IRQn 1 */
}
```

Transmittion starts right after the code enable the DMA transfer for transmit request by setting the DMAT bit in the UART CR3 register. It is possible to debug and see it by adding a breakpoint in the following line (*stm32f4xx_hal_uart.c* source file):

```c
	SET_BIT(huart->Instance->CR3, USART_CR3_DMAT);
```

Results so far:

![image](https://user-images.githubusercontent.com/58916022/214940313-3be16c52-b0df-41fb-a98e-4793d70994aa.png)
