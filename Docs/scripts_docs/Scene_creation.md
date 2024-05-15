## Scene creation

For the general list: [beam me up, Scotty!](../README.md)

### Ground

Sets the position of the ground: in particular, it defines the position of each of the 9 squares composing the game area. If the player moves beyond a given distance from the center of the game area in a certain direction, the 3 squares behind the player are deleted and 3 squares in front are created.
A method is defined for every direction (`SwapForward()`, `SwapBackward()`, `SwapRight()`, `SwapLeft()`). An example is reported below.

<details>
<summary>Inspect code</summary>
    
```c#
    private void SwapForward()
    {
        //old_grounds
        ground[0] = applied_ground[0];
        ground[1] = applied_ground[1];
        ground[2] = applied_ground[2];

        ground[3] = applied_ground[3];
        ground[4] = applied_ground[4];
        ground[5] = applied_ground[5];

        ground[6] = applied_ground[6];
        ground[7] = applied_ground[7];
        ground[8] = applied_ground[8];


        //new grounds
        applied_ground[0] = ground[6]; // new
        applied_ground[1] = ground[7]; // new
        applied_ground[2] = ground[8]; // new

        applied_ground[3] = ground[0];
        applied_ground[4] = ground[1];
        applied_ground[5] = ground[2];

        applied_ground[6] = ground[3];
        applied_ground[7] = ground[4];
        applied_ground[8] = ground[5];


        applied_ground[0].GetComponent<CreateTreesAndTargets>().deleteGreenery();
        applied_ground[1].GetComponent<CreateTreesAndTargets>().deleteGreenery();
        applied_ground[2].GetComponent<CreateTreesAndTargets>().deleteGreenery();
        applied_ground[0].GetComponent<CreateTreesAndTargets>().createGreenery();
        applied_ground[1].GetComponent<CreateTreesAndTargets>().createGreenery();
        applied_ground[2].GetComponent<CreateTreesAndTargets>().createGreenery();

    }
```
  
</details>

In the `Update()`, the methods for swapping the ground are called whenever a certain change in the player position is detected.

<details>
<summary>Inspect code</summary>
    
```c#
    void Update()
    {
        if (player.transform.localPosition.x > midX + 25 || player.transform.localPosition.x < midX - 25 ||
            player.transform.localPosition.z > midZ + 25 || player.transform.localPosition.z < midZ - 25)
        {
            // Changing the value 40 in midX/midZ +/- 40, will increase/decrease the area beyond which the trigger is caused
            if (player.transform.localPosition.x > midX + 40) { midX += 50; SwapRight(); }
            if (player.transform.localPosition.x < midX - 40) { midX -= 50; SwapLeft(); }
            if (player.transform.localPosition.z > midZ + 40) { midZ += 50; SwapForward(); }
            if (player.transform.localPosition.z < midZ - 40) { midZ -= 50; SwapBackward(); }
            SetGround();
        }

    }
```
  
</details>

### Createtargetsandtrees

### Rocks
