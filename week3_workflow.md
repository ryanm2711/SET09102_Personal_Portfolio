# Workflow

### Task Board
I moved a task I selected from the backlog to **in progress**, then added a description to said task that would be understandable for other members of the team on what I hope to achieve by the end of the task.

In the following screenshot you can see the exact description used, as well as assigning myself to the task, and linking it to a pull request for the issue - which will automatically move into done once approved and merged.

![MAUI Project Issue 13 Task Board Description](https://github.com/ryanm2711/SET09102_Personal_Portfolio/blob/main/images/maui_issue_13_task_board_description.png?raw=true)

### Feature Branch

Then I created a feature branch based off of **develop** branch, named after the issue number - **#13**. This kept my work contained and any changes I made into that branch, would not affect others' work and potentially break their code if conflicts arised. 

### Commit History

Here is the commit history of the branch:

![MAUI Project Issue 13 Commit History](https://github.com/ryanm2711/SET09102_Personal_Portfolio/blob/main/images/maui_issue_13_commit_history.png?raw=true)

## Task workflow
This week I took on two tasks.

### First Task
The first task was setting up a database wrapper to be used within the application, following the microsoft documentation I setup a mysqlite connection that would work as a foundation that we can build upon in the later weeks to store data. I created two classes, one being **static** and another being a **singleton**. The **DatabaseSettings** static class acted as a config of sorts for the database, and the **Database** singleton class possessed all the functions to work with the mysqlite connection.

### Second Task
And secondly the task of implementing a way for the user to add, update, and remove alert types within the application. The **save button** worked with both adding new & updating existing alert types, all dependant on if a current item is selected. Any changes to the alerts will be stored in the mysqlite database, and on next app startup; loaded. The UI had to be intuitive and clear to the user on what to do, that included having clear names for the buttons and any other inputs. For the input box I added a hint that is displayed whenever no input has been given. 

[Here is the pull request for the issue and having it be merged into develop branch](https://github.com/Software-Engineering-Red/MAUI-APP/pull/23)

This is the result of the UI I came up with:

![MAUI Project Issue 13 Result](https://github.com/ryanm2711/SET09102_Personal_Portfolio/blob/main/images/maui_issue_13_result.png?raw=true)

Here, you should use screenshots and descriptive commentary to show that the required
have been completed successfully.

## Reflection

I had some difficulty finishing my task due to the confusion on how to implement it, I couldn't figure it out myself so I had to ask another team member on how he went about implementing his task. This helped speed things up tremendously and I was able to quickly come up with a solution for my task.

The current workflow in my opinion is perfect for the scope of the project and size of team, the DoD ruleset as well as the branch setup is clear and concise - and it avoids the headache of conflicts arising from tasks being done all in the same branch. Not only that, the task board works nicely as you can link it to issues; meaning that when the task is completed and merged it moves the issue to **completed** in task board automatically, this saves time and less opportunity for error as a developer.
