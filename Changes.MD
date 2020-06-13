Here is the list of changes that I have to keep an idea of what has changed over time.

* 6/13/2020   - With the new changes to rclone, I've tested async and found no issues so I've removed that from mergerfs and rclone setup and let async run
* 6/5/2020    - Removed GD-Find and added a post exec action that runs async and returns immediately to warm up the directory cache
* 12/5/2019   - Removed attr-timeout since it caused some problems with nested files and caching
* 10/22/2019  - Initial change log to keep a running tally. Added attr-timeout 1000h to keep attributes cached longer.
