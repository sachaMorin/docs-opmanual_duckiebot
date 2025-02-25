# Operation - Make it move {#rc-control status=ready}

<div class='requirements' markdown='1'>

Requires: A Duckiebot in `DB18` or later configurations.

Requires: Laptop configured according to [](#laptop-setup).

Requires: You have configured the Duckiebot as documented in [](#setup-duckiebot).

Results: You can make your Duckiebot move.

</div>

This section describes how to make your Duckiebot move.

<!--Requires: You have created a Github account and configured public keys,
both for the laptop and for the Duckiebot. The procedure is documented in [](+software_reference#github-access).-->

<!--

## Option 0 - With a joystick

Assuming that your Duckiebot is [properly initialized](#setup-duckiebot), if you have a gamepad then plug the usb dongle into the raspberry pi of your duckiebot and you should be able to use it right away

-->

## Keyboard control {#make-it-move_shell status=ready}

The easiest way to move a Duckiebot is by keyboard control. This video shows how to drive a Duckiebot using the keyboard, through the Duckietown Shell.

<div figure-id="fig:howto-virtual" figure-caption="Duckiebot keyboard control.">
<dtvideo src="vimeo:526584868"/>
</div>

### Duckietown Shell

To move your Duckiebot using your computer's keyboard open a terminal and run:

    $ dts duckiebot keyboard_control ![ROBOT_NAME]

which, after startup, will open an arrows interface window:

<figure>
    <figcaption>The keyboard control graphical user interface</figcaption>
    <img style='width:8em' src="keyboard_gui.png"/>
</figure>

Note: input Duckiebot ![hostname], do not include `.local` part.

The following keys control the Duckiebot:

<col2 figure-id="tab:virtual_keyboard" figure-caption="Keyboard joystick functions" class="labels-row1">
    <span>Keys</span>
    <span>Function</span>
    <span>ARROW_KEYS</span>
    <span>Steer your Duckiebot</span>
    <span>q</span>
    <span>Quit</span>
    <span>a</span>
    <span>Turn on Lane Following</span>
    <span>s</span>
    <span>Stop Lane Following</span>
    <span>i</span>
    <span>Toggle Anti-instagram</span>
</col2>

The <kbd>a</kbd>, <kbd>s</kbd>, <kbd>i</kbd> function require the [lane following demo](#demo-lane-following) to be running.

Warning: This does not currently work on Mac OSX.

### The no-window way with Duckietown shell (For Mac Users)

For some reason messages published on Mac inside the container don't make it all the way to the robot.

When the popup arrow window is not responsive, running the stack directly on the Duckiebot might help:

    laptop $ dts duckiebot keyboard_control ![hostname] --cli

## Option 2: Using the Dashboard {#setup-ros-websocket-image status=draft}

If you followed the instructions in [](#duckiebot-dashboard-setup), you
should have access to the Duckiebot dashboard.

You can open the browser and visit the page `http://![hostname].local/mission-control`.

This is the Mission Control page.
It is the page that lets you monitor and control your Duckiebot.
The top of the page should be similar to the following image,


<div figure-id="fig:dashboard_mission_control_auto" figure-caption="">
  <img src="dashboard_mission_control_auto.png" style='width: 35em'/>
</div>


The first thing to check to make sure that everything we have done so far
is correct, is the status of the **Bridge**, in the top-right corner of the page.
The label should show the status "**Bridge: Connected**" (as shown in the image above).
If the indicator reads "**Bridge: Closed**", it means that something went wrong
while launching the ROS websocket node above. In that case, start again from
the beginning of this section.

Note: Don't worry if one of the blocks is called "Camera" but you
don't see an image. We will get to that later.

This page will show you lateral and angular speed of your robot, and
a plot of left and right motor speed. Toggle the **Take over** switch
in the top-right corner of the page to gain control of your robot.
You will see that the background of the page will highlight and the
central plot will start moving.

You can now use the arrows on your keyboard to drive your Duckiebot.

**Did you know?**
The page contains 4 blocks by default.
Feel free to drag them around and rearrange them as you please.
You can also use the menu button of each block to resize them.

## Troubleshooting

Symptom: Duckiebot goes backwards, even though I command it to go forward.

Resolution: If you have a `DB17` or `DB18`, revert the polarities (plus and minus cables) of the cables that go to the motor driver (`HUT`) for both motors.

Symptom: The robot doesn't move

Resolution: Check that the `duckiebot-interface` container is running

Open [the Portainer interface](#sub:dashboard-portainer) and check the running containers. You should see one called `dt18_03_roscore_duckiebot-interface_1`.

You can also determine this by running:

    $ docker -H ![ROBOT_NAME].local ps

and look at the output to find the Duckiebot interface container and verify that it is running.

Resolution: One of the base images is out of date
Update your Duckiebot with the command

    laptop $ dts duckiebot update ![ROBOT_NAME]

Symptom: Everything seems fine, I can see the commands being sent to the Duckiebot (e.g., through the Dashboard > Mission Control), but the Duckiebot does not move. My Dashboard > Robot > Components page show a red alert for the `HUT`.

Note: if you have a `HUT` v3.15 you will stumble on this problem the first time you try to move your Duckiebot.

Resolution: Re-flash your `HUT` following the procedure described in [](#reflash-microcontroller). 

<!--


Symptom: I plugged in a gamepad, I found and run the unduckumented joystick demo but the joystick does not move the wheels.

Resolution: Check that the red indicator on the joystick stopped blinking.

<div figure-id="fig:joystick_connection_status" figure-class="flow-subfigures">
    <div figure-id="subfig:joystick_no_connection" figure-caption="Bad joystick status">
        <p style='width:14em'>
            <img src="joystick_no_connection.jpg" style='width:14em'/>
        </p>
    </div>

    <div figure-id="subfig:joystick_good_connection" figure-caption="Bad joystick status">
        <p style='width:14em'>
            <img src="joystick_good_connection.jpg" style='width:14em'/>
        </p>
    </div>
</div>    

Symptom: The joystick is connected (as shown in [](#subfig:joystick_good_connection)) but
the Duckiebot still does not move.

Resolution: Make sure that the controller is connected to the Duckiebot and that the OS receives the data from it. Run

    duckiebot $ jstest /dev/input/js0

If you receive the error

    jstest: No such file or directory

it means that the USB receiver is not connected to the Raspberry Pi or is broken.

If the command above shows something like the following

    Driver version is 2.1.0.
    Joystick (ShanWan PC/PS3/Android) has 8 axes (X, Y, Z, Rz, Gas, Brake, Hat0X, Hat0Y)
    and 15 buttons (BtnX, BtnY, BtnZ, BtnTL, BtnTR, BtnTL2, BtnTR2, BtnSelect, BtnStart, BtnMode, BtnThumbL, BtnThumbR, ?, ?, ?).
    Testing ... (interrupt to exit)
    Axes:  0:     0  1:     0  2:     0  3:     0  4:-32767  5:-32767  6:     0  7:     0 Buttons:  0:off  1:off  2:off  3:off  4:off  5:off  6:off  7:off  8:off  9:off 10:off 11:off 12:off 13:off 14:off

it means that the USB receiver is connected to the Raspberry Pi. Leave the terminal above open and use the joystick to command the Duckiebot. If you observe that the numbers shown in the terminal change according to the commands sent through the joystick than the problem is
in ROS. Make sure that the joystick demo is launched. Restart the Duckiebot if needed and try again.

If the numbers do not change while using the joystick then follow this guide at the next Resolution point.

Resolution: The controller might be connected to another Duckiebot nearby. Turn off the controller, go to a room with no other Duckiebots around and turn the controller back on. Retry.



-->
