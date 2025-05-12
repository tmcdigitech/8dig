---
title: Move and turn
weight: 40
---
In Visual Studio Code select `main.py` from the **File Explorer** on the left. At the top of the explorer click the new file icon (a document with a +) and name the file `obs1.py`.

Enter this code:

```python
robot.setup(DRIVE_BASE)

robot.straight(100)
robot.straight(-100)
```

Save your work (<kbd>Ctrl-S</kbd> or <mark>File > Save</mark> from the menu bar) and run the code with <kbd>F5</kbd>. VSCode will automatically copy all the files in the project onto the robot and run the main.py file.

Choose `obs1.py` from the menu. The robot will drive forwards a little way, and then back again to where it started.

Let's look at the instructions you gave the robot. The first line tells the robot which components you will be using. The drive base is a combination of two motors which are used together to move around (otherwise driving around is *really* annoying). Every script will need a `robot.setup()` line at the start to make sure the necessary components are set up.

When you say `robot.straight(100)` you are instructing the robot to go forward 100mm. So if you wanted the robot to travel 250mm, you would write:

```python
robot.straight(250)
```

If you know the distance in another unit, like cm, then you have to convert it to mm first. Since 1cm contains 10mm, we multiply by 10 to convert from cm to mm. So to travel 12cm we would write:

```python
robot.straight(120)
```

#### Check your understanding

If you the robot to move forward 37cm, what instruction would you give it?

If you needed to move backward 200mm, what instruction would you give it?

## Turning

You can instruct the robot to turn on the spot.

```python
robot.turn(180)
robot.turn(-60)
```

The instruction takes a number in degrees, so `robot.turn(180)` will turn the robot *right* through 180°, so it faces the direction it just came from. The instruction `robot.turn(-60)` will turn *left* through 60°. Remember that there are 360° in a full circle, so turning the robot through 360° should end up with it facing the same way it started.

#### Check your understanding

If you wanted to turn the robot 90° to the right, what instruction would you give it?

If you wanted it to turn to the left instead, what instruction would you give it?
