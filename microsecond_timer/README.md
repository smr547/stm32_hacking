# Microsecond timer

I plan to write some hardware drivers and a microsecond timer would be handy -- lets build one

## What's out there already?

Let's see what Google says on this topic. I tried the following search term ``best way to code microsecond delays on stm32`` and
only reviewed the first page of answers.

* [An excellent tutorial](https://deepbluembedded.com/stm32-delay-microsecond-millisecond-utility-dwt-delay-timer-delay/) 
discussing DWT and STM hardware timers
* [Hardware timer](https://controllerstech.com/create-1-microsecond-delay-stm32/) as basis for utility function
* [Forum discussion](https://electronics.stackexchange.com/questions/451512/what-is-the-best-way-to-implement-a-microseconds-precise-delay-measurement) 
with suggestions
* [Excellent analysis](https://www.carminenoviello.com/2015/09/04/precisely-measure-microseconds-stm32/) in this post, with code examples
* [Another discussion group](https://electronics.stackexchange.com/questions/451512/what-is-the-best-way-to-implement-a-microseconds-precise-delay-measurement)

* [Simple DWT code](https://github.com/keatis/dwt_delay)

I'll read over this material and summaries here soon.

Consider buying the ebook [Mastering STM32](https://leanpub.com/mastering-stm32?utm_campaign=mastering-stm32&utm_medium=embed&utm_source=embedder) 
-- see the [Table of Contents](https://www.carminenoviello.com/mastering-stm32-toc.pdf)




## The code
Delay function:
```
#pragma GCC push_options
#pragma GCC optimize ("O3")
void delayUS_DWT(uint32_t us) {
	volatile uint32_t cycles = (SystemCoreClock/1000000L)*us;
	volatile uint32_t start = DWT->CYCCNT;
	do  {
	} while(DWT->CYCCNT - start < cycles);
}
#pragma GCC pop_options

Main loop
```
while (1) {
    HAL_GPIO_TogglePin(SIG_OUT_GPIO_Port, SIG_OUT_Pin);
    delayUS_DWT(5);
}
```
Include code notes here

## Results

With SYSCLK running at 100MHz the code toggled a GPIO pin at maximum rate with various delay intervals

| Delay   | Meas.  | Period  | Freq   |
| uS      | uS     | uS      | MHz    |
|---------|--------|---------|--------|
| none    |   0.42 |   0.84  | 1.20   |
|   0     |   0.73 |   1.4   | 0.68   |
|   1     |   1.72 |   3.4   | 0.29   |
|   2     |   2.72 |   5.4   | 0.184  |
|   5     |   5.68 |  11.37  | 0.088  |
|  10     |  10.72 |  21.45  | 0.0446 |

## Observations

* HAL library overhead is significant (maximum achievable output frequency of 1.20 MHz with a 100 MHz system clock
* 0.72 uSec fixed overhead in function delay() function call and processing
* function is useful for delays of 1 uSec or more with and
* precision improves with delay time

## Improving the code
To reduce overheads and preccision try:

* inlining the delay code
* refactoring 

In the interest of maintaining portability, we continue to use the HAL library

## Inlined code

```
	uint32_t delay_us = 10;
	uint32_t cycles = (SystemCoreClock / 1000000L) * delay_us;
	volatile uint32_t start;

	while (1) {
		HAL_GPIO_TogglePin(SIG_OUT_GPIO_Port, SIG_OUT_Pin);
		start = DWT->CYCCNT;
		do {
		} while (DWT->CYCCNT - start < cycles);
	}
```

Reduced overhead by about 0.2 uSec

| Delay   | Meas.  | Period  | Freq   |
| uS      | uS     | uS      | MHz    |
|---------|--------|---------|--------|
|   1     |   1.47 |   2.9   | 0.339  |
|  10     |  10.50 |  21.00  | 0.0476 |


