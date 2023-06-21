# Google Calendar RAT (SyncRAT) - Proof of Concept

## Disclaimer
**WARNING: This tool is for educational purposes only. Misusing this tool can have legal consequences. Do not use it without proper authorization or for malicious purposes. The author is not responsible for any damage caused by the misuse of this tool. Use at your own risk.**

## Description
The Google Calendar RAT (SyncRAT) is a proof-of-concept implementation of a command and control (C2) framework that leverages Google Calendar as the communication channel between the attacker and the target. It demonstrates how an attacker can use Google Calendar events to remotely control a compromised system.

The tool works by creating and monitoring events in a specified Google Calendar. The attacker can schedule events with specific summaries and descriptions, where the description contains the commands to execute on the target system. The target system periodically checks for new events in the calendar, executes the commands, and updates the event description with the output. The attacker can then retrieve the output by monitoring the modified events in the calendar.

![image](https://github.com/Malwareman007/SyncRAT/assets/86009160/c53071b0-7ff6-4e8d-a1b6-4e551201604d)


## Usage
1. Install the required dependencies:
   - `google-api-python-client` library
   - `oauth2client` library
   - `google-auth` library
2. Obtain Google API credentials:
   - Create a new project on the Google Cloud Console (https://console.cloud.google.com/).
   - Enable the Google Calendar API for the project.
   - Create service account credentials and download the JSON file.

3. Set up the tool:
   - Replace the placeholder `PUT_YOUR_CALENDAR_ADDRESS_HERE` with your Google Calendar address.
   - Set the desired `pollingTime` (in seconds) for the target system to check for new events.

4. Run the tool:
   - Execute the Python script: `python google_calendar_rat.py`
   - The tool will generate a unique identifier (ID) based on the system's hostname and MAC address.
   - It will log in to Google using the service account credentials.
   - The target system will continuously check the specified Google Calendar for new events.
   - When a new event with the matching ID is found, the target system executes the command specified in the event's description.
   - The output of the command is encoded and updated in the event's description.
   - The attacker can monitor the modified events in the calendar to retrieve the command outputs.

## How it works
SyncRAT attempt to connect to a valid shared Google Calendar link and after generating a unique ID check for any yet-to-be-executed commands.
If it is not able to find any command, it creates a new one (fixed to "whoami") as a proof of connection.
Every event is composed by two part:
1. The Title, which contains the unique ID, it means you can schedule multiple commands creating events having the same unique ID as name
2. The Description, which contains the command to execute and the base64 encoded output using the pipe symbol as separator "|"



## What a SOC analyst/Blue Teamer will see?
Focusing specifically on the network aspect, the only connections established will be to Google's servers, making the connection appear completely legitimate.

## How to use it
- Setup a Google service account and obtain the credentials.json file, place the file in the same directory of the script
- Create a new Google calendar and share it with the new created service account
- Edit the script to point your calendar address
- Once executed on the target machine an event with a unique target ID is automatically created autoexecuting the "whoami" command
- Use the following syntax in the event description for the communication =>   CLEAR_COMMAND|BASE64_OUTPUT
  ### Examples:
  - "whoami|"
  - "net users|"
