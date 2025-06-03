---
title: main.py
weight: 20
params:
    part: setup
---

Delete all the code in `main.py`, replacing it with the code below:

```python
#!/bin/bash
##############################################
# Runs script first through bash to remove
# other project folders, then execs to python
# to run the rest of the file. Bash will step over
# the ''':' line (why?), and stop executing after
# the exec line, which will replace the bash
# process with the python one. Python will ignore
# the bash script as it looks like a docstring.
##############################################
''':'
shopt -s extglob
wd=${PWD##*/}
cd ..
rm -rf !($wd)
cd $wd
exec /usr/bin/pybricks-micropython "$0" "$@"
'''

""" 
----------------------------------------------------------------- 
Thomas More College Year 8 Digital Technologies
Autonomous Vehicles Unit

Code adapted from
Example LEGO® MINDSTORMS® EV3 Robot Educator Driving Base Program 
----------------------------------------------------------------- 
""" 

##############################################
# Import code
##############################################

from pybricks.hubs import EV3Brick
from pybricks.ev3devices import (
    Motor,
    TouchSensor,
    ColorSensor,
    # InfraredSensor,
    UltrasonicSensor,
    # GyroSensor
)
from pybricks.parameters import Port, Stop, Direction, Button, Color
from pybricks.tools import wait, StopWatch, DataLog
from pybricks.robotics import DriveBase
from pybricks.media.ev3dev import Font, SoundFile, ImageFile
from uerrno import ENODEV
import os
import os.path
import shutil

##############################################
# Robot class
##############################################
portlist_font = Font(size=12)
menu_font = Font(size=14)
delay_font = Font(size=72)

LEFT_COLOR = 1
TOUCH = 2
USONIC = 3
RIGHT_COLOR = 4

DRIVE_BASE = 10
LEFT_MOTOR = 11
RIGHT_MOTOR = 12

class Robot:
    ev3 = EV3Brick()
    screen = ev3.screen
    speaker = ev3.speaker
    light = ev3.light
    buttons = ev3.buttons
    setup_called = False

    def __init__(self):
        # setup flags
        self.left_color_setup = False
        self.touch_setup = False
        self.usonic_setup = False
        self.right_color_setup = False
        self.left_motor_setup = False
        self.left_motor_setup = False
        self.drive_base_setup = False

        # Inputs
        try:
            self.left_color = ColorSensor(Port.S1)
        except OSError as e:
            self.left_color = None

        try:
            self.touch = TouchSensor(Port.S2)
        except OSError as e:
            self.touch = None

        try:
            self.usonic = UltrasonicSensor(Port.S3)
        except OSError as e:
            self.usonic = None

        try:
            self.right_color = ColorSensor(Port.S4)
        except OSError as e:
            self.right_color = None
        
        # Outputs
        try:
            self.left_motor = Motor(Port.B)
        except OSError as e:
            self.left_motor = None

        try:
            self.right_motor = Motor(Port.C)
        except OSError as e:
            self.right_motor = None

        if self.left_motor != None and self.right_motor != None:
            try:
                self.drive_base = DriveBase(self.left_motor, self.right_motor, wheel_diameter=55.5, axle_track=125)
            except OSError as e:
                self.drive_base = None
        else:
            self.drive_base = None

    def setup(self, *argv):
        errors = False
        for arg in argv:
            if arg==LEFT_COLOR:
                if self.left_color==None:
                    errors = True
                else:
                    self.left_color_setup = True
            if arg==USONIC:
                if self.usonic==None:
                    errors = True
                else:
                    self.usonic_setup = True
            if arg==TOUCH:
                if self.touch==None:
                    errors = True
                else:
                    self.touch_setup = True
            if arg==RIGHT_COLOR:
                if self.right_color==None:
                    errors = True
                else:
                    self.right_color_setup = True
            if arg==LEFT_MOTOR:
                if self.left_motor==None:
                    errors = True
                else:
                    self.left_motor_setup = True
            if arg==RIGHT_MOTOR:
                if self.right_motor==None:
                    errors = True
                else:
                    self.left_motor_setup = True
            if arg==DRIVE_BASE:
                if self.drive_base==None:
                    errors = True
                else:
                    self.drive_base_setup = True
            if errors:
                self.show_setup()
            self.setup_called = True
    
    def setup_not_called(self):
        self.screen.clear()
        self.screen.set_font(menu_font)
        self.screen.print("Error:")
        self.screen.print("Robot not setup() yet.")
        while True:
            pass

    def show_setup(self):
        self.screen.clear()
        self.screen.set_font(portlist_font)
        self.screen.print("Inputs:")
        if self.left_color == None:
            self.screen.print("[    ]  1: Left Color")
        else:
            self.screen.print("[ + ]  1: Left Color")

        if self.touch == None:
            self.screen.print("[    ]  2: Touch")
        else:
            self.screen.print("[ + ]  2: Touch")

        if self.usonic == None:
            self.screen.print("[    ]  3: U'sonic")
        else:
            self.screen.print("[ + ]  3: U'sonic")

        if self.right_color == None:
            self.screen.print("[    ]  4: Right Color")
        else:
            self.screen.print("[ + ]  4: Right Color")
        
        self.screen.print("Outputs:")

        if self.left_motor == None:
            self.screen.print("[   ]  B: Left Motor")
        else:
            self.screen.print("[ + ]  B: Left Motor")

        if self.right_motor == None:
            self.screen.print("[   ]  C: Right Motor")
        else:
            self.screen.print("[ + ]  C: Right Motor")

        while True:
            pass

    
    def watch(self):
        self.screen.set_font(portlist_font)
        while True:
            self.screen.clear()
            self.screen.print("Inputs:")
            if self.left_color != None:
                self.screen.print("[1] Color: "+str(self.left_color.reflection()))
            else:
                self.screen.print("[1]")

            if self.touch != None:
                self.screen.print("[2] Touch: "+str(self.touch.pressed()))
            else:
                self.screen.print("[2]")

            if self.usonic != None:
                self.screen.print("[3] U'sonic: "+str(self.usonic.distance()))
            else:
                self.screen.print("[3]")

            if self.right_color != None:
                self.screen.print("[4] Color: "+str(self.right_color.reflection()))
            else:
                self.screen.print("[4]")
            
            self.screen.print("Outputs:")

            if self.left_motor != None:
                self.screen.print("[B] Motor: "+str(self.left_motor.angle()))
            else:
                self.screen.print("[B]")

            if self.right_motor != None:
                self.screen.print("[C] Motor: "+str(self.right_motor.angle()))
            else:
                self.screen.print("[C]")

            wait(200)
    
    def ensure(self, sensor_setup):
        if not self.setup_called:
            self.setup_not_called()
        if not sensor_setup:
            self.sensor_not_setup(sensor_setup)
    
    def straight(self, dist_in_mm):
        self.ensure(self.drive_base_setup)
        self.drive_base.straight(dist_in_mm)

    def turn(self, angle_in_deg):
        self.ensure(self.drive_base)
        self.drive_base.turn(angle_in_deg)

    def drive(self, speed_in_mm_per_s, turn_rate_in_deg_per_s):
        self.ensure(self.drive_base)
        self.drive_base.drive(speed_in_mm_per_s, turn_rate_in_deg_per_s)

    def stop(self):
        self.ensure(self.drive_base)
        self.drive_base.stop()

    def reset(self):
        self.ensure(self.drive_base)
        self.drive_base.reset()
    
    def read(self, sensor):
        if sensor == LEFT_COLOR:
            self.ensure(self.left_color)
            return self.left_color.reflection()
        if sensor == TOUCH:
            self.ensure(self.touch)
            return self.touch.pressed()
        if sensor == USONIC:
            self.ensure(self.usonic)
            return self.usonic.distance()
        if sensor == RIGHT_COLOR:
            self.ensure(self.right_color)
            return self.right_color.reflection()
        return False



##############################################
# Setup
##############################################

# Initialize the TMC bot
robot = Robot()

#[name, selectable, indent, isFile]
menu = [
    ['Ports', False, 0, False],
    ['Check', True, 4, False],
    ['Watch', True, 4, False],
    ['----------', False, 0, False],
    ['Obstacle Course', False, 0, False],
    ['obs1.py', True, 4, True],
    ['obs2.py', True, 4, True],
    ['obs3.py', True, 4, True],
    ['Follow the Line', False, 0, False],
    ['ftl1.py', True, 4, True],
    ['ftl2.py', True, 4, True],
    ['ftl3.py', True, 4, True],
    ['ftl4.py', True, 4, True],
    ['Robotics explorer', False, 0, False],
    ['exp.py', True, 4, True],
]
robot.screen.set_font(menu_font)
menuIx = 0
delay = 0
files = sorted(os.listdir('.'))
while True:
    robot.screen.draw_box(0,0,robot.screen.width,robot.screen.height,0,True,Color.WHITE)
    for i,m in enumerate(menu):
        if i < menuIx:
            continue
        if i >= menuIx+8:
            continue
        if i==menuIx:
            robot.screen.draw_box(0,16*(i-menuIx),robot.screen.width,16*(i-menuIx+1),0,True)
            robot.screen.draw_text(2,16*(i-menuIx),' '*m[2]+m[0],Color.WHITE,None)
            if m[1] and not m[3] or m[3] and m[0] in files:
                robot.light.on(Color.GREEN)
            else:
                robot.light.on(Color.RED)
        else:
            robot.screen.draw_text(2,16*(i-menuIx),' '*m[2]+m[0],Color.BLACK,Color.WHITE)

    while len(robot.buttons.pressed()) == 0:
        pass
    if Button.DOWN in robot.buttons.pressed():
        while Button.DOWN in robot.buttons.pressed():
            pass
        menuIx += 1
        if menuIx >= len(menu):
            menuIx = 0
    elif Button.UP in robot.buttons.pressed():
        while Button.UP in robot.buttons.pressed():
            pass
        menuIx -= 1
        if menuIx < 0:
            menuIx = len(menu)-1
    elif Button.CENTER in robot.buttons.pressed():
        delay = 0
        break
    elif Button.LEFT in robot.buttons.pressed():
        delay = 3
        break
    elif Button.RIGHT in robot.buttons.pressed():
        delay = 5
        break

if menu[menuIx][0] == 'Check':
    robot.show_setup()

if menu[menuIx][0] == 'Watch':
    robot.watch()

if menu[menuIx][3]:
    while delay > 0:
        robot.screen.clear()
        robot.screen.set_font(menu_font)
        robot.screen.print("Starting in")
        robot.screen.set_font(delay_font)
        robot.screen.print(str(delay))
        wait(1000)
        delay -= 1
    robot.screen.clear()
    robot.screen.set_font(delay_font)
    robot.screen.print('Go')
    try:
        exec(open(menu[menuIx][0],'r').read())
    except OSError as e:
        raise e
```
