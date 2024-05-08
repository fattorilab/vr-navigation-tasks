## Data saving

### Saver.cs

Saves session data, in particular frame-by-frame and objects-specific data. 
First, sets the path for saving (MEF name and path received from [MainTask](https://github.com/fattorilab/vr-navigation-tasks/blob/main/Docs/scripts_docs/Task_management.md#maintaskcs)), and fetches the ID of the last session from the database (see InteractWithDB.cs).

  <details> 
  <summary>Inspect code</summary>
  
  ```c#
    void Awake()
    {
        #region Choose monkey and set path

        string MEF = GetComponent<MainTask>().MEF;
        path_to_data = GetComponent<MainTask>().path_to_data;
        if (MEF.ToLower() == "ciuffa") { path_to_MEF = Path.Combine(path_to_data, "MEF27"); }
        else if (MEF.ToLower() == "lisca") { path_to_MEF = Path.Combine(path_to_data, "MEF28"); }
        else
        {
            bool ans = EditorUtility.DisplayDialog("Wrong MEF name", "Unable to find the monkey" + MEF, //don't know how to put a simple popup here (the choice is irrelevant)
                            "Close and check MEF in MainTask");
            QuitGame();
        }

        Debug.Log($"If desidered, files will be saved in {path_to_MEF}");

        #endregion

        #region Connect to DB and get last ID

        try
        {
            DB = GameObject.Find("DB");
            string path_to_DB = Path.Combine(path_to_MEF, "esperimentiVR.db");
            lastIDFromDB = DB.GetComponent<InteractWithDB>().GetLastIDfromDB(path_to_DB);
        }
        catch
        {
            bool ans = EditorUtility.DisplayDialog("Cannot interact with DB", "It is not possible to read last ID from database. You may not to be able to save data",
                            "Close and check DB", "Proceed anyway");
            if (ans) { QuitGame(); }
        }

        #endregion

    }
      
  ```
  </details>

Then saves the info in two separate CSV files:
- frame-by-frame data, recorded at each `LateUpdate` and stacked in a list then saved into a CSV file, named `yyyy_mm_dd_IDxxxdata`;
  <details> 
    <summary>List of saved vars</summary>
    
    ```c#
      string general_vars = "Unity_timestamp; Frame; ";
      string task_general_vars = "Trial; Correct Trials; Current_condition; Current_state; Error_type; Reward_count; ";
      string task_specific_vars = ""; // correct_target; interval; 
      string move_vars = "player_x_arduino; player_y_arduino; player_x;  player_y; player_z; player_x_rot; player_y_rot; player_z_rot; ";
      string eyes_vars = "pupil_timestamp; px_eye_right; py_eye_right; px_eye_left; py_eye_left; " +
                              "eye_diameter_left; eye_diameter_right";
    ```
  </details>
  
- objects data, recorded at the time of object appearance/disappearance by the script that instantiates/activates/deactivates the object (search for `addObject` and `addObjectEnd` in MainTask.cs, CreateTrees.cs), and stacked in a list then saved to a CSV file, named `yyyy_mm_dd_IDxxxsupplement`;
    <details> 
    <summary>List of saved vars</summary>
    
    ```c#
    "Identifier; Type; x; y; z; rot_x; rot_y; rot_z; scale_x; scale_y; scale_z; TimeEntry; TimeExit"
    ```
  </details>

Finally, the script saves all public fields from MainTask (and optionally from any other script, e.g Movement.cs, provided modifications to the code) into a JSON structure then saved as entry to the database.

### InteractWithDB.cs
