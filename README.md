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
[I2C config](https://github.com/waikin-ng/MKEL1123_Project_Milestone_5/blob/main/i2c.PNG?raw=true)  
[USART config](https://github.com/waikin-ng/MKEL1123_Project_Milestone_5/blob/main/usart.PNG?raw=true)  
4. Copy all used libraries into the project folder.  
5. Select on Generate Code after all the configurations have been done.  

Steps in STM32CubeIDE:  
1. Copy all library header and source file into project directories (Inc, Src).  
2. Edit main.c by adding MPU6050 header file  
```
#include "mpu6050.h"
```
3. To initialize MPU6050, these code needed to be add into main.c (Use HAL_Delay to set the delay)  
4. Edit main.c to decide the output configuration (We are using serial monitor here to show both raw value and filtered angle)
```
     /* USER CODE END WHILE */
	  MPU6050_Read_All(&hi2c1, &MPU6050);
	  double angle_x = MPU6050.KalmanAngleX;
	  double angle_y = MPU6050.KalmanAngleY;

	  //double angle_z = MPU6050.Az;
	  double accel_x_raw = MPU6050.Ax;
	  double accel_y_raw = MPU6050.Ay;
	  double accel_z_raw = MPU6050.Az;

	  double gyro_x_raw = MPU6050.Gx;
	  double gyro_y_raw = MPU6050.Gy;
	  double gyro_z_raw = MPU6050.Gz;

	  HAL_UART_Transmit(&huart2, (uint8_t *)datax, sprintf(datax,
	  		                "\nAccel values\n- Accel_X_raw:%3.4f\n- Accel_Y_raw:%3.4f\n- Accel_Z_raw:%3.4f\n\n\n",
						  accel_x_raw ,
	                      accel_y_raw ,
	                      accel_z_raw
	                ), 1000);

	  HAL_UART_Transmit(&huart2, (uint8_t *)datax, sprintf(datax,
	  		                "\nGyro values\n- Gyro_X_raw:%3.4f\n- Gyro_Y_raw:%3.4f\n- Gyro_Z_raw:%3.4f\n\n\n",
						  gyro_x_raw ,
	                      gyro_y_raw ,
	                      gyro_z_raw
	                ), 1000);

	  HAL_UART_Transmit(&huart2, (uint8_t *)datax, sprintf(datax,
	  		                "\nAngle values\n- X:%3.4f\n- Y:%3.4f\n- Temperature\n- %3.4f\n\n\n",
						  angle_x ,
	                      angle_y ,
	                      MPU6050.Temperature
	                ), 1000);
	  HAL_Delay(3000);
    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}
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
    return Kalman->angle
```
8. Noted 0x68 will be returned by the sensor if everything goes well (this value is get according to data sheet for MPU6050)
```
// check device ID WHO_AM_I

    HAL_I2C_Mem_Read(I2Cx, MPU6050_ADDR, WHO_AM_I_REG, 1, &check, 1, i2c_timeout);

    if (check == 104)  
```




