# vr-navigation-tasks

Super repository containg all VR tasks, and the relative documentation.

It contains Virtual Reality (VR) tasks built to test 3D goal-directed navigation. The tasks projects are built and run on Unity (version: 2022.3.20f1).
Each project is a state-machine (thereby, the logic is coherent) and represents a different task.

These VR projects expects:
- an Arduino, to control the movements of the participant and release a reward;
- [Pupil Labs](https://docs.pupil-labs.com/) eye-recording technology, to record eye movement data;
- [OpenEphys](https://open-ephys.org/gui) for neural recordings;
- a directory tree of the kind: 
    - OUTPUTDIR;
        - MEFXX (where XX is the participant ID);
          - VIDEOS;
          - DATI;
- a database file in .db format (expected at OUTPUTDIR/MEFXX/database.db), to record session-specific details;

Tasks sessions are run entirely in Editor Mode, to allow for customization of the tasks parameters (e.g. epochs duration, number of targets). 
Each sessions starts at the start of the game (click on start icon), and ends whenever all task conditions are satisfied (or upon clicking again on the start icon).
Per each session, a set of files are given in output:
- recorded screen videos + videos timestamps;
- frame data, including player movement data, eye data, etc;
- objects data, including timestamp of appearance/disappearance.
- 
At present, the available tasks are:

- [Fixation task](https://github.com/fattorilab/Fixation_3D_Task);
- Trial structure task - [free choice](https://github.com/fattorilab/Trial_Structure_free_choice_Task);
- Trial structure task - [given target](https://github.com/fattorilab/Trial_Structure_given_target_Task);
- Trial structure task - [Many obstacles](https://github.com/fattorilab/Trial_Structure_many_obstacles_Task);
- Trial structure task - [Many obstacles eat all](https://github.com/fattorilab/Trial_Structure_many_obstacles_eatAll_Task);

See task_design.pdf for details on the design of each task.

## Documentation

Since each task share a standard structure, a general [documentation](https://linktodocumentation) is provided for the C# scripts managing the and supporting data saving, video recording and interface with the experimental setup (e.g. joystick).

## Update submodules

If the repository of a task is modified, use the following to pull the latest changes in this super repository:

```bash
  git submodule update --remote
```
