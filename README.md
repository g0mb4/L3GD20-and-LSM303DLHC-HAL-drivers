# L3GD20 and LSM303DLHC HAL drivers
 L3GD20 and LSM303DLHC HAL drivers for the STM32F3 Discovery Board
 
## Sources
The L3GD20 driver is based on [TILZ0R's Library 28](https://stm32f4-discovery.net/2014/08/library-28-l3gd20-3-axis-gyroscope).

The LSM303DLHC driver is based on the [Adafruit's LSM303DLHC implementation](https://github.com/adafruit/Adafruit_LSM303DLHC).

## Use other board/MCU than STM32F3 Discovery
Change 
```c
#include "stm32f3xx_hal.h"
```
in **stm32f3xx_l3gd20.h** and **stm32f3xx_lsm303dlhc.h**

Change
```c
/* default CS pin on STM32F3 Discovery board */
#define L3GD20_CS_PORT				GPIOE
#define L3GD20_CS_PIN				GPIO_PIN_3
```
in **stm32f3xx_l3gd20.h**

## Example
```c
...
#include "stm32f3xx_l3gd20.h"
#include "stm32f3xx_lsm303dlhc.h"
...
I2C_HandleTypeDef hi2c1;
SPI_HandleTypeDef hspi1;
l3gd20_data_t l3gd20_data = { 0 };
lsm303dlhc_data_raw_t lsm303dlhc_data_acc = { 0 };
lsm303dlhc_data_raw_t lsm303dlhc_data_mag = { 0 };
lsm303dlhc_data_t lsm303dlhc_data_acc_conv = { 0 };
lsm303dlhc_data_t lsm303dlhc_data_mag_conv = { 0 };
...
int main(void) {
	...
	lsm303dlhc_acc_init_t lsm303dlhc_acc_init = { 0 };
	lsm303dlhc_mag_init_t lsm303dlhc_mag_init = { 0 };
	
	lsm303dlhc_acc_init.ctrl_reg1_a = LSM303DLHC_ACR1A_XEN | LSM303DLHC_ACR1A_YEN | LSM303DLHC_ACR1A_ZEN | LSM303DLHC_ACR1A_ODR30_100_HZ;
	lsm303dlhc_acc_init.ctrl_reg4_a = LSM303DLHC_ACR4A_FS10_1MG;
	
	lsm303dlhc_mag_init.op = LSM303DLHC_MAGOP_CONT;
	lsm303dlhc_mag_init.rate = LSM303DLHC_MAGRATE_15;
	lsm303dlhc_mag_init.gain = LSM303DLHC_MAGGAIN_1_3;
	lsm303dlhc_mag_init.auto_range = false;
	...
	/* init SPI/I2C */
	...
	if (l3gd20_init(&hspi1, L3GD20_SCALE_2000) != L3GD20_OK) {
		/* handle error */
	}

	if (lsm303dlhc_init_acc(&hi2c1, &lsm303dlhc_acc_init) != LSM303DLHC_OK) {
		/* handle error */
	}

	if (lsm303dlhc_init_mag(&hi2c1, &lsm303dlhc_mag_init) != LSM303DLHC_OK) {
		/* handle error */
	}
	...
	while (1) {
		if (l3gd20_read(&l3gd20_data) == L3GD20_OK) {
			/* data in l3gd20_data */
		} else {
			/* handle error */
		}
		
		if (lsm303dlhc_read_acc_raw(&lsm303dlhc_data_acc) == LSM303DLHC_OK) {
			/* raw data in lsm303dlhc_data_acc */
			/* if conversion is needed: */
			lsm303dlhc_convert_acc(&lsm303dlhc_data_acc_conv, &lsm303dlhc_data_acc);
		} else {
			/* handle error */
		}
		
		if (lsm303dlhc_read_mag_raw(&lsm303dlhc_data_mag) == LSM303DLHC_OK) {
			/* raw data in lsm303dlhc_data_mag */
			/* if conversion is needed: */
			lsm303dlhc_convert_mag(&lsm303dlhc_data_mag_conv, &lsm303dlhc_data_mag);
		} else {
			/* handle error */
		}
		...
	}
	...
}
```

