
# SPIKE PD Line Follow 
![](https://i1.ytimg.com/vi/tpRW91oHa7o/maxresdefault.jpg)

## Hardware
 - Wheel 62.4 x 20 with Short Axle Hub, Black Tire 62.4 x 20 (86652 / 32019) x2 
 - Technic Large Motor 88013 x2 
 - SPIKE/RI Color Sensor x2
 - SPIKE Prime / RI Hub
## Map
![](https://camo.githubusercontent.com/c178029d1fa5caaeb9d19826990878c4c42d9ffee742bacc0c8940d6896ef6de/68747470733a2f2f73636f6e74656e742d747065312d312e78782e666263646e2e6e65742f762f74312e363433352d392f3130343830363739395f323835353135333037373932393533395f373038353233343936343132323038393334345f6e2e6a70673f5f6e635f6361743d313035266363623d312d35265f6e635f7369643d383235313934265f6e635f6f68633d355a425a5778497131394541582d76716f797a265f6e635f68743d73636f6e74656e742d747065312d312e7878266f683d30305f41542d336b78306556576b4841456d54505f2d497761714b777741756f434465723648514831396d6b7456384477266f653d3632324530384245)
Click here to download: [Robotics-Training-Map repo](https://github.com/ofdl-robotics-tw/Robotics-Training-Map)
## Program
Python, we recommend using **Robot Inventor** software to programming. (SPIKE Prime hub are able to use RI software too.)
We using **Raw reflect light value**  to normalized sensor.

### Reading Raw color sensor value
```
from hub import port

S1d = port.F.device
S2d = port.D.device
S1d.mode(4) #RREFL
S2d.mode(4) #RREFL

while True:
    print(str(S1d.get()[0]) + " , " +str(S2d.get()[0]))
```
### Main Program
```
from hub import port

MotorB = port.B.motor
MotorC = port.C.motor
S1d = port.F.device
S2d = port.D.device

#Mode 4 RREFL - Raw Reflect Light
S1d.mode(4) 
S2d.mode(4)

#Define Sensor 1,2 raw min and max value
S1_in_min = 265
S1_in_max = 1024
S2_in_min = 240
S2_in_max = 1024

#Function - return normalized reflect light value
def S1():
    return (S1d.get()[0] - S1_in_min) * 100 / (S1_in_max - S1_in_min);

def S2():
    return (S2d.get()[0] - S2_in_min) * 100/ (S2_in_max - S2_in_min);

#Constant define 
Kp = 0.3
Kd = 7.5
Base = 90
err = 0
err_old = 0
pwrB = 0
pwrC = 0
corr = 0

while True:
    err = S1() - S2()
    corr = err * Kp + Kd * (err - err_old)
    pwrB = int(-Base - corr) # motor B is invert
    pwrC = int(Base - corr)
    MotorB.pwm(pwrB)
    MotorC.pwm(pwrC)
    err_old = err
```
