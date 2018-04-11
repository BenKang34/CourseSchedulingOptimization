# Course Scheduling Optimization

## Application of Optimization to Problem
The original proposal for optimization was to use the class cancellation rate as a target metric. Upon further analysis, we realized that the cancellation rate metric does not directly affect the root cause of certain inefficiencies in the system. We may revisit a modified version of this metric in future analysis, but for now our target metric will be the classroom utilization rate.
Classroom utilization rate can be simply defined as:  
<div style="text-align:center"><img src="https://latex.codecogs.com/gif.latex?\frac{\text{number&space;of&space;registered&space;students}}{\text{room&space;capacity}}" title="\frac{\text{number of registered students}}{\text{room capacity}}" /></div>  

As an example, consider a room that has a capacity of 50 seats with four courses assigned to it and 40 students in each course.  
<div style="text-align:center"><img src="https://latex.codecogs.com/gif.latex?\frac{&space;\text{40&space;registered&space;students}}{\text{50&space;seats&space;available}}&space;=&space;80\%" title="\frac{ \text{40 registered students}}{\text{50 seats available}} = 80\%" /></div>
This is the basic explanation of the metric. An in-depth MIP formulation of this metric will be discussed later in the report. 

## General Description of MIP Formulation  
### Decision Variable
The decision variable that will be used for this optimization is X_ijkl, which is a binary variable used to determine whether a course section has been assigned to a room at a given time of the day. If the value of this variable is 1, it means that the slot has been assigned. If the value is 0, it means the slot has not been assigned. The indexes for this variable are as follows:
* i: course + section as a unique identifier
* j: room
* k: day of the week from Monday to Sunday
* l: time slot {0, 1, …, 14} would be the index of time slots of each day from 10AM – 5PM, and each slot would be 30 minutes  
  
### Objective Function
The objective of this problem would be to maximize the utilization rate, defined as:
<img src="https://latex.codecogs.com/gif.latex?\frac{&space;\text{(number&space;of&space;students&space;registered)}\times\text{(whether&space;or&space;not&space;the&space;room&space;is&space;being&space;used)}&space;}{\text{(room&space;capacity)}}" title="\frac{ \text{(number of students registered)}\times\text{(whether or not the room is being used)} }{\text{(room capacity)}}" />  
This rate will then be summed over all course sections, rooms, days of the week, and time slots. The utilization rate will only be measured when the room is being used, which is to prevent filling up as many time slots as possible. Obtaining a value of 0% when the room is not being used does not help us in the context of this problem, which is why the objective function will account for rooms that are unused. This also gives flexibility with using a room for other purposes if it is not assigned for a class. 
  
### Constraints
The constraints for this optimization problem are as follows:
* Classroom capacity – the room capacity should be bigger than the number of registered students, with a 10% margin
  * *Example: if 40 students are registered, there should be at least 44 seats available in the room*
* Day of the week – Classes should be assigned to specific days as required in the original dataset
* One course per room - Each room should have a maximum of 1 course for each time slot
* Unique room - If a class has several time slots, they must all be placed in the same room
* Time slots – making sure that the slots assigned to a class for a particular day and room will satisfy the number of slots required  
  * *Example: if the duration of a class is 90 minutes, then there should be 3 time slots assigned to it, since each slot is 30 minutes*
* Consecutive slots – the time slots assigned for the course must be consecutive
  * A 90-minute class must be from 10AM – 11:30AM for example, not 10AM-10:30AM then 11:30AM – 12:30PM
* Multiple day classes – If a class is offered on Mondays and Wednesdays, then the slots assigned for Monday should be the same as the slots assigned on Wednesdays (same room and time)  
  
For this project, we did not consider any constraints pertaining to instructor preferences. We also assumed that a course historically offered on Mondays and Wednesdays will also be offered on those same days for future semesters.
  
### Formulation  
![dataflow](https://github.com/BenKang34/CourseSchedulingOptimization/blob/master/Formulation.png)
