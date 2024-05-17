## Utilities

A set of scripts useful to satisfy different analysis and task related requirements.

For the general list: [beam me up, Scotty!](../README.md)


### SetEditor

Sets the aspect ratio of the game window to 16:9, in order to have a standardized window size for gaze tracking. 
As per our editor settings, 16:9 is the first option in game window, and thereby SetGameView(int sizeIndex) in the code is set to SetGameView(1).
Since it is not necessary the same for your machine, we suggest to check beforehand and modify the code accordingly.

<details> 
<summary>Inspect code</summary>

```c#
static void SetGameView(int sizeIndex)
{
  #if UNITY_EDITOR
        var assembly = typeof(Editor).Assembly;
        var gameView = assembly.GetType("UnityEditor.GameView");
        var instance = EditorWindow.GetWindow(gameView);
        gameView.GetMethod("SizeSelectionCallback").Invoke(instance, new object[] { sizeIndex, null });
  #endif
}

void OnEnable()
{
    if (!started)
    {
        SetGameView(1); // option 1 is 16:9 aspect
        started = true;
    }
}

```
</details> 

### Virtual reality

Sets the distance between the virtual eyes (i.e. left and right cameras) of the participant, in order to allow immersion in the 3D scene.  

<details> 
<summary>Inspect code</summary>

```c#

public float EyeDistanceInCM;

void Start()
{
    Camera camL = GameObject.Find("Left Camera").GetComponent<Camera>();
    Camera camR = GameObject.Find("Right Camera").GetComponent<Camera>();

    camL.transform.localPosition -= new Vector3(EyeDistanceInCM / 200, 0, 0);
    camR.transform.localPosition += new Vector3(EyeDistanceInCM / 200, 0, 0);

    camL.backgroundColor = new Color(49, 77, 121);
    camR.backgroundColor = new Color(49, 77, 121);

    camL.clearFlags = CameraClearFlags.Skybox;
    camR.clearFlags = CameraClearFlags.Skybox;
}
```
</details> 
