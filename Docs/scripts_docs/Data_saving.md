## Data saving

The following scripts provide and call the methods for saving session-specific data and adding an entry to the database.

For the general list: [beam me up, Scotty!](../README.md)

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

    [Notice](#notice-last_state): Current_state refers to the variable `last_state` of the MainTask, even though there is a `current_state` defined. This is due to the fact that, when changing state, `current_state` is updated to the next state value at the end of the current state, e.g passing from 0 to 1, current_state becomes 1, during the end of state 0. This is because `current_state` is used to shift between states. On the other hand, the variable `last_state` updates to 1 only at the beginning of state 1, therefore it is in sync with the state-machine.)
  

  </details>
  
- objects data, recorded at the time of object appearance/disappearance by the script that instantiates/activates/deactivates the object (search for `addObject` and `addObjectEnd` in MainTask.cs, CreateTrees.cs), and stacked in a list then saved to a CSV file, named `yyyy_mm_dd_IDxxxsupplement`;
    <details> 
    <summary>List of saved vars</summary>
    
    ```c#
    "Identifier; Type; x; y; z; rot_x; rot_y; rot_z; scale_x; scale_y; scale_z; TimeEntry; TimeExit"
    ```
  </details>

Finally, the script saves all public fields from MainTask (and optionally from any other script, e.g Movement.cs, provided modifications to the code) into a JSON structure then saved as entry to the database.
To add parameters of another script, modify the region `Add recording to the DB` (within the method `saveAllData()`), specifically:

  <details> 
  <summary>Inspect code</summary>
    
  ```c#
      // Get parameters from public fields of main and movement
      string jsonMainTask = JsonUtility.ToJson(main, true);
      string jsonMovement = JsonUtility.ToJson(player.GetComponent<Movement>(), true);
      string new_Param = "{ \"MainTask script params\": " + jsonMainTask
          + ", \"Movement params\": " + jsonMovement + " }";
  ```
  </details>

When closing the session, the user is prompted to confirm the saving of the data structures through popups (the answer to whom is also used by the [Video_recording](https://github.com/fattorilab/vr-navigation-tasks/blob/main/Docs/scripts_docs/Video_recording.md) scripts for the same purpose).

**Saver depends on**:
- OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO;

### InteractWithDB.cs

Provides methods used to:

- Connect to the database and fetch the ID of the last session;
  <details> 
    <summary>Inspect code</summary>
    
    ```c#
    public int GetLastIDfromDB(string path_to_DB)
    {

        int lastID = -1; // Initialize lastID with a default value in case no records are found

        Debug.Log($"Connecting to DB (DB_filepath={path_to_DB}) to READ LAST ID");
        conn = "URI=file:" + path_to_DB;

        using (dbconn = new SqliteConnection(conn))
        {
            dbconn.Open();

            dbcmd = dbconn.CreateCommand();
            sqlQuery = "SELECT * FROM Recordings ORDER BY ID DESC LIMIT 1;";
            dbcmd.CommandText = sqlQuery;

            dbreader = dbcmd.ExecuteReader();

            // Check if there's a record in the result
            if (dbreader.Read())
            {
                // Get the value of "ID" from the current record
                lastID = dbreader.GetInt32(dbreader.GetOrdinal("ID"));
            }

            dbreader.Close();
            dbconn.Close();

            // Return the lastID value
            Debug.Log("Last ID from DB " + lastID);
            return lastID;
        }
    }
    ```
  </details>

- Add an entry to the database.
  <details> 
    <summary>Inspect code</summary>
    
    ```c#
    public void AddRecording(string path_to_DB, int new_ID, string new_Date, string new_Task, string new_Param)
    {
        Debug.Log("Connecting to DB " + $"DB_filepath={path_to_DB} to ADD NEW RECORDING");
        conn = "URI=file:" + path_to_DB;

        using (dbconn = new SqliteConnection(conn))
        {
            dbconn.Open();

            dbcmd = dbconn.CreateCommand();
            sqlQuery = "INSERT INTO Recordings (ID, Date, Task, Param) VALUES ('" + new_ID + "','" +  new_Date + "','" + new_Task + "','" + new_Param + "')";
            dbcmd.CommandText = sqlQuery;

            dbcmd.ExecuteNonQuery();

            dbconn.Close();

            Debug.Log("New rec. added to DB with ID " + new_ID);
        }
    }
    ```
  </details>


