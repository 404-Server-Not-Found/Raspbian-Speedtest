
Raspbian Power Monitor
======================

Raspberry Pi Speedtest logger Setup Tutorial
----------------------------------------------------------------

This tutorial orignally comes from "scottvlaminck", but weve made some inproments to his python script so that its logged to Google Sheets correctly

In this tutorial we will use a Raspberry Pi with **"speedtest-cli"** installed to run a speedtest every hour and log it to a Google Sheet so first, we need to install the nessessary packages.

```shell
Sudo apt-get install python-pip
Sudo pip install speedtest-cli
```
Then we should be able to see an output by running

```shell
speedtest-cli 
```

Now we need to install an extra paskage that fixes the output of **"speedtest-cli"** so that it looks right in Google Sheets

```shell
Sudo apt-get update
Sudo apt-get install git
Sudo git clone https://github.com/HenrikBengtsson/speedtest-cli-extras.git
```

Then we should be able to test the new output

```shell
/home/pi/speedtest-cli-extras/bin/speedtest-csv
```

Now we can set the Pi up to store the results for Google Sheets

```shell
Sudo git clone https://github.com/google/gdata-python-client.git
cd gdata-python-client; sudo python ./setup.py install
cd
```

Then we will clone this project and change into it's directory

```shell
git clone https://github.com/Pretzel-Computers/Raspbian-Speedtest
cd raspberrypi-speedtest
```

### Now we can configure out Google Sheet and Oauth2 Tokens 

* Create a google spreadsheet via https://docs.google.com/spreadsheets/u/0/ 
	* With the following headers in the first row:

> `ConnectionType	Start Date	Stop Date	Provider	IP	Speedtest Server	Distance	Pingtime	Download Speed	Upload Speed	resultimg`

Now get the ID (which you can get from the url, e.g.: https://docs.google.com/spreadsheets/d/**SPREADSHEET-ID**/edit#gid=0)
Now create a gdocs app with oauth creds via https://console.developers.google.com/project and for that app: 
	* Create an OAuth 2.0 client ID
	* Enable Google Drive API
	
Now go back to the PI and rename gsheet.cfg to gsheet_add.cfg by running
```shell
mv gsheet.cfg gsheet_add.cfg
```
Then add your Oauth2 Client and Secret
```shell
Sudo nano gsheet_add.cfg
```

Now get an oauth token
```shell
Sudo python get_auth_token.py
```
Then update gsheet_add.cfg again with the Oauth and Refresh tokens
```shell
Sudo nano gsheet_add.gfc
```

Now test it and check your Google Sheet
```shell
run.sh
```

Now we want to run all this at the top of every hour we do this by adding it to Cron

```shell
crontab -e`
```

then at the bottom of the file add

```shell
0 * * * * /home/pi/raspberrypi-speedtest/run.sh
```

## Thats it! Now you can see your speed every hour, and if you want you can set jp your Google sheet to mark speed that are below your liking automatically by using Conditional Formatting: https://support.google.com/docs/answer/78413?co=GENIE.Platform%3DDesktop&hl=en
