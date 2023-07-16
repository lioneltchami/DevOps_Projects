## AUX PROJECT 1: SHELL SCRIPTING
Write a shell script to onboarding 20 users. The script must meet the following conditions:

- Create the project folder called Shell

- Create a csv file named names.csv inside the Shell directory

- The script you created should read the CSV file, create each user on the server, and add to an existing group called developers (You will need to manually create this group ahead).

- Ensure that your script will first check for the existence of the user on the system, before it will attempt to create that it.

- Ensure that the user that is being created also has a default home folder

- Ensure that each user has a .ssh folder within its HOME folder. If it does not exist, then create it.

- For each user’s SSH configuration, create an authorized_keys file and add ensxure it has the public key of your current user.



The Script implementation is shown on the screenshot below:

![Screenshot from 2022-10-04 12-01-43](https://user-images.githubusercontent.com/23356682/193810541-2989f473-614c-44db-a6cc-f0f0be657d76.png)

![Screenshot from 2022-10-04 12-01-57](https://user-images.githubusercontent.com/23356682/193810813-decc4a1c-3d41-4376-b3cc-b0df1658683d.png)

The names in the CSV file are listed as follows

![Screenshot from 2022-10-04 12-45-51](https://user-images.githubusercontent.com/23356682/193811621-d5475de7-1e0e-4352-b479-e9863d0d22a5.png)

The output of the onboarding is given as the following

![Screenshot from 2022-10-04 12-09-21](https://user-images.githubusercontent.com/23356682/193812847-4e8da649-937c-4e78-8de7-d172114b753b.png)



