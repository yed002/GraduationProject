import time
import math
import mpu6050
from datetime import datetime

#roll => up : -, down : +
#pithc => left : -, right : +

mpu = mpu6050.mpu6050(0x68)

def read_sensor_data():
    accelerometer_data = mpu.get_accel_data()

    gyroscope_data = mpu.get_gyro_data()

    temperature = mpu.get_temp()

    return accelerometer_data, gyroscope_data, temperature

def calculate_angle(accel_data):
    x, y, z = accel_data['x'], accel_data['y'], accel_data['z']
    roll = math.atan2(y, math.sqrt(x2 + z2))
    pitch = math.atan2(-x, math.sqrt(y2 + z2))
    return math.degrees(roll), math.degrees(pitch)

time_count = 0
full_count = 0
name = str(datetime.now().date())+str(datetime.now().time())+".txt"
file = open(name,"w")

while time_count < 60:
    accel_data, gyro_data, temp = read_sensor_data()
    roll, pitch = calculate_angle(accel_data)
    pitch += 70
    print(f"Roll: {roll:.2f} degrees, Pitch: {pitch:.2f} degrees")
    file.write(f"Roll: {roll:.2f} degrees, Pitch: {pitch:.2f} degrees\n")
    time_count += 1

    if roll > 9 or pitch > 7 or roll < -7 or pitch < -7:
        full_count += 1

    time.sleep(1)

print("full_count : ", full_count)
file.close()