# Workshop Exercise - Role-based access control

## Table Contents

* [Objective](#objective)
* [Guide](#guide)
* [Ansible Tower Users](#ansible-tower-users)
* [Ansible Tower Teams](#ansible-tower-teams)
* [Granting Permissions](#granting-permissions)
* [Test Permissions](#test-permissions)

# Objective

You have already learned how Ansible Tower separates credentials from users. Another advantage of Ansible Tower is the user and group rights management.  This exercise demonstrates Role Based Access Control (RBAC)

# Guide

## Ansible Tower Users

There are three types of Tower Users:

- **Normal User**: Have read and write access limited to the inventory and projects for which that user has been granted the appropriate roles and privileges.

- **System Auditor**: Auditors implicitly inherit the read-only capability for all objects within the Tower environment.

- **System Administrator**: Has admin, read, and write privileges over the entire Tower installation.

Let’s create a user:

- In the Tower menu under **ACCESS** click **Users**

- Click the green plus button

- Fill in the values for the new user:

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td>FIRST NAME </td>
    <td>Werner</td>
  </tr>
  <tr>
    <td>LAST NAME</td>
    <td>Web</td>
  </tr>
  <tr>
    <td>Organization</td>
    <td>Default</td>
  </tr>         
  <tr>
    <td>EMAIL</td>
    <td>wweb@example.com</td>
  </tr>
  <tr>
    <td>USERNAME</td>
    <td>wweb</td>
  </tr>  
  <tr>
    <td>PASSWORD</td>
    <td>ansible</td>
  </tr>
  <tr>
    <td>CONFIRM PASSWORD</td>
    <td>ansible</td>
  </tr>
  <tr>
    <td>USER TYPE</td>
    <td>Normal User</td>
  </tr>                           
</table>



    - Confirm password

- Click **SAVE**

## Ansible Tower Teams

Teams provide a means to implement role-based access control schemes and delegate responsibilities across organizations. For instance, permissions may be granted to a whole Team rather than each user on the Team.

Create a Team:

- In the menu go to **ACCESS → Teams**

- Click the green plus button and create a team named `Web Content`.

- Click **SAVE**

Now you can add a user to the Team:

- Switch to the Users view of the `Web Content` Team by clicking the **USERS** button.

- Click the green plus button, tick the box next to the `wweb` user and click **SAVE**.

Now click the **PERMISSIONS** button in the **TEAMS** view, you will be greeted with "No Permissions Have Been Granted".

## Granting Permissions

To allow users or teams to actually do something, you have to set permissions. The Team `Web Content` should only be allowed to modify content of the assigned webservers.

Add the permission to use the template:

- In the Permissions view of the Team `Web Content` click the green plus button to add permissions.

- A new window opens. You can choose to set permissions for a number of resources.

    - Select the resource type **JOB TEMPLATES**

    - Choose the `Create index.html` Template by ticking the box next to it.

- The second part of the window opens, here you assign roles to the selected resource.

    - Choose **EXECUTE**

- Click **SAVE**

## Test Permissions

Now log out of Tower’s web UI and in again as the **wweb** user.

- Go to the **Templates** view, you should notice for wweb only the `Create index.html` template is listed. He is allowed to view and launch, but not to edit the Template. Just open the template and try to change it.

- Run the Job Template by clicking the rocket icon. Enter the survey content to your liking and launch the job.

- In the following **Jobs** view have a good look around, note that there where changes to the host (of course…​).


----
**Navigation**
<br>
[Previous Exercise](../2.4-surveys) - [Next Exercise](../2.6-workflows)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-2---ansible-tower-exercises)
