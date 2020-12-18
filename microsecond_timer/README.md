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

Include code notes here

## Results

With SYSCLK running at 100MHz the code toggled a GPIO pin at maximum rate with various delay intervals

| Delay   | Meas.  | Period  | Freq   |
| uS      | uS     | uS      | MHz    |
|---------|--------|---------|--------|
| none    |   0.42 |   0.84  | 1.20   |
|   0     |   0.73 |   1.4   | 0.68   |
|   1     |
|   5     |
|  10     |


