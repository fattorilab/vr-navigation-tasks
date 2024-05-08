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

<details> 
<summary>Inspect code</summary>
  
```c#
#region ADDED BY EDO: RECORDING FRAMES AND TIMESTAMPS

GameObject experiment;
private StreamWriter _streamWriter;
private bool _isStreamWriterInitialized = false;
public string path_to_data_RecorderFrames;

// Movie path
public string path_to_video;

#endregion
```
</details>


