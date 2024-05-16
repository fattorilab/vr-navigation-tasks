# vr-navigation-tasks

[<img src="Docs/img/monkey_researcher_little.jpg" width="350"/>](little_mef)

Super repository containg all VR tasks, and the relative documentation.

It contains Virtual Reality (VR) tasks designed to test 3D goal-directed navigation. The tasks projects are built and run on Unity (version: 2022.3.20f1).
Each project is a state-machine (thereby, the logic is coherent) and represents a different task.

These VR projects expects:
- Unity (version: [2022.3.20f1](https://unity.com/releases/editor/whats-new/2022.3.20));
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
  
At present, the available tasks are:

- [Fixation task](https://github.com/fattorilab/Fixation_3D_Task);
- Trial structure task - [free choice](https://github.com/fattorilab/Trial_Structure_free_choice_Task);
- Trial structure task - [given target](https://github.com/fattorilab/Trial_Structure_given_target_Task);
- Trial structure task - [Many obstacles](https://github.com/fattorilab/Trial_Structure_many_obstacles_Task);
- Trial structure task - [Many obstacles eat all](https://github.com/fattorilab/Trial_Structure_many_obstacles_eatAll_Task);

See task_design.pdf for details on the design of each task.

## Get this repo and ready to use

1. Download [Git](https://git-scm.com/downloads) and use the git bash to clone this repository (and all tasks sub-repos) with:

```bash
  git clone --recursive https://github.com/fattorilab/vr-navigation-tasks.git
```

2. Open the [Unity Hub](https://unity.com/download), add a project task (e.g. Trial_Structure_free_choice_Task) e wait for Unity to setup and open the project.

3. Set up all the relevant parameters (e.g. length of reward) and options (e.g. target dispositions) from the Unity Editor.

4. You're ready, press the start icon and do some science.


To propagate the latest changes of a given task project to this super repository:

```bash
# Navigate to the sub-repository related to modified task
cd vr-navigation-tasks/modified-task-repo

# Pull the latest changes from the remote repository
git pull origin main

# Navigate back to the super repository directory
cd ..

# Stage the updated sub-repository
git add modified-task-repo

# Commit the changes
git commit -m "Updated sub-repo"

# Push the changes to the remote super repository
git push origin main

```

## Documentation

Since each task shares a standard structure, a general [documentation](https://github.com/fattorilab/vr-navigation-tasks/tree/main/Docs) is provided for the C# scripts managing and supporting data saving, video recording and interfacing with the experimental setup (e.g. arduino, Pupil Lab).
