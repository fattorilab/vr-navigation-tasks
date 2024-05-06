## Data saving

### Saver.cs

Saves session data, in particular frame-by-frame and objects-specific data. 
First, sets the path for saving (MEF name and path from [MainTask](https://github.com/fattorilab/vr-navigation-tasks/blob/main/Docs/scripts_docs/Task_management.md#maintaskcs)), and fetches the ID of the last session from the database.
Then saves the info in two separate CSV files:
- frame-by-frame data, named `yyyy_mm_dd_IDxxxdata`;
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
  
- objects data, `yyyy_mm_dd_IDxxxsupplement`;
    <details> 
    <summary>List of saved vars</summary>
    
    ```c#
    "Identifier; Type; x; y; z; rot_x; rot_y; rot_z; scale_x; scale_y; scale_z; TimeEntry; TimeExit"
    ```
  </details>



### InteractWithDB.cs
