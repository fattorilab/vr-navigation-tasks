## Connection to the experimental setup

The following scripts manage the connection and communication with Arduino (that interfaces Unity with the joystick, the reward delivery system and OpenEphys neural recorder) and with Pupil Labs eye recorder.

For the general list: [beam me up, Scotty!](../README.md)

### arduino.cs

Contains methods to:
- Establish a connection with Arduino through the appropriate serial port;
- Parse Arduino response to get joystick data (i.e x, y);

<details>

<summary> Inspect list of variables </summary>

```c#

    #region Variables Declaration

    // Declare a SerialPort object to communicate with the Arduino
    SerialPort sp;

    // List to store the last X values received from the Arduino
    List<float> lastXValues = new List<float>();

    // List to store the last Y values received from the Arduino
    List<float> lastYValues = new List<float>();

    // The speed (in baud rate) of the COM port communication
    int COMspeed;

    // The COM port to use for communication
    string COM;

    // The deadzone for the joystick
    public int JSdeadzone;

    // A separate thread to handle the joystick sampling
    Thread thread;

    // A flag to indicate when to stop the thread
    bool stopThread;

    // The time of the last sample
    float lastSampleTime;

    #endregion

```

</details>

### Ardu.cs

Establishes a connection with Arduino. Continuously (i.e. on update) checks the connection, and fetches coordinates data (x, y). If connection fails at any time, a pop-up appears asking to keep going in testing mode, or quit the application.

Moreover this scripts provide the methods to:
- start/stop OpenEphys neural recording;
- deliver the reward through the reward delivery system.

<details>

<summary> Inspect list of variables </summary>

```c#

    #region Variables Declaration

    // GameObject
    arduino ardu;

    // Connection bools
    private bool ans = false;
    bool ardu_working = true;
    bool testing = false;

    // Port
    public string COM = "COM10";

    // Axes
    public float ax1 = float.NaN;
    public float ax2 = float.NaN;

    // Reward counter
    public int reward_counter;

    #endregion

```

</details>

**Ardu.cs depends on**:
- arduino.cs - to establish connection with the serial port, and send/receive signals.


### Pupil Lab

**Work in progress**
