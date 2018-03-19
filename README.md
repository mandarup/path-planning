

![Path Planning](img/output.gif?raw=true "path planning")




# Setup

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.


## The map of the highway is in data/highway_map.txt
Each waypoint in the list contains  [x,y,s,dx,dy] values. x and y are the waypoint's map coordinate position, the s value is the distance along the road to get to that waypoint in meters, the dx and dy values define the unit normal vector pointing outward of the highway loop.

The highway's waypoints loop around so the frenet s value, distance along the road, goes from 0 to 6945.554.



# Model

## Trajectory generation

This implementation makes use of spline (used as is from http://kluge.in-chemnitz.de/opensource/spline/, the spline function is in a single header file)

## Acceleration, Deceleration, Jerk avoidance

Avoid jerks by constant acceleration and not-too-harsh, controlled deceleration.

    constant acceleration = 0.224

    deceleration = (speed_of_our_car - speed_of_car_in_front) / (distance between the two cars + 0.02)


## Lane Change

If vehicle in front is too close (within 30m) then check if any of adjacent lanes are not occupied within 30m in front and 5m behind our car, starting with center lane to right lane (search direction/priority). If unoccupied lane found, then select first unoccupied lane to switch. If all adjacent lanes are occupied then decelerate, enforcing crash avoidance logic.

## Crash avoidance

If  a car is detected within 30m (in front) from our car, and no adjacent lanes available to switch to, then our car starts slowing down
at a rate computed as (per 0.02s):

    (speed_of_our_car - speed_of_car_in_front) / (distance between the two cars + 0.02)
