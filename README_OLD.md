# What is Stroboscope ?
A stroboscope is a device that produces a rapid series of flashes of light that can be used to create a visual illusion of a stopped or slowed-down motion. Stroboscopes have various applications, including in manufacturing, research, and entertainment. In this article, we will discuss how to build a stroboscope using an Arduino board.

# stroboscope-using-arduino
The stroboscope is an intense, high speed light source used for visual analysis of objects in periodic motion and for high speed photography.
This stroboscope can measure up to 200 Hz frequency. It can be used in various industries where high-speed machinery is used. By adjusting the speed of the stroboscope the rotating part will be stopping with respect to our eyes. But It is actually not. So we can easily measure the speed of that rotating part or easily visualize the defect of that rotating part.

# Hardware Design:
To build a stroboscope using an Arduino board, we will need the following components:

- An AT-Mega328P-PU Chip with a vero board (such as the Arduino Uno)
- A LED Flash Light or a high-power LED
- A resistor (for current limiting the LED)
- A N-channel enhancement mosfet (For driving the LED light)
- Two potentiometer (for adjusting the flashing frequency and duty cycle)
- An LCD Display (For displaying)
- A Box (for compact design)
- wires (for connecting the components)

The high-power LED will be used to produce the flashes of light, and the potentiometer will be used to adjust the flashing frequency and another is used for controlling the duty cycle. And the resistor is used to limit the current flowing through the LED to protect it from burning out.

# Software Design:
Once we have all the necessary hardware components, we can proceed to the software design. We will use the Arduino IDE to write the program that will control the flashing of the LED. We will use the digitalWrite() function to turn the LED on and off and the delay() function to create the flashing effect. here I takeing the help of millis() function insted of delay() function.

https://github.com/shuvabratadey/stroboscope-using-arduino/blob/d7f7323356c847a2a979165b9f62832e6a62482f/Stroboscope/Stroboscope.ino

# Some Pictures of this Project
<img src="https://github.com/shuvabratadey/stroboscope-using-arduino/blob/main/Stroboscope%20Pictures/Starting%20Time.jpg" width="500"/>
<img src="https://github.com/shuvabratadey/stroboscope-using-arduino/blob/main/Stroboscope%20Pictures/Start%20Measurement.jpg" width="500"/>
<img src="https://github.com/shuvabratadey/stroboscope-using-arduino/blob/main/Stroboscope%20Pictures/Internal%20Circuit.jpg" width="500"/>
</br><img src="https://github.com/shuvabratadey/stroboscope-using-arduino/blob/main/Stroboscope%20Pictures/Front%20Side.jpg" width="500"/>
</br><img src="https://github.com/shuvabratadey/stroboscope-using-arduino/blob/main/Stroboscope%20Pictures/Right%20Side.jpg" width="500"/>

# Conclusion:
In conclusion, building a stroboscope using an Arduino board is a relatively simple and fun project. By using a high-power LED and a potentiometer, we can create a flashing effect with a controllable frequency. The code examples provided in this article are just the beginning, and you can modify them to suit your needs or add additional features.
