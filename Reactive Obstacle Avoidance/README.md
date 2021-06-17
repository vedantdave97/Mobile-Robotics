In this Section, initially the publisher-subscriber communication between the ROS and the MATLAB is developed.
The MATLAB publishes the message on the topic '/mobile_base/command_velocity' and a ROS subscriber
receives these motion command messages and moves the simulated robot according to the
commanded velocities.<br /><br />

<h3>Robot Movement with Publisher</h3>
The Movement of the Robot takes place in the following manner:
- Create a publisher for the '/mobile_base/commands/velocity' topic. Create
an empty message velMsg for that topic. Set the X-component of linear velocity field of the velMsg.Linear.X.
 Publish the message with the command 'send'. The robot should start moving in a straight line. 
 Stop the robot by sending a new message with zero linear velocity.
- Move the robot in a straight line for a specified distance at a specified linear velocity.
The input argument velPub denotes the publisher. The distance traveled by the robot is controlled in an open loop fashion by dead
reckoning as product of commanded velocity and time. The forward motion with
the command velocity executes for a time interval t = d/v seconds. A rate
object with a rate that is inverse proportional to that time interval is utilised.
- The Robot is also rotated on the spot with a particular angular velocity.<br /><br />

<h3>Callback Subscriber for Laser Scan Messages</h3>
We now focus with subscribing to the '/laserScan' topic and processing
the laser scan message with a Callback function.<br /> The 'sensor_msgs/LaserScan.msg' is
mapped onto a handle object of type ScanHandle which is composed of the properties
ranges and angles. Determine the distance and heading to the most imminent obstacle
with which the robot might collide if it does not alter its direction of motion. This obstacle
representation provides the basis for a reactive obstacle avoidance behavior.
The class ScanHandle has properties of '/laser_scan' topic i.e. Ranges(array of range readings in m) and 
Angles(array of angles in rad).<br />
A callback function for the '/laser_scan' topic converts the LaserScanMsg into to the ScanHandle object laserScan.<br /><br/>
The function 'nearestObstacle' determines the  distance r<sub>min</sub> and heading &phi;</sub><sub>min</sub> towards the most relevant
obstacle. The relevance of an obstacle depends on its proximity to the robot but
also to the relative heading. Nearby obstacles in longitudinal direction are far
more relevant for obstacle avoidance than remote obstacles or obstacles in lateral
direction.  Thus the scaled obstacle distance is given by<br />
<img src="https://render.githubusercontent.com/render/math?math=\hat{r}_i = (r_i - r_{robot})(1-\beta cos(\phi_i))">
The robot radius r<sub>robot</sub> is subtracted from the range readings r<sub>i</sub> as those measure the distance to the center of the robot
rather than the distance to the robots perimeter.

<h3>Reactive Obstacle Avoidance</h3>
This is a simple obstacle avoidance behavior which is in terms of a functional mapping between the laser range readings (Subscriber '/laser_scan') and the motor actions (Publisher '/mobile_base/commands/velocity'). Reutilize the handle class, the subscriber Callback and the function nearestObstacle from the previous assignments. The linear velocity v is reduced with decreasing range reading and eventually becomes zero at a stopping distance r<sub>stop</sub>. The robot is supposed
to stop if the scaled obstacle distance is below the threshold distance i.e. 
<img src="https://render.githubusercontent.com/render/math?math=\hat{r}_{min} = r_{min}(1-\beta cos(\phi_{min})) < r_{stop}">
The limitations are shown with the intuitive image below:
![Alt text](Figures/Reactive_obst.JPG?raw=true "Title")