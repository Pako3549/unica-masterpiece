## FoodGuard: Fridge Monitoring System

FoodGuard is an Iot system designed to turn a traditional refrigerator into a smart fridge, with the aim of saving energy and respecting the environment.

With the aid of a neural network, it recognizes the products that the user placed in the fridge and catalogs them to display them in an application. Additionally, the system is equipped with various sensors that monitor the fridge's parameters, providing alerts if those compromise the food's preservation.

## Features

- Food recognition
- Temperature and humidity control
- Weight control for each shelf
- Detection of spoiled food
- Door status detection

## MOBILE APP
It has been developed a cross platform app to allow users to keep an eye on their fridge. Its interface is simple and intuitive, providing a pleasant user experience.

It is possible to monitor:

- **Sensor values**:
    - Temperature and humidity
    - Concentration of gases emitted during the food spoilage process
    - Weight of each shelf
- **Products**, inside the fridge.

## HARDWARE
The system is designed to be simple and accessible to everyone; its brain is represented by these 2 boards:

- **STM32F401RE**, fetches the sensors data, processes them and sends them to the Jetson Nano through Serial Communication 
- **Jetson Nano**, runs the AI program to recognize food and sends data (AI and sensors) to Firebase using the Wi-Fi

These are connected to the following sensors and devices:

- **DHT22**: a sensor that monitors temperature and humidity
- **HX711**: a weight sensor that provides the total weight of each shelf
- **HC-SR04**: an ultrasonic sensor that detects the door status (open or closed)
- **Camera**: any type of camera that allows the neural network to process the video stream for food recognition