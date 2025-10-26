# 2D-Motion-Graph
A Python program which generates a graph of 2 dimensional motion using user inputs of initial velocity, angle, and initial height. It calculates flight time, horizontal range, and the maximum height that the projectile reaches, and then it creates a graph showing the trajectory of the projectile.

Features:
  Calculates:
    Time the projectile is in the air (using the quadratic formula)
    Displacement
    Maximum height
  Animates projectile motion
  Uses the Kinematic equations taught in AP Physics 1 or any standard Physics Class in high school (assumes that accleration_y is -9.8 m/s^2
  Build with NumPy and MatplotLib

Physics concepts that were used:
  This project uses the kinematic equations to find values and visualize the trajectory of a launched particle accurately in the form of a 2D graph
  It finds the different x and y components of initial velocity via:
    v_initial_x = v_initial * cos(angle)
    v_initial_y = v_initial * sin(angle)
  It solves for time until the projectile reaches the ground via:
    y = y_initial + v_initial_y * time + 0.5 * acceleration_y * time ** 2
    Rearranging gives: 
      time = (-v_intiial_y - sqrt((v_initial_y ^ 2) - 4(initial_height)(0.5 * acceleration_y)) / acceleration_y
    Based on this, the displacement can be found via:
      delta_x = v_initial_x * time
  It solves for maximum height via:
    v_final_y ** 2 = v_initial_y ** 2 + 2 * acceleration_y * (y - y_initial)
    Rearranging to find y gives:
    (v_initial_y ** 2) / (2 * acceleration_y) + initial_height = maximum height
      v_final_y equals 0 because at the peak, it has no velocity in the y direction
      initial_height = y_initial

      
<<CODE>>

print("Kinematics grapher")

import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import PillowWriter

pi = 3.14159265359
acc_y = -9.8

# User inputs
angle = float(input("What is the angle you are launching the ball at (degrees)? "))
v_naught = float(input('What speed are you throwing the ball (m/s)? '))
height = float(input('How high above the ground are you throwing the ball (m)? '))

angle_rad = angle * pi / 180
v_naught_y = v_naught * np.sin(angle_rad)
v_naught_x = v_naught * np.cos(angle_rad)

# Time until it hits the ground
def time_finder(v_naught_y, height, acc_y):
  discriminant = v_naught_y**2 - 2 * acc_y * height
  if discriminant < 0:
    raise ValueError("No real solution to time")
  else:
    return (-v_naught_y - np.sqrt(v_naught_y**2 - 2 * acc_y * height)) / acc_y

time_max = time_finder(v_naught_y, height, acc_y)
print(f"Time elapsed: {time_max:.2f} seconds.")

# Horizontal displacement
def displacement_x_finder(v_naught_x, time_max):
    return v_naught_x * time_max

delta_x = displacement_x_finder(v_naught_x, time_max)
print(f"Your total horizontal distance: {delta_x} meters.")

# Maximum height
max_height = (v_naught_y**2) / (2 * -acc_y) + height
print(f"Maximum height reached: {max_height} meters above the ground.")

# Animation setup
fig = plt.figure()
l, = plt.plot([], [], 'k-')

plt.xlim(0, delta_x * 1.1)
plt.ylim(0, max_height * 1.2)

metadata = dict(title='2D Motion', artist='2D Motion Drawer')
writer = PillowWriter(fps=45, metadata=metadata)

def x_position(time, v_naught_x):
    return v_naught_x * time

def y_position(time, v_naught_y, acc_y):
    return height + v_naught_y * time + 0.5 * acc_y * time**2

xList = []
yList = []
with writer.saving(fig, '2D_Motion.gif', 100):
    for time in np.linspace(0, time_max, 250):
        xList.append(x_position(time, v_naught_x))
        yList.append(y_position(time, v_naught_y, acc_y))
        l.set_data(xList, yList)
        fig.canvas.draw()
        writer.grab_frame()
plt.show()
