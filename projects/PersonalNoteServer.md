## Personal Note Server

## Goal
To create a simple service for me to record ideas and then be able to create mind maps via some LL, also for personal we get text messages from it reminding me
## What we will need
~- Web Server~ 
~- Simple UI for me to write down notes~
~- Submit button to save the Note~
~- Want to store notes in .MD format~
~- On the server it would be good to take the input, write to file, and then git push to this github~

- Cron Job that runs daily 
- Cpp script
    - subprocess to run bash script to pull github notes
    - claude or some llm to run in the terminal and have prompt
    - python script to email out summary

## Current State
- 12/14/2025
    - Webserver that starts but does nothing
    - HTML page that I can type in Markdown
    - TODOs
        - get server deployed on raspberry pi
        - figure out how to keep rp on all the time
        - need to add functionality for note saving
