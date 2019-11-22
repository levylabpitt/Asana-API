# Asana API

This is a set of VIs designed to allow anyone to interact with Asana programmatically.

If there is functionality you would like to see, please send me an email at aaron.greenberg@levylab.org or find me in person. Alternatively, create a task in [This Project](https://app.asana.com/0/1127430537451907/list)

## Asana Authentication.vi

This VI is required for every other VI to function. Alternatively, you can choose to use your own method of handling OAuth. 

The inputs for this VI are a Refresh Token In, Info In, and Error In. Refresh Token in is optional but recommended, and allows for repeated authorization when used in a state machine or the like. 

Info in is gained by [registering an application on Asana](https://asana.com/developers/documentation/getting-started/auth). The important parts you'll need come from the "Register an Application" header, and you will need to put in the Client ID and Client Secret that Asana gives you, as well as the redirect URI that *you choose*. **I recommend a blank page, such as [this](https://app.asana.com/api/1.0), or otherwise a simple page like Google's homepage. Localhost pages can also work.**

The outputs for this VI are a Timeout, the Refresh Token, the Authorization Array, and Error Out.

That Authorization Array MUST be wired into every other VI in the Asana API in order for it to function. 

The refresh token is not needed, but can be used in a state machine or while loop to pass into the VI and avoid the need to do the manual step again. 

The timeout is how long your Authentication array is valid for, in Asana's case, an hour. *I recommend using a case structure to check if your current time is greater than the timeout, and if it is, reauthenticate. If it is not, I would not authenticate again.*

If anyone knows how to use TCP listeners to remove the manual step from this VI, please reach out to me at aaron.greenberg@levylab.org (or find me in person)

## Create Project.vi

This VI creates a project in Asana.

The inputs are Name, Description, Authorization, and Error In. 

The Name is what you want your project named, and the description is what you want the description to be. Authorization is handled by Asana Authentication.VI, see above. Name and Authorization are mandatory. Description is not.

The outputs are the project GID, the response body, and Error Out.

The project GID is Asana's globally unique reference to your new project, and it will not change.

The response body is a large block of JSON encoded text, telling you your GID and other information. It also has more information about error codes, in the case that you find yourself with errors. 

Most likely, this will not be helpful to you. 

## Create Task.vi

This creates a Task. It functions identically to Create Project.vi, however it makes a task.

The inputs are Name, Description, Authorization, Project GID, and Error In.

The name and description write the name and description for your new task. Name is mandatory, description is not. Authorization is mandatory, see Asana Authentication.vi for more information.

The Project GID in is the project you want to write it to. This is also mandatory. 

The outputs are the Task GID, Response Body, and Error Out. Task GID is Asana's globally unique reference to your new Task, and it does not change.

Response Body is the same as with Create Project.vi, see above for information.

## Get Project Members.vi

This returns every member of a project. In Asana, these are the people who are accessible at the top of a project.

The inputs are Project GID, Authorization, and Error in. 

Project GID is Asana's globally unique reference to the project you want information on. It is mandatory. 

Authentication is mandatory. See Asana Authentication.vi for more information.

The outputs are an array of members and Error Out.

This array of members contains each membership GID, the resource_type, and a cluster of information on each user, containing their GID, resource_type, and name.

## Get Teams

This VI will return a list of all teams in the levylab.org workspace. It is hard coded to be the levylab.org workspace. I can, however, modify it to allow other workspaces if anyone would like.

The input is Error In. The outputs are Error Out and an array of clusters, each one having a team's GID, name, and resource type.

## Get Teams by User

This will return a list of teams that a user is a member of. This works for any user in any workspace. 

The inputs are User GID and Error In. The outputs are Error Out and an array of clusters, each one having a team's GID, name, and resource type.

## Get Projects.vi

Given an Asana Team, this will return every project in that team. Note that Levylab's teams include "Labview," "Undergrads," "Researchers," etc. Those are the categories along the left side of the page. See [Asana's page on Teams](https://asana.com/guide/help/organizations/team-basics) for more information.

The inputs for this VI are Team GID, Authorization, and Error In.

The outputs are Error Out and an array of clusters, each containing a project's GID, name, and resource_type.

## Get Tasks.vi

Given a project, this will return all available tasks in said project. It is advisable to use Get Projects.vi to fetch projects and a combination of Search 1D Array and Index Array to get the project you would like, or you can hardcode it. 

The inputs are Project GID, Authorization, and Error In. 

The outputs are Error Out and an array of clusters, each containing a task's GID, name, and resource_type.

## Post Status Update.vi

This will post a status update to a project in the Progress tab.

The inputs are Project GID, color, title, text, Authorization, and Error In. The Project GID specifies which project you post to, the color (green, yellow, or red) changes the status of the update (On Track, At Risk, and Off Track, respectively), and the text field determines the body of the post.

The outputs are the GID of the status update, the response body, and Error Out.

## Help