#esphome #rccar #rccars #rc #homeassistant #L298N #ttmotors #esp8266 #esp32cam #fpv #fpvcar #fpvwifi #wificar #rcwifi #car #cars #wirelessrccar #radiocontrol #esphomerobot #robotcar #robotcar #esphomecar #esphomeproject #esphomeservo #esphomepantilt
I am not an electronics, programming or espHome expert. I have been searching and getting inspiration from videos. What I am is a big fan of Home Assistant with a multitude of home automation devices, even addicted :).

I do a RC FPV Car with espHome in a nodemcuv2 8266 board.
 Pros:
  - go around the house 
  - gossip and annoy my dog
 Cons:
 - Lot of lag camera and buttons
 - Very Fast
 - Many car crashes

(I've tried it later with an esp32cam but I can't get it to work, I'll put it aside later in the same battery).

 EVERYTHING IS QUITE PROVISIONAL, NO IMAGE DIAGRAM and has not been assembled just for testing, VERSION 0.1
  Now it's difficult to control away from home, the camera and the button presses have a lot of lag and instead the car goes very fast. Maybe is my internet connection. 

Questions: (If you can help let me in the comments below)
Can someone tell me how to control the speed?
How to know the battery status?

INSPIRATION:
https://youtu.be/tyY7AN132Xs
https://youtu.be/aJeD7R48ZyI

MATERIALS:
 - Esp8266
 - 12V Battery that it works when it's charging
 -  12.6V Charger
https://a.aliexpress.com/_vECinm
- Arduino Car Kit with 4 TT motors and wheels
https://a.aliexpress.com/_ugpMKk
- 2 servo motors  SG90 360
Or
- 2 servo motors MG996 360
https://a.aliexpress.com/_vYO1g4
- Servo PT Pan/Tilt and 2 servo motors
https://a.aliexpress.com/_vaiPQg
Or
- this 
-  MG996 Pan/Tilt and 2 servo motors MG996 
https://a.aliexpress.com/_v7d13E
- Dupont Cable
 https://a.aliexpress.com/_vOOCXW
 - Portable Battery for the usb camera
 - rtsp USB Camera (Xiamo Xiaofang in my case)

SCRIPTS.YAML:

rc_car_forward:
  alias: RC Car forward
  sequence:
  - service: switch.turn_off
    data:
      entity_id: switch.input1
  - service: switch.turn_off
    data:
      entity_id: switch.input3
  - delay:
      milliseconds: 500
  - service: switch.turn_on
    data:
      entity_id: switch.input2
  - service: switch.turn_on
    data:
      entity_id: switch.input4
rc_car_backward:
  alias: RC Car backward
  sequence:
  - service: switch.turn_off
    data:
      entity_id: switch.input2
  - service: switch.turn_off
    data:
      entity_id: switch.input4
  - delay:
      milliseconds: 500
  - service: switch.turn_on
    data:
      entity_id: switch.input1
  - service: switch.turn_on
    data:
      entity_id: switch.input3
rc_car_right_rotation:
  alias: RC Car right rotation
  sequence:
  - service: switch.turn_off
    data:
      entity_id: switch.input1
  - service: switch.turn_off
    data:
      entity_id: switch.input4
  - delay:
      milliseconds: 500
  - service: switch.turn_on
    data:
      entity_id: switch.input3
  - service: switch.turn_on
    data:
      entity_id: switch.input2
  mode: single
rc_car_left_rotation:
  alias: RC Car left rotation
  sequence:
  - service: switch.turn_off
    data:
      entity_id: switch.input3
  - service: switch.turn_off
    data:
      entity_id: switch.input2
  - delay:
      milliseconds: 500
  - service: switch.turn_on
    data: 
      entity_id: switch.input1
  - service: switch.turn_on
    data:
      entity_id: switch.input4
  mode: single
rc_car_stop:
  alias: RC Car stop
  sequence:
  - service: switch.turn_off
    data:
      entity_id: switch.input1
  - service: switch.turn_off
    data:
      entity_id: switch.input2
  - service: switch.turn_off
    data:
      entity_id: switch.input3
  - service: switch.turn_off
    data:
      entity_id: switch.input4

RC-CAR.YAML:

esphome:
  name: rc-car

esp8266:
  board: nodemcuv2

logger:

ota:
  password: "xxx"

wifi:
  ssid: YOUR_WIFI_NAME
  password: YOUR_PASS

  ap:
    ssid: "Rc-Car Fallback Hotspot"
    password: "YOUR_PASS"
    
captive_portal:

switch:
  - platform: gpio
    pin: GPIO15
    name: "input1"
    id: input1
  - platform: gpio
    pin: GPIO14
    name: "input2"
    id: input2
  - platform: gpio
    pin: GPIO12
    name: "input3"
    id: input3
  - platform: gpio
    pin: GPIO13
    name: "input4"
    id: input4

servo:
  - id: servo_x
    output: pwm_output
  - id: servo_y
    output: pwm_output2
    
api:
  services:
    - service: control_servo_x
      variables:
        level: float
      then:
        - servo.write:
            id: servo_x
            level: !lambda 'return level / 100.0;'
    - service: control_servo_y
      variables:
        level: float
      then:
        - servo.write:
            id: servo_y
            level: !lambda 'return level / 100.0;'

light:
  - platform: binary
    name: "Leds camera"
    output: leds_cam

output:
  - id: leds_cam
    platform: gpio
    pin: GPIO2
  - platform: esp8266_pwm
    id: pwm_output
    pin: GPIO5
    frequency: 50 Hz
  - platform: esp8266_pwm
    id: pwm_output2
    pin: GPIO4
    frequency: 50 Hz
