# design-patterns

You're creating a home automation system with a set of different sensors, e.g. a weather sensor (outside) and a pressure sensor (beneath your doormat). For each sensor, you have a class that handles communication with the sensor and retrieves some data. You also have a processor class for each sensor that manipulates the raw sensor data and turns it into something usable. Lastly, you want to have a class that formats the now processed data into a nice string representation to display it on your monitor. The monitor is also a class.

## Implementation

Each sensor, processor and formatter have an abstract base class that declares the interface of the particular part of the system. Then, you subclass each abstract base class for each sensor. The individual *families* of subclasses are contained in the *factories*. There is one factory class per sensor. Also the factories have an abstract base class (the *abstract factory*) that declares the factory methods. When the home automation system wants to display data from a certain sensor, it simply passes a different factory to the monitor class, which will then 

The flexibility of
