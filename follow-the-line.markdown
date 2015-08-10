---
layout: default
title:  "Let's get rolling"
num: 1
---

Basic rule of life: if you don't move, things are going to get pretty hard up the road... Now different creature and inventors found various way to solve that issue. To each hardware solution come a different software solutions; you don't control a car, a 4 leg robot or a rocket the same way. In this section we'll discover how to make your robot move and race for the stars.

<img src="./assets/marxbot.jpg" alt="picture of the marxbot" style="height:300px; float:right; margin:10px;">

## a)  Acting: One robot, two wheels

Actually, they are *treels*, a portmanteau neologism of wheels and trails. But for all your concern, you can see the robot as two wheels along the horizontal axis. Controlling your robot movement will go through setting the speed of each of those wheels. For that we use the `robot.wheels.set_velocity(leftSpeed, rightSpeed)` function which accept two values (yep, left and right speed) as parameters, both measured in cm/s. Positive values will make wheels roll forward, negative will make them roll backward.

Is this this simple to control your robot? Yes it is. Try out various speed for each wheels to get a feeling of how the robots is moving. Fors instance, moving straight is *leftSpeed = rightSpeed*, turning right is *leftSpeed > rightSpeed* and turning on your self is *leftSpeed = -rightSpeed*. While your moving is aimless, you can already think of a few stuff to do with it. Try to draw shapes with your robots for instance (while circle are pretty straight forward, try to draw triangles or more complexe shapes!).

While setting directly the wheels' speed does the job, it's not really practical. If the robots think it terms of left and right speed, we don't really. We would be more used to think in terms of moving forward/backward and turning, like when driving a car. So, we want to feed the robot forward speed and an angular speed, and it to translate it to left wheel speed and right wheel speed. Any idea? Try to imagine if you have only forward or angular, and then compose the two of them. Below are a possible solution, don't look at it before you've tried out a little bit by yourself! To make it easier to use, we formalised it as a function. A good time if ever to see how they are coded in Lua!

<img src="./assets/robot_wheels.png" alt="picture of the differential drive" style="float:right; margin:10px;">

```lua
function driveAsCar(forwardSpeed, angularSpeed)

  -- We have an equal component, and an opposed one   
  leftSpeed  = forwardSpeed - angularSpeed
  rightSpeed = forwardSpeed + angularSpeed

  robot.wheels.set_velocity(leftSpeed,rightSpeed)
end
```

Pretty good. You can already use this formalisme to get a better control over the robot. But we don't want to pilot our robots as a car (from the inside) we want to give him orders (from the outside). We'll see later on even better ways to command the robot. For now, we have more important issues, our robot is ... a bit blind.

## b) Sensing: ground color
Don't expect 20/20 vision right from the start. Especially that our robots are meant to work as a team. As such, they are not pumped up with sensors all around and heavy computing power. They are simple (who said stupid?!) robots, with local sensing. And we'll get as local as one can get: watching the ground under your own feet (well, treels). Or more precisely, detecting gray level of the ground's color. This is very usefull for reading marks on the ground, would that be for communcation purpose, to emphasize areas of purpose, to guide robot over a path...

<img src="./assets/robot_motor_ground.png" alt="ground sensor" style="float:right; margin:10px;">

The robot have 4 grounds sensors on its lower part, each reading the brightness of the ground under them. They output a value between 0 and 1; 0 for black and 1 for white, shades of gray for values in between. Each readings is a table composed of *value* an *offset*. The value refers to the brightness and the offset to a vector for the position of the specific sensor stemming from the centre of the robot. Since we have 4 sensors, we have 4 of those readings. They are all contained in the `robot.motor_ground` table and can be accessed as follow:

```lua
log("----")
for i = 1,4 do
    log(robot.motor_ground[i].value)
    log(robot.motor_ground[i].offset.x .. " " ..
        robot.motor_ground[i].offset.y)
end
```

In this section's area are a few patterns on the ground, any idea on how to make your robot avoid them? Or get to them and stop? First you'll need a robot that will explore a bit by itself. We'll study this point better in next section, but you can already mash up something together with the `robot.random.uniform(min, max)` function. Try to implement one of both algorithm by yourself. Below is a solution for the stopping one:

```lua
onSpot = true
for i = 1,4 do
    if( robot.motor_ground[i].value > 0.10 )
      onSpot = false
end

if(onSpot)
  driveAsCar(0,0)
else
  driveAsCar(robot.random.uniform(10,20), robot.random.uniform(-10,10))
```

## c) Behaving: Follow the line
Ok, that is great. You can move, you can see where you are moving. What about coupling both and making you move following marks on the ground? This is actually a very classic algorithm which is solved both by first learners and highly skilled engineers, depending on the environment and precision/speed required. Our job is simple: we have a line, we want to follow it, when it's straight (simple!) and when it's turning (ergh...).


```lua
-- OK, weirdly, for that one, left and right speed was more practical...
-- Try to redo it with the car metaphor!

leftSpeed = 5
rightSpeed = 5

if(robot.motor_ground[1].value > 0.80) -- something on my left
  rightSpeed = -3

if(robot.motor_ground[4].value > 0.80) -- something on my right
  leftSpeed = -3

robot.wheels.set_velocity(leftSpeed, rightSpeed)
```

You might actually have different result depending on the speed you put on your robot. Since your robot has an inertia, and that we didn't make him turn to himself, you might encounter weird behavior. You might want to fine tune a few of the parameters to get the behavior you want!

## d) Playing: Everybody race now!
And when I say *everybody* I mean *your lone robot* (Pss, don't worry, problem solved at next section!). You have know all the basic tools at hand to play this racing game. This part here is not about following the course but about experimentating. Try to play with the bricks you know, try new ones, and make your racer the faster! If you want new tracks, you can always draw one over the background picture with your favorite image editor. Share trackes, race other IA, and compare your time! 

