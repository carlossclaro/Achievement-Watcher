A sexy achievement file parser with real-time notification.<br />
View all the achievements earned on your PC whether it's coming from Steam or a Steam emulator, and more.
To see the full list of what this app can import please see the **Compatibility** section.

<table >
<tr>
<td align="left"><img src="https://github.com/xan105/Achievement-Watcher/raw/master/screenshot/home.png" width="400px"></td>
<td align="left"><img src="https://github.com/xan105/Achievement-Watcher/raw/master/screenshot/ach_view.png" width="400px"></td>
</tr>
</table>

The original idea was that some steam emulator generate a text file where all the achievements you have unlocked are stored.
But they aren't very friendly to know which is which, here is an example :
```ini
[NEW_ACHIEVEMENT_1_1]
Achieved=1
CurProgress=0
MaxProgress=0
UnlockTime=0000000000
[SteamAchievements]
00000=NEW_ACHIEVEMENT_1_1
Count=1
```
So which achievement is NEW_ACHIEVEMENT_1_1 ? You'll have to ask the steam API or look online in a site like the steamdb to find out.
So let's just do that automagically :)

Live notification on achievement unlocking
==========================================

Not as sexy as a directX Overlay but it's the next best thing.<br />
Display a Windows toast notification when you unlock an achievement.<br />
**Please verify your notification and focus assistant settings for this to work properly**.<br />

<p align="center">
<img src="https://github.com/xan105/Achievement-Watcher/raw/master/screenshot/live.gif">
</p>

You can disable this feature in the settings section.<br />

Note that there might be a slight delay between the event and the display of the notification as running powershell and loading a remote img resource can take a few seconds in some cases.<br />

Game must be set to Window borderless for the notification to be rendered on top of it otherwise you'll just here the sound.<br />

🚑 Not seeing any notification ? Quick fix :
- try to set your game to Window borderless.
- try to disable the automatic game and fullscreen rule in focus assistant
- try to set checkIfProcessIsRunning to false in `%appdata%/Achievement Watcher/options.ini`

Oh and make sure `watchdog.exe` is running.<br />
<br />
Not all games are supported, please see below.<br />
  
Compatibility :
================

|Emulator|Supported|Unlock Time|Ach Progress|Notification|
|--------|---------|-----------|------------|------------|
|Codex (Steam)| Yes | Yes | No | Yes |
|RLD! (Steam) | Yes | No | No | No |
|Skidrow (Steam) | Yes | No | No | No |
|ALI213 (Steam) | Via custom dir, Yes | Yes | No | Yes |
|Green Luma Reborn (Steam) | Yes | No | No | No |
|SmartSteamEmu (Steam)| Via plugin, Yes | Yes | No | Yes |
|Goldberg Steam Emu (Steam)| Via a custom build, Yes | Yes | No | Yes |
|Legit Steam Client (Steam) | Your Steam profile must be public, Yes | Yes | No | Steam overlay does it already | 
|RPCS3 (PS3) | Via custom dir, Yes | No | N/A | RPCS3 does it already|  
|LumaPlay (Uplay) | Yes | No | No | No |


### Steam Emulator
By default the following locations will be scanned for the files steam emulators generate :
```
- %PUBLIC%\Documents\Steam\CODEX
- %appdata%\Steam\CODEX
- %ProgramData%\Steam\*\
- %localappdata%\SKIDROW
- %appdata%\SmartSteamEmu
- %appdata%\Goldberg SteamEmu Saves
```

You can add your own folder in the app, just make sure that you select a folder which contains appid folder(s) :<br/>
 ```
 |___ Custom dir
      |___ 480 
      |___ 220 
 ```
For ALI213 there is no default folder so choose the dir where the `AlI213.ini` or `valve.ini` file is; <br>
The app will then parse it and look for `\Profile\[EMUUSERNAME]\Stats\achievements.bin` from the chosen location.
 
⚠️ Green Luma Reborn: Parse ach. only if the reg key "SkipStatsAndAchievements" is set to dword:00000000 for that APPID.

### Legit Steam
You can choose to view none (default) / only installed / all owned Steam games.<br/>
Ach. are updated based on files timestamp in `STEAM\appcache\stats`<br/>

⚠️ This feature requires that your Steam Profile is set to `Public`.<br/>

<p align="center">
<img src="https://github.com/xan105/Achievement-Watcher/raw/master/screenshot/steam_privacy.png">
</p>

Due to the server rate limit if you 've a huge Steam library it might not get all your games in one go.<br/>
If you are using your own steam web api key (see section below), this doesn't concern you.

### RPCS3 Playstation 3 Emulator
Please add a folder in the app where `rpcs3.exe` is located. The app will then look for ~~achievement~~ trophies for every game and every ps3 user.<br/>
Note that `TROPCONF.SFM` is language specific; So for PS3 games, trophies will be in the language you are playing with.<br/>
As of this writing there is no unlock time : the trophies unlocked in a PS3 that has never been connected online doesn't contains timestamps.

### LumaPlay
Since there is no public API to get a Uplay game achievements info as of this writing there are limitations: <br/>
Uplay client must be installed in order to try to get the game's info from its cache.<br/> 
To have the game info in the Uplay client cache you **don't** need to install the game but you need to at least have seen once the achievement listing page of the game in the Uplay client.<br/>
This app will keep and send the data to a remote server to build its own cache, when the server has the game info Uplay client is no longer required as the app will fetch the data from said server. <br/>
Therefore with time only newest game would require Uplay client in theory. <br/>

Options
=======
Options are stored in ```%AppData%\Achievement Watcher\cfg\options.ini``` but most of them are configurable via the GUI<br />

[achievement]
- lang<br />
  default to user locale<br />
  Both UI and data from Steam<br />
- showHidden<br />
  default to false<br />
  Wether or not show hidden achievements if any<br />
- mergeDuplicate<br />
  default to true<br />
  Try to merge multiple achievement files with the same steam appid<br />
- hideZero<br />
  default to false<br />
  Hide 0% Game<br />
- notification<br />
  default to true<br />
  Display or not a Windows toast notification on achievement unlocking. <br />
  (`AchievementWatcher.exe` doesn't need to be running for this, but `watchdog.exe` does).<br />
- legitSteam<br />
  default to 0<br />
  Steam games : (0) none / (1) installed / (2) owned.<br />
- souvenir<br />
  default to true
  Take a screenshot when you unlock an achievement (In "your pictures folder/game name/game name - ach name.png")
  
[notifier]
- timeTreshold<br />
  default to 5 (sec)<br />
  When an achievement file is modified; Amount of sec `watchdog.exe` will consider the most recent achieved achievement (from its timestamp value) to be new.<br />

- checkIfProcessIsRunning<br />
  default to true<br />
  When an achievement file is modified; Wether to check or not if the corresponding game is running and responding.<br />
  <br />
  Both options are mainly there to mitigate false positive.
  
- tick<br />
  default to 600 (ms)
  Ignore file modification within specified timeframe to prevent spam of notification when a game triggers multiple file write at the same time.
  Set it to 0 to disable this feature.

- appID<br />
  if not set, default to "Microsoft.XboxGamingOverlay_8wekyb3d8bbwe!App" if available <br />
  otherwise to "Microsoft.XboxApp_8wekyb3d8bbwe!Microsoft.XboxApp"<br />
  Notification appID ([Application User Model ID](https://docs.microsoft.com/fr-fr/windows/desktop/shell/appids)).<br />
  Example: 
  
  |Name| AppID |
  |----|-------|
  |Xbox Game Bar|Microsoft.XboxGamingOverlay_8wekyb3d8bbwe!App |
  |Xbox App| Microsoft.XboxApp_8wekyb3d8bbwe!Microsoft.XboxApp |
  
  ⚠️ You need to use a UWP AppID otherwise you won't be able to remotely load ach. img.
  
Steam Web API Key
=================
Some use of the Steam Web API to fetch data from Steam requires the use of an API Key.<br />
If you leave the field blank in the settings section, it will automagically fetch said data.<br />

<p align="center">
<img src="https://github.com/xan105/Achievement-Watcher/raw/master/screenshot/settings.png" width="600px">
</p>

An example of a server that feeds you the data is provided within this repo.<br />
This service is not guarantee over time and is solely provided for your own convenience.<br />
If you experience any issues please use your own Steam Web API key.<br />              
                
You can acquire one [by filling out this form](https://steamcommunity.com/dev/apikey).<br />
Use of the APIs also requires that you agree to the [Steam API Terms of Use](https://steamcommunity.com/dev/apiterms).<br />

Command Line Args | URI Scheme
==============================

Args:<br />
`--appid xxx [--name yyy]`<br />

URI:<br />
`ach:--appid xxx [--name yyy]`<br />

xxx is a steam appid<br />
yyy is an optional steam ach id name<br />

After the loading directly display the specified game.<br />
And if specified highlight an achievement.
                    
Windows compatibility
=====================
Windows x64 only.<br />
Windows 10 >= 1809 is recommended.<br />

It should work starting with Windows 8 and above but keep in mind that this was mostly tested with Windows 10.<br />

Translation Help
================

I do my best to translate everything for every supported language by Steam, but it's rather difficult and I don't speak that much languages.
Fluent in another language ? Any help to add/modify/improve would be greatly appreciated.

More details [here](https://github.com/xan105/achievement-watcher/tree/master/app/locale)

Auto-Update
===========

This software auto update itself via Windows scheduled tasks.
There are .cmd files in the root directory to create, delete and manually run the tasks.

File cache & Logs
=================
in ```%AppData%\Achievement Watcher```

Legal
=====
Software provided here is to be use at your own risk. This is provided as is without any express or implied warranty.<br />
In no event or circumstances will the authors or company be held liable for any damage to yourself or your computer that may arise from the installation or use of the free software aswell as his documentation that is provided on this website.<br />
And for anything that may occur as a result of your use, or inability to use the materials provided via this website.<br />

Software provided here is purely for informational purposes and does not provide nor encourage illegal access to copyrighted material.<br />

Software provided here is not affiliated nor associated with Steam, © Valve Corporation and data from its API is provided as is without any express or implied warranty.<br />

Software provided here is not affiliated nor associated with Uplay, © Ubisoft and data from its API is provided as is without any express or implied warranty.<br />

Software provided here is not affiliated nor associated with any cracking scene groups.<br />

Other trademarks, copyright are the property of their respective owners. No copyright or trademark infringement is intended by using third-party resources. Except where otherwise specified, the contents of this project is subject to copyright.<br />
