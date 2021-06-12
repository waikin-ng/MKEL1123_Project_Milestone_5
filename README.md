# MKEL1123_Project_Milestone_5
MPU 6050 library for STM32
This post is to explain how to interface MPU6050 (GY-521) with STM32 microcontroller

Download this software:  
STM32CubeMX https://www.st.com/en/development-tools/stm32cubemx.html?sc=stm32cubemx  
STM32CubeIDE https://www.st.com/en/development-tools/stm32cubeide.html   
STLink driver https://www.st.com/en/development-tools/st-link-v2.html  

Hardware connection:  
SCL: Serial Clock pin. Connect this pin to microcontrollers SCL pin  
SDA: Serial Data pin. Connect this pin to microcontrollers SDA pin  
GND: Ground pin. Connect this pin to ground connection  
VCC: Power supply pin. Connect this pin to +3.3V pin  

Steps in STM32CubeMX:  
1. Create a new project by using STM32CubeMX.  
2. Choose board by selecting Board Selector. (We are using NUCLEO-F446RE here)  
3. Verify pinout and initialize the I2C bus.  
4. Copy all used libraries into the project folder.  
5. Select on Generate Code after all the configurations have been done.  

Steps in STM32CubeIDE:  
1. Copy all library header and source file into project directories (Inc, Src).  
2. Edit main.c by adding MPU6050 header file  
```
#include "mpu6050.h"
```
3. To initialize MPU6050, these code needed to be add into main.c (Use HAL_Delay to set the delay)  
4. Edit main.c to decide the output configuration (We are using serial monitor here)
```
 /* USER CODE END WHILE */
	  MPU6050_Read_All(&hi2c1, &MPU6050);
	  double angle_x = MPU6050.Ax;
	  double angle_y = MPU6050.Ay;
	  double angle_z = MPU6050.Az;

	  HAL_UART_Transmit(&huart2, (uint8_t *)datax, sprintf(datax,
	  		                "Angle values\n- X:%3.4f\n- Y:%3.4f\n- Z:%3.4f\nTemperature\n- %3.4f\n\n\n",
	                      angle_x ,
	                      angle_y ,
	                      angle_z,
	                      MPU6050.Temperature
	                ), 1000);
	  HAL_Delay(5000);
    /* USER CODE BEGIN 3 */
 /* USER CODE END WHILE */
```
5. Functions built so data from MPU6050 can be obtained (Accelerometer, gyrometer, temperature sensor)
 ```
 void MPU6050_Read_Accel(I2C_HandleTypeDef *I2Cx, MPU6050_t *DataStruct) 
```
```
void MPU6050_Read_Gyro(I2C_HandleTypeDef *I2Cx, MPU6050_t *DataStruct) 
```
```
void MPU6050_Read_Temp(I2C_HandleTypeDef *I2Cx, MPU6050_t *DataStruct)
```
7. Data read from the MPU6050 and processed by Kalman filter will be available through the MPU6050 structure.
```
```
8. Noted 0x68 will be returned by the sensor if everything goes well
```
// check device ID WHO_AM_I

    HAL_I2C_Mem_Read(I2Cx, MPU6050_ADDR, WHO_AM_I_REG, 1, &check, 1, i2c_timeout);

    if (check == 104)  
```




