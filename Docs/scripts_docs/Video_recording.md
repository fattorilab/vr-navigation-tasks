## Video recording

Video recording exploits the Unity plugin [FFmpegOUT](https://github.com/keijiro/FFmpegOut?tab=readme-ov-file#installation) by [keijiro](https://github.com/keijiro). 
Two scripts () where customly modified to satisfy our requirements, including control over video saving and tracking of video timestamps.
Those scripts are located in `Assets/FFmpegOUT/Runtime`.
The modifications are reported below.

For the general list: [beam me up, Scotty!](../README.md)

### FFmpegSession.cs

<details> 
<summary>Inspect code</summary>
  
```c#
public static FFmpegSession Create(
    string name,
    int width, int height, float frameRate,
    FFmpegPreset preset
)
{
    #region CUSTOM MODIFICATION: SET OUTPUT PATH

    GameObject experiment = GameObject.Find("Experiment");
    string path_to_MEF = experiment.GetComponent<Saver>().path_to_MEF;
    int lastIDFromDB = experiment.GetComponent<Saver>().lastIDFromDB;
    string fileName = DateTime.Now.ToString("yyyy_MM_dd") + "_ID" + (lastIDFromDB + 1).ToString() + $"_{name}";
    fileName += preset.GetSuffix(); // adds .mp4
    path_to_video = Path.Combine(path_to_MEF, "VIDEO", fileName);
    #endregion

    //name += System.DateTime.Now.ToString(" yyyy MMdd HHmmss");
    //var path = name.Replace(" ", "_") + preset.GetSuffix();
    return CreateWithOutputPath(path_to_video, width, height, frameRate, preset);
} 
```
</details>


### CameraCapture.cs

This script creates the video frame-by-frame: it estimates the time elapsed (i.e. gap) from the last captured frame, and compares it with the video frame rate: if the gap is equal to 2 frames, the current frame is saved twice; in case it is N > 2 frames, a single frame is saved and the count is increased by N.

To simplify the syncing of video and task data for tracking of events, player and eye movements, a frame-by-frame CSV file is produced here.
The CSV file has a row per each frame, except the header. 
The variable `Current_state` refers to `last_state` of the MainTask script, that is the same `Current_state` recorded by the saver (to know whey, check [Notice](#Notice)). (This is due to the fact that, when changing state, `current_state` is updated to the next state value at the end of the current state, e.g passing from 0 to 1, current_state becomes 1, during the end of state 0. This is because `current_state` is used to shift between states. On the other hand, the variable `last_state` updates to 1 only at the beginning of state 1, therefore it is in sync with the state-machine.)

The files nomenclature is the following:
- video, `yyyy_mm_dd_IDxxx_Main Camera`
- timestamps, `yyyy_mm_dd_IDxxx_Main Camera_recorderFrames`

<details> 
<summary>Inspect code</summary>
  
```c#

#region ADDED BY EDO: SAVE RECORDER FRAMES

GameObject experiment = GameObject.Find("Experiment");
int main_frame_num = experiment.GetComponent<MainTask>().frame_number;
int last_state = experiment.GetComponent<MainTask>().last_state;
long main_start_time = experiment.GetComponent<MainTask>().starttime;
int reward_count = experiment.GetComponent<Ardu>().reward_counter;

// Check if the StreamWriter is not initialized
if (!_isStreamWriterInitialized)
{

    // Get path_to_data and lastIDFromDB from the Saver
    string path_to_MEF = experiment.GetComponent<Saver>().path_to_MEF;
    int lastIDFromDB = experiment.GetComponent<Saver>().lastIDFromDB;

    // Check if path_to_data and lastIDFromDB are not null or zero
    if (!string.IsNullOrEmpty(path_to_MEF) && lastIDFromDB != 0)
    {
        path_to_data_RecorderFrames = Path.Combine(path_to_MEF, "VIDEO",
            (DateTime.Now.ToString("yyyy_MM_dd") + "_ID" + (lastIDFromDB + 1).ToString() + $"_{camera.tag}" + "_recorderFrames.csv"));
        _streamWriter = new StreamWriter(path_to_data_RecorderFrames);
        _streamWriter.WriteLine("Timestamp,Frame_time,Rec_Frame,Droppings,Dropped_frames,Reward_count,Current_state");

        // Set the flag to true after initializing the StreamWriter
        _isStreamWriterInitialized = true;
    }
}

#endregion

#region ADDED BY EDO: Methods

private void addRecorderFrameRow(long main_start_time, int reward_count, int last_state)
{
    // After pushing the frame to FFmpeg, write the details to the CSV file.
    if (_isStreamWriterInitialized)
    {
        long rec_time = System.DateTimeOffset.Now.ToUnixTimeMilliseconds();
        _streamWriter.WriteLine($"{(rec_time - main_start_time)},{FrameTime},{_frameCount},{_frameDropCount},{_frameDelay},{reward_count},{last_state}");
    }
}

#endregion
```
</details>

At the end of application, a popup prompts the user to decide either to save the video (and related timestamps) or to delete it.

<details> 
<summary>Inspect code</summary>

  ```c#

#region ADDED BY EDO: Save/Delete depending on user choice

// VIDEO

if (!Saver.wants2saveVideos)
{
    File.Delete(path_to_video);
}

// CSV of recorder frames
if (_isStreamWriterInitialized)
{
    _streamWriter.Close();

    // Save or delete csv file depending on user prompt
    if (!Saver.wants2saveVideos)
    {
        File.Delete(path_to_data_RecorderFrames);
    }
}

#endregion
```
</details>


