# F1-Optimum-Lap-Simulator
Calculates the optimum lap time for any track - uses the Yas Marina Circuit as an example.

import numpy as np
import matplotlib.pyplot as plt

"""
Abu Dhabi F1 Lap Time Estimator

This script simulates the Yas Marina Circuit using basic physics and estimates
a theoretical perfect lap time based on car and tyre parameters.
You can modify the track layout to simulate other tracks.

I just estimated the shape of this circuit and then scaled it to the correct length.
Please do not come for me, I just started learning Python! 

"""
'''
# Track Set Up
starting_direction = 180
heading = -1 * starting_direction + 90
track_length = 0
real_length = 5281
scaling_factor = real_length / 3384

# Time Estimation Set Up
tyre_type = input("What type of tyres are being used for this lap?")
if tyre_type == "soft":
    coeff_friction = 1.75
elif tyre_type == "medium":
    coeff_friction = 1.5
elif tyre_type == "hard":
    coeff_friction = 1.3

horse_power = float(input("What is the horsepower of the vehicle?"))
weight = float(input("What is the weight of the vehicle?"))
drag_coeff = float(input("What is the drag coefficient of the vehicle?"))
print()

air_density = 1.225
g = 9.81
power_watts = 745.7 * horse_power
frontal_area = 1.5
total_time = 0

# Track (Estimated Then Scaled)
track = [
    {"type": "straight", "length": 230},
    {"type": "corner", "radius": 30, "angle": 95, "turn": "left"},
    {"type": "straight", "length": 100},
    {"type": "corner", "radius": 30, "angle": 55, "turn": "left"},
    {"type": "straight", "length": 50},
    {"type": "corner", "radius": 100, "angle": 100, "turn": "right"},
    {"type": "corner", "radius": 80, "angle": 40, "turn": "left"},
    {"type": "straight", "length": 240},
    {"type": "corner", "radius": 20, "angle": 155, "turn": "left"},
    {"type": "straight", "length": 750},
    {"type": "corner", "radius": 10, "angle": 115, "turn": "left"},
    {"type": "straight", "length": 30},
    {"type": "corner", "radius": 10, "angle": 90, "turn": "right"},
    {"type": "corner", "radius": 250, "angle": 25, "turn": "left"},
    {"type": "corner", "radius": 130, "angle": 10, "turn": "left"},
    {"type": "corner", "radius": 4000, "angle": 3, "turn": "left"},
    {"type": "corner", "radius": 450, "angle": 25, "turn": "left"},
    {"type": "corner", "radius": 60, "angle": 196, "turn": "left"},
    {"type": "straight", "length": 110},
    {"type": "corner", "radius": 50, "angle": 30, "turn": "right"},
    {"type": "straight", "length": 60},
    {"type": "corner", "radius": 50, "angle": 35, "turn": "right"},
    {"type": "straight", "length": 50},
    {"type": "corner", "radius": 20, "angle": 104, "turn": "right"},
    {"type": "straight", "length": 70},
    {"type": "corner", "radius": 30, "angle": 80, "turn": "left"},
    {"type": "straight", "length": 25},
    {"type": "corner", "radius": 30, "angle": 100, "turn": "left"},
    {"type": "straight", "length": 140},
    {"type": "corner", "radius": 30, "angle": 70, "turn": "right"},
    {"type": "straight", "length": 66.5},
    {"type": "corner", "radius": 30, "angle": 110, "turn": "right"},
    {"type": "straight", "length": 40},
    
]

x, y = 0,0
xs, ys = [x], [y]

for segment in track:
    if segment["type"] == "straight":
        segment["length"] *= scaling_factor
        length = segment["length"]
        heading_rad = np.radians(heading)
        dx = length * np.cos(heading_rad)
        dy = length * np.sin(heading_rad)
        x = x + dx
        y = y + dy
        xs.append(x)
        ys.append(y)

        track_length = track_length + length

        v_straight = (2 * power_watts / (air_density * drag_coeff * frontal_area))**(1/3)
        
        time = length / v_straight
        total_time += time

    elif segment["type"] == "corner":
        segment["radius"] *= scaling_factor
        radius = segment["radius"]
        angle = segment["angle"]
        turn = segment["turn"]
        heading_rad = np.radians(heading)

        if turn == "left":
            sign = 1
        
        elif turn == "right":
            sign = -1

        cx = x - sign * radius * np.sin(heading_rad)
        cy = y + sign * radius * np.cos(heading_rad)
        start_angle = heading - 90
        end_angle = start_angle + angle * sign
        theta = np.radians(np.linspace(start_angle, end_angle, 50))
        arc_x = cx + sign * radius * np.cos(theta)
        arc_y = cy + sign * radius * np.sin(theta)
        arc_x = np.array(arc_x)
        arc_y = np.array(arc_y)
        xs.extend(arc_x.tolist())
        ys.extend(arc_y.tolist())
        x = arc_x[-1]
        y = arc_y[-1]
        heading = (heading + sign * angle) % 360

        arc_length = 2 * np.pi * radius * (angle / 360)
        track_length += arc_length 

        v_corner = np.sqrt(coeff_friction * g * radius)
        
        time = arc_length / v_corner
        total_time += time


plt.figure(figsize=(8,8))
plt.plot(xs, ys, 'k-', linewidth=2)
plt.axis('equal')
plt.title('Abu Dhabi Grand Prix Track Layout')
plt.xlabel('X (m)')
plt.ylabel('Y (m)')
plt.grid(False)
plt.show()


print("The total length of the track measures", np.round(track_length, 2), "metres.")

print("Using", tyre_type, "tyres - with an estimated coefficient of friction of", coeff_friction, "- and a vehicle of", horse_power, "horse power,")
print(weight, "kg weight, and a drag coefficient of", drag_coeff, ", the perfect lap time of this track should be", np.round(total_time, 2), "seconds.")
'''
