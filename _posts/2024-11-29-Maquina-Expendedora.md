---
layout: post
title: "Expending Machine"
date: 2024-09-25 19:39:09 +0200
categories: sistemas-empotrados
---

## Introduction

This practice consists of making a coffee vending machine using the sensors provided by the university and programming in Arduino. As mentioned, the practice involves a coffee machine which must perform the following tasks:

### Load:

The machine must display the word "Cargando..." on the LCD while an LED turns on and off three times. When the LED blinks for the third time, the machine changes state to a customer waiting state.

### Service state:

#### Waiting client:

In this state, the machine displays the words "Esperando Cliente" on the LCD until it detects that the customer is less than one meter away. Then it transitions to the service state.

#### Temperature and humidity detection:

In this state, the machine shows the temperature and humidity values for 4 seconds, displaying the values dynamically.

#### coffees menu:

In this state, a menu of coffees with different prices is displayed, which you can navigate using a joystick. Pressing the joystick changes to a coffee preparation state, where the LCD displays "Preparando cafe" while an LED lights up progressively until the coffee is ready. Then the LCD tells you to "RETIRE BEBIDA," and it returns to the menu state.


#### Returning to server start:

If you press a button and release it after 2 to 3 seconds of pressing, the machine returns to the initial service state (customer detection).

### Admin:

By pressing the button for 5 seconds, you switch to an administrator state where you can perform the following tasks:

#### Leds on:

While the user is in the Admin view, both LEDs must be on.

#### Admin Menu:

The following menu must be displayed on the LCD:
 - Ver temperatura
 - Ver distancia sensor
 - Ver contador
 - Modificar precios

##### Ver temperatura:

The admin can access and view the temperature and humidity values detected by the machine.

##### Ver distancia sensor:

The admin can view the distance detected by the ultrasonic sensor.

##### Ver contador:

From the moment the board starts, a counter in seconds is initiated, which the admin can access.

##### Modificar precios:

The admin can access the coffee prices in the machine and modify them as desired. After modifying, the coffee price value is saved in the original coffee menu.

### Returning to menu:

If the same button is pressed again for 5 seconds, you will stop being an admin and return to the service state.

## Components used:

For this practice, we used the following sensors:

    ● Arduino UNO
    ● LCD
    ● Joystick
    ● Sensor Temperatura/Humedad DHT11
    ● Sensor Ultrasonidos
    ● Botón
    ● 2 LEDS normales (LED1, LED2)

## Machine functionality

The operation and control of this coffee vending machine mainly work with a state machine in which I control each of the machine's states to achieve the expected result. Each state has its particular function, and below, I will explain the utility of each state:

### STARTING_STATE:

This state calls the function `arranque_led()` which displays the word "CARGANDO..." on the LCD while an LED blinks 3 times for one second. This is achieved using a counter, which starts at 0 and increases every time the LED blinks. When the LED blinks for the third time, the LCD is cleared, and the state changes.

### NEAR_DETECTION_STATE:

This state calls the function `near_detection()` which is responsible for obtaining the distance from the ultrasonic sensor, and if the distance is less than one meter, it changes state. If this does not happen, it prints "ESPERANDO CLIENTE" on the LCD. In this case I have put the detection theshold in 5 cm instead of 100 cm in order to show better how this state works.

### TEMP_HUM_STATE:

This state calls the function `print_temperature()` which displays the temperature and humidity values detected by the sensor for 5 seconds. After 5 seconds, it changes state.

### MENU_STATE:

This state calls the function `menu_selection()` which displays the coffee menu and navigates through it using the joystick. Moving the joystick along the y-axis modifies a counter depending on whether it is moved up or down. If the joystick's returned value is less than 350, the counter decreases by one. Conversely, if the joystick's reading is greater than 700, the counter increases. Based on the counter's value, the LCD will display a different coffee from the menu. I also limited the counter's value to go from 0 to 4, ensuring it always shows the menu's coffees. Pressing the joystick transitions to the next state.

### PREP_COFFEE:

This state calls the function `preparing_coffe()` which displays "Preparando Cafe..." on the LCD for 4 to 8 seconds while an LED progressively lights up over time. After the seconds have passed, it prints "RETIRE BEBIDA" on the LCD for 3 seconds, turns off the LED, and returns to `MENU_STATE`.

### ADMIN_STATE:

This state can only be accessed by pressing the button for 5 seconds. This is achieved through an interruption explained later in the "Interruptions" section. This state calls the function `admin_menu()` which displays a menu of actions the admin can perform. Its operation is similar to `MENU_STATE`. You can navigate the menu using the joystick and a counter. Pressing the joystick on a specific section of the admin menu transitions to the following states. Note that in any of the following states, if you move the joystick left on the x-axis, it will return to this state showing the admin menu.

#### ADMIN_SHOW_TH:

This state simply calls the function `show_th()` which displays the temperature and humidity values detected by the machine until you decide to exit the state.

#### ADMIN_SHOW_DIST:

This state calls the function `show_dist()` which makes the LCD display the distance in centimeters detected by the machine's ultrasonic sensor until you decide to leave the state.

#### SHOW_TIME:

This state calls the function `show_time()` which calculates the time elapsed since the board started. This is achieved by initializing a variable in the setup that calls `millis()` and later, in the function, calling another variable with `millis()`. Subtracting and dividing it by 1000 gives the seconds elapsed since the board started. This value is then displayed on the LCD until you decide to leave the state.

#### CHANGE_MENU:

This state calls the function `change_menu()` which navigates through a similar menu where it shows a coffee and its respective price. Pressing the joystick changes the state to modify its price.

#### MOD_PRICE:

In this state, depending on the coffee selected in the `CHANGE_MENU` state, you can modify its price. If you move the joystick up on the y-axis, the price will increase by 5 cents. Similarly, moving it down decreases the price. Pressing the joystick after modifying the price saves the value and returns to `CHANGE_MENU`. Leaving the admin state and returning to service mode shows the modified price.

## Interruptions:

Interruptions in Arduino are mechanisms that allow the microcontroller to respond quickly to certain external or internal events, temporarily halting the execution of the main program to execute a specific function called 'Interrupt Service Routine' (ISR). Once the ISR is completed, the main program resumes at the point where it was interrupted.


### Joystick button (JOISTIK_BUTTON):

Configured on the `JOISTIK_BUTTON` pin (pin 3).  
You use an external interrupt configured to trigger when the joystick button detects a falling edge (`FALLING`).  
Associated ISR function: `joistik_button_ISR`.

Objective: Register that the joystick was pressed, changing the `joistik_pressed` variable to `true` if it is in the appropriate state.

Impact: This allows detecting that the user interacted with the joystick to select options in the menus or perform other actions, such as changing prices or confirming selections.

### administrator button (BUTTON_PIN):

Configured on the `BUTTON_PIN` pin (pin 2).  
You use an external interrupt configured to trigger on any button state change (`CHANGE`).  
Associated ISR function: `button_ISR`.

Objective: Register that the button was pressed or released, activating the machine's state-changing logic.

Impact:

This button allows toggling between user and admin modes, depending on how long it is pressed:

    - Pressing for more than 5 seconds: Switches to admin mode if not already in it, or returns to service mode if already in admin mode.
    - Pressing between 2 and 3 seconds: Changes to the initial service state if in service mode and not in admin mode.

The `is_pressed` variable indicates that there is active button interaction, while `buttonPressStartTime` calculates the press duration.

### Advantages os using an interruption in this case:

    - Fast response: The use of interruptions ensures that user actions, such as pressing buttons, are recorded immediately without waiting for the main program to finish a task.

    - Avoid polling: Instead of constantly checking the buttons' state in the main loop, interruptions save time and resources.

    - They facilitate state transitions (such as switching to admin mode) without noticeable delays.

## Practice difficulties:

During this practice, I encountered various challenges both technically and conceptually, requiring time and effort to resolve. Here are some of the most significant difficulties:

### Synchronization with interruptions:

One of the most complex parts was configuring and managing interruptions to ensure that external events, such as pressing the button or joystick, were processed immediately without interfering with the main program's flow.

For example:

    - Joystick button: Configure the appropriate edge (FALLING) and ensure no debounce issues could cause incorrect readings.

    - Admin button: Implement logic to accurately measure the press duration and switch between modes (user and admin) depending on how long the button was held.

### Dynamic menu with joystick:

Designing the coffee and admin menus was another significant challenge. Using the joystick to navigate the options required:

    - Precise readings: Correctly differentiating movements up, down, or left, considering the joystick's readings (between 0 and 1023) and setting appropriate thresholds for each direction.

    - Error prevention: Limiting the menu counter's range so that the options did not overflow (e.g., preventing a value lower than 0 or higher than 4).

### Transitions between states:

Controlling the machine using a state machine was intuitive in concept but complex in implementation. Some challenges included:

    - Avoiding blockages: Ensuring each state had a defined flow and could transition correctly to the next state without getting stuck due to sensor readings or unexpected delays.

    - Data persistence: Ensuring the prices modified by the admin were correctly reflected in the menu and remained consistent while the machine was running.

### LCD visualization:

Configuring the LCD to display relevant information in each state was challenging. This included:

    - Clear and readable design: Ensuring the text was clear enough and did not overlap during state transitions.

    - Creation of custom characters: Designing and displaying symbols such as the Celsius degrees symbol (°C) to make the information more understandable.

### Time adjustments:

Many states, such as coffee preparation or temperature/humidity visualization, required precise timers. Adjusting these times to be appropriate and proportional to the user's actions was an iterative task that required numerous tests.

### Circuit construction and wiring:

One of the most notable challenges was building the circuit and wiring it efficiently and without errors. The vending machine requires integrating several electronic components, such as the Arduino UNO, the LCD, LEDs, the joystick, sensors, and buttons. Some specific challenges included:

    - Limited space on the breadboard: Connecting multiple components quickly filled the breadboard space, making it difficult to maintain an organized design. To address this, I used different colored wires to distinguish power connections (+5V), ground (GND), and signals, ensuring each connection was solid and correctly insulated.

    - Incorrect initial connections: During the initial assembly, some LCD and ultrasonic sensor pins were connected to incorrect positions, causing them not to function. This problem was resolved by carefully reviewing the electrical diagram and testing each component individually before integrating it into the system.

    - Circuit organization: As more components were added, maintaining clean wiring became a challenge. I created documentation of the wiring to facilitate future reviews.


## Future improvements and features:

    - Persistent storage:
        Currently, coffee prices are temporarily modified during program execution but are lost if the machine is restarted. A persistent storage module could be implemented to save these values even after the machine is turned off.

    - Implementation of a payment system:
        Integrate a payment module (e.g., a coin reader) to simulate a real vending machine experience, where customers can pay before preparing the drink.

    - Menu customization:
        Allow the admin to add new products to the menu or remove existing options, increasing the machine's flexibility.

## Personal reflection: Lessons learned

This practice was an enriching experience that allowed me to broaden my knowledge in various areas of electronics and programming, specifically in embedded systems. Below, I detail the main lessons I learned during its development:

### Hardware handling:

One of the most valuable aspects of this practice was working directly with real hardware, facing challenges beyond simulation or pure programming:

    - Integration of multiple components: I learned to connect and coordinate various sensors (DHT11, ultrasonic), actuators (LEDs), and input devices (joystick, buttons) into a functional circuit. This involved understanding how each component interacts electrically with the Arduino board, from managing voltages and currents to configuring pins.

    - Electrical troubleshooting: There were moments when components did not work correctly due to connection errors or signal noise. This forced me to improve my skills in circuit construction to minimize interference.

## Conclusion:

Building this coffee vending machine not only allowed me to apply the knowledge acquired in embedded systems but also helped me understand the importance of integrating hardware and software to achieve a functional and efficient system. Each challenge, from hardware management to implementing state logic and interruptions, represented an opportunity to learn and grow as a developer.

This project demonstrated the importance of careful planning, the ability to solve problems creatively, and the proper approach to ensuring all components work effectively. It also opened the door to future improvements, such as implementing payment systems or persistent storage.

In short, this practice not only enhanced my technical skills but also provided valuable experience in solving real-world problems, preparing me to tackle more complex challenges in the field of electronics and embedded systems.

## Full video Expending Machine:

[Ver el video en YouTube](https://youtu.be/vboUhIjYGVw)

## 2 to 3 sconds interruption:

[Ver el video en YouTube](https://youtu.be/MLskGAv8Qio)

## Temperature and Humidity dinamic values 

[Ver el video en YouTube](https://youtube.com/shorts/cmJOXD264HY)





