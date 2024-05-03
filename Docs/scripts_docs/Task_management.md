## Task management

The following scripts manage the parameters and options of the task, the behaviour of the player (specifically, its motion).

For the general list: [beam me up, Scotty!](../README.md)

### MainTask

Allows to customize the task (e.g length of reward; epochs duration, disposition of the targets, etc).

Tracks 
Instantiate targets (and obstacles, where the task requires), and shows/hides

<details>

<summary> Inspect list of variables </summary>

**Mind that** only public fields can be customized in the Editor and are saved in the database session-specific entry.
Some fields are non-serialized so that they are not included in Json structure that is saved in the database (see [Saver](./Data_saving.md#Saver)).

```c#
    #region GameObjects and components

    [SerializeField]

    [HideInInspector]
    [Header("GameObjects and components")]

    // Cams
    [System.NonSerialized] Camera camM;
    [System.NonSerialized] Camera camL;
    [System.NonSerialized] Camera camR;

    // Pupil
    [System.NonSerialized] public PupilDataStream PupilDataStreamScript;
    private RequestController RequestControllerScript;
    private bool PupilDataConnessionStatus;

    // Game
    Rigidbody player_rb;
    [HideInInspector] GameObject environment;
    [HideInInspector] GameObject experiment;
    [HideInInspector] GameObject player;

    // Black pixels (for scripts syncing)
    private GameObject markerObject_M;
    private GameObject markerObject_R;
    private GameObject markerObject_L;

    #endregion

    #region Saving info

    [Header("Saving info")]
    public string MEF;
    public string path_to_data = "C:/Users/admin/Desktop/Registrazioni_VR/";
    [System.NonSerialized] public int lastIDFromDB;
    private string identifier;
    [HideInInspector] public int seed;
    [HideInInspector] public long starttime = 0;
    [HideInInspector] public int frame_number = 0;

    #endregion

    #region Reward info

    [Header("Reward")]
    public static int RewardLength = 50;
    [System.NonSerialized] private float RewardLength_in_sec = RewardLength / 1000f;
    public int reward_counter = 0;

    #endregion

    #region Trials Info

    [Header("Trials Info")]

    // Trials
    public int trials_win;
    public int trials_lose;
    [System.NonSerialized] public int current_trial;
    public int[] trials_for_target;
    public int trials_for_cond = 1;

    // States
    public int current_state;
    [System.NonSerialized] public int last_state;
    [System.NonSerialized] public string error_state;

    // Conditions
    private int randomIndex;
    public List<int> condition_list;
    [System.NonSerialized] public int current_condition;

    // Tracking events
    private float lastevent;
    private bool first_frame;

    // Moving timer
    private static bool isMoving = false;
    private static Diagnostics.Stopwatch stopwatch = new Diagnostics.Stopwatch();

    #endregion

    #region Target Info

    [Header("Target Info")]
    public string file_name_positions;
    public List<Vector3> target_positions = new List<Vector3>(); // --> List, because changes size during runtime
    public settingsEnum Target_settings = new settingsEnum();
    public enum settingsEnum
    {
        RandomThree,
        MiddleThree,
        Six,
        All
    };
    GameObject[] targets;
    [System.NonSerialized] public GameObject TargetPrefab;
    public Vector3 CorrectTargetCurrentPosition;

    // Materials
    [System.NonSerialized] public Material initial_grey;
    [System.NonSerialized] public Material red;
    [System.NonSerialized] public Material green_dot;
    [System.NonSerialized] public Material red_dot;
    [System.NonSerialized] public Material final_grey;
    [System.NonSerialized] public Material white;

    #endregion

    #region Epochs Info

    [Header("Epoches Info")]
    // Array, because is not changing size during the runtime
    public float[] FREE_timing = { 0.3f, 0.6f, 0.9f };
    public float[] DELAY_timing = { 0.3f, 0.6f, 0.9f };
    public float[] RT_timing = { 0.3f, 0.6f, 0.9f };

    private List<int> FREE_timing_list;
    private List<int> DELAY_timing_list;
    private List<int> RT_timing_list;

    public float BASELINE_duration = 2f;
    public float INTERTRIAL_duration = 2f;
    private float FREE_duration;
    private float DELAY_duration;
    private float RT_maxduration;
    public float MOVEMENT_maxduration = 6f;
    public float second_RT_maxduration = 2f;

    #endregion 

    #region Arduino Info

    [Header("Arduino Info")]
    [System.NonSerialized] public Ardu ardu;
    [System.NonSerialized] public float arduX;
    [System.NonSerialized] public float arduY;

    #endregion

    #region PupilLab Info

    [Header("PupilLab Info")]
    [System.NonSerialized] public Vector2 centerRightPupilPx = new Vector2(float.NaN, float.NaN);
    [System.NonSerialized] public Vector2 centerLeftPupilPx = new Vector2(float.NaN, float.NaN);
    [System.NonSerialized] public float diameterRight = float.NaN;
    [System.NonSerialized] public float diameterLeft = float.NaN;
    [System.NonSerialized] public bool pupilconnection;

    #endregion

```

</details>

### Movement
