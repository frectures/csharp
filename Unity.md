# Unity

## Installation

- Download the Unity Hub (100 MB) from https://unity.com/download
- Start Unity Hub
  - New to Unity? Click `Create account` first
- Click `Sign in`
- Click `Install Unity Editor`
  - 2020.3.33f1 LTS (5 GB) at the time of writing
- Click `Agree and get personal edition license`

## Hello World

- Click `Projects` on the left
- Click `New project` on the right
- Click `Core` on the left
- Click `3D` in the middle
- Change `Project settings` on the right:
  - `Project name`: `Hello World`
  - `Location`: Your git folder
- Click `Create project` on the right
- Copy/paste https://raw.githubusercontent.com/github/gitignore/main/Unity.gitignore into a new file named `.gitignore` in your project folder `Hello World`

## Configure Editor

- Right-click into `Assets` in the bottom
- Click `Create` > `C# Script`
- Double-click the newly created script
- Visual Studio Code opens
  - Provided you installed Visual Studio Code for the git tutorial last week
- Do you want to install the recommended extensions for C#? Click `Install`
  - Unless you don't care about Auto-Completion, Refactoring etc.
- The .NET Core SDK cannot be located. Click `Get the SDK`
- Click `All .NET Downloads`
- Click `.NET 4.8`
  - **.NET 5.0 and 6.0 are too new for Unity 2020**
- Click your platform
  - Probably Windows `x64`
- Start the downloaded installer
- C# support works better if you open the *project folder* `Hello World` in Visual Studio Code
  - The scripts are located in the `Assets` sub-folder

## Tutorials

- [Spiele Entwickeln mit Unity für Anfänger](https://www.youtube.com/watch?v=37SFWBllCaM&list=PLf_j4Db1v3RkEknk50NWuOVL96xv6if-7)
  - Von Kollegin empfohlen, die seit kurzem in einem Unity-Projekt arbeitet
- [Introduction to Game Development (Unity and C#)](https://www.youtube.com/watch?v=L9zlZjxmwXU&list=PLFt_AvWsXl0fnA91TcmkRyhhixX9CO3Lw)
  - Vor vielen Jahren selber geschaut
