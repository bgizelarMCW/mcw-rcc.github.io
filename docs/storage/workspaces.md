# Scratch Workspaces

Scratch storage is organized into workspaces. A workspace is a folder with an expiration date. You create one for each job or analysis, work in it, and release it when you are done. RCC cleans up expired workspaces automatically, which keeps scratch available for everyone.

Your lab's scratch space is `/scratch/g/pi_netid`, where `pi_netid` is your PI's MCW username. You do not save files directly in that folder. You create a workspace inside it and work there.

## Quick Reference

| Task                     | Command                        | If you are in more than one lab                |
|--------------------------|--------------------------------|------------------------------------------------|
| Create a workspace       | `ws_allocate myproject`        | `ws_allocate -F lab_pi_netid myproject`        |
| List your workspaces     | `ws_list`                      | `ws_list -F lab_pi_netid`                      |
| Extend a workspace       | `ws_extend myproject 60`       | `ws_extend -F lab_pi_netid myproject 60`       |
| Release a workspace      | `ws_release myproject`         | `ws_release -F lab_pi_netid myproject`         |
| See what can be restored | `ws_restore -l`                | `ws_restore -l` (shows all your labs)          |
| Restore a workspace      | `ws_restore old_name new_name` | Same command. Create the new one with `-F`     |

## Which Command Do I Use?

- **You belong to one lab:** use the plain commands in the middle column.
- **You belong to more than one lab:** add `-F lab_pi_netid` to say which lab you mean. For example, if your PI's username is `jsmith`, use `-F lab_jsmith`.
- With `ws_allocate`, the lab is required. If you leave it out, the command lists your labs and shows an example command with `-F` that you can copy.
- With the other commands, it is the safe choice when you are unsure.
- Not sure which labs you belong to? Run `ws_list -l`. It shows your labs as `lab_pi_netid`. Ignore the `lab_donotuse` entry. It is not a lab.

## How Workspaces Work

- A workspace lasts 60 days by default. 60 days is also the maximum.
- You can extend each workspace once, for up to 60 days.
- An extension counts from the day you run it, not from the old expiration date. Extending early gives you less total time, so wait until you need it.
- Asking for fewer days than the workspace has left shortens it. A shorter request does not use up your extension.
- Your lab group can read and write your workspace by default.
- Expiration reminder emails are off by default. You must turn them on when you create the workspace. See [Get an Expiration Reminder](#get-an-expiration-reminder).
- After a workspace expires or is released, there is a 14-day grace period. The data is kept, and you can restore it yourself. After the grace period it is permanently deleted.
- During the grace period the data still counts against your lab's scratch quota.

!!! warning "Scratch is not long-term storage"
    Workspaces are for jobs. Copy results you want to keep to your [group storage](rcc-storage.md#group) before the workspace expires. Group storage is protected by snapshots and replication. Scratch is not.

## Create a Workspace

Run `ws_allocate` with a name for the workspace:

```bash
ws_allocate myproject
```

If you belong to more than one lab, add your lab:

```bash
ws_allocate -F lab_pi_netid myproject
```

It prints the path of your new workspace, for example `/scratch/g/pi_netid/netid-myproject`. It also shows the extensions you have left (1) and the days remaining (60).

The workspace folder is named `netid-myproject`, where `netid` is your MCW username. Use this path in your job scripts.

To choose a shorter lifetime, add the number of days after the name:

```bash
ws_allocate myproject 30
```

If you ask for more than 60 days, you get a warning and the workspace is created with 60 days.

To keep a workspace private (owner only) instead of shared with your lab, add `--private`:

```bash
ws_allocate --private myproject
```

### Get an Expiration Reminder

RCC does not send expiration reminders by default. To get an email before your workspace expires, add `-r` (days before expiration) and `-m` (your email address) when you create it:

```bash
ws_allocate -r 7 -m username@mcw.edu myproject
```

This example sends a reminder 7 days before the workspace expires.

!!! warning "You cannot add a reminder later without using your extension"
    Reminders can only be set when you create a workspace, or when you extend it. Adding or changing a reminder on an existing workspace counts as your one extension. If you want to keep your extension, set the reminder when you create the workspace.

## List Your Workspaces

Run `ws_list`:

```bash
ws_list
```

Example output:

```text
Id: netid-myproject
    workspace directory  : /scratch/g/pi_netid/netid-myproject
    remaining time       : 29 days, 5 hours
    creation time        : Mon Nov 23 20:40:48 2026
    expiration time      : Wed Dec 23 20:40:48 2026
    filesystem name      : lab_pi_netid
    available extensions : 1
```

| Field                               | Meaning                                    |
|-------------------------------------|--------------------------------------------|
| `Id`                                | Your username and the workspace name       |
| `workspace directory`               | The path to use in your jobs               |
| `remaining time`                    | Time left before the workspace expires     |
| `creation time` / `expiration time` | When it was created and when it expires    |
| `filesystem name`                   | Your lab, shown as `lab_pi_netid`          |
| `available extensions`              | How many extensions you have left (1 or 0) |

In more than one lab? To show one lab only, add `-F lab_pi_netid`.

To see released workspaces that can still be restored, and how long they will be kept, run `ws_list --expired`.

!!! note "Id versus name"
    `ws_list` shows an Id that begins with your username, such as `netid-myproject`. When you extend or release a workspace, use the name you chose when you created it, which is `myproject`. Do not paste the Id into those commands.

## Extend a Workspace

Each workspace can be extended once. Give the workspace name and the number of days, up to 60:

```bash
ws_extend myproject 60
```

In more than one lab? Add your lab:

```bash
ws_extend -F lab_pi_netid myproject 60
```

- The new expiration date is the number of days you ask for, counted from today.
- Extending early gives you less total time, so wait until you are close to the expiration date.
- If you ask for fewer days than remain, the workspace is shortened and your extension is not used.
- A request for more than 60 days is reduced to 60, with a warning.
- To add or change a reminder while extending, use `ws_allocate -x -r 7 -m username@mcw.edu myproject 60`. This uses your extension.
- Check `available extensions` in `ws_list` to see if you have an extension left. When it shows 0, the workspace cannot be extended again.

## Release a Workspace

When you are finished with a workspace, release it:

```bash
ws_release myproject
```

In more than one lab? Add your lab:

```bash
ws_release -F lab_pi_netid myproject
```

The data is kept for a 14-day grace period in case you need it back, then it is permanently deleted. The 14 days start when you release it, so releasing early deletes the data sooner than letting the workspace expire on its own.

During the grace period the data still counts against your lab's scratch quota.

!!! danger "Need the space back right now?"
    Add `--delete-data` to release the workspace and **permanently delete its data immediately**. There is no grace period and no restore. You get 5 seconds to press Ctrl-C to cancel.

    ```bash
    ws_release --delete-data myproject
    ```

## Restore a Workspace

If you released a workspace by mistake, or one expired and you still need the data, you can restore it yourself during the 14-day grace period. After that the data is permanently deleted, so restore it as soon as you can.

Restoring puts the old data into a workspace that already exists, so you create a new one first.

1. List the workspaces you can restore:

    ```bash
    ws_restore -l
    ```

    Example entry:

    ```text
    netid-myproject-1795570887
            unavailable since : Tue Nov 24 19:41:27 2026
            restorable until  : Tue Dec  8 19:41:27 2026 (13 days, 23 hours)
            in filesystem     : lab_pi_netid
    ```

    The first line is the name to use. It is your workspace name with a number added. `restorable until` is the end of the grace period. If the list is long, narrow it down:

    ```bash
    ws_restore -l | grep myproject
    ```

2. Create a new workspace to receive the data. If you belong to more than one lab, add `-F lab_pi_netid`:

    ```bash
    ws_allocate restored
    ```

3. Restore the old workspace into it. Replace the first name with the one from step 1:

    ```bash
    ws_restore netid-myproject-1795570887 restored
    ```

    You are asked a quick question, such as whether a strawberry is an animal, a fruit or vegetable, an object or a person. Type the number of the right answer and press Enter. When it finishes, you see `restore successful`.

4. Your files are in a folder inside the new workspace. The folder has the old name:

    ```text
    /scratch/g/pi_netid/netid-restored/netid-myproject-1795570887
    ```

    Copy or move the files to where you want them.

    Restoring moves the data. The old name no longer appears in `ws_restore -l`.

!!! tip "If the restore did not finish"
    If the new workspace is empty and the old name is still listed by `ws_restore -l`, run the restore command again. Answer the question when it asks.

!!! note "Use the name from ws_restore -l"
    When a workspace is released or expires, its name changes. Copy the name exactly as `ws_restore -l` shows it. Do not use the name from `ws_list`.

## Sharing a Workspace

Your lab group can already read and write your workspace, unless you created it with `--private`.

Sharing with people outside your lab is not available yet. The `ws_share` command exists, but it does not work with RCC storage, so do not use it. If you need to share data with someone in another lab, contact <help-rcc@mcw.edu>.

## Using Workspaces in Jobs

Create the workspace before you submit a job, then use its path in your script, for example:

```bash
WS=/scratch/g/pi_netid/netid-myproject
cd $WS
```

See [Using Data in a Job](../cluster/jobs/storage-job.md) for more.

## Troubleshooting

| Message or problem                                        | What to do                                                                                                                                                              |
|-----------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Non-existent workspace given                              | Use the name you chose when you created it (`myproject`), not the Id from `ws_list` (`netid-myproject`).                                                                |
| "not currently a member of lab ... in this login session" | You were added to the lab recently. Log out and log back in, then try again.                                                                                            |
| `ws_allocate` lists your labs and asks you to try again   | You belong to more than one lab. Add `-F lab_pi_netid`.                                                                                                                 |
| `available extensions : 0`                                | The workspace was already extended and cannot be extended again. Copy your data to group storage, or create a new workspace.                                            |
| Extension did not give you more time                      | An extension counts from today. If you asked for fewer days than remained, the workspace was shortened.                                                                 |
| You did not get an expiration email                       | Reminders are off by default. Set `-r` and `-m` when you create the workspace. See [Get an Expiration Reminder](#get-an-expiration-reminder).                           |
| Cannot create files in `/scratch/g/pi_netid`              | Create a workspace with `ws_allocate`, and save files there.                                                                                                            |
| A workspace expired or was released and you need the data | Restore it within 14 days. See [Restore a Workspace](#restore-a-workspace).                                                                                             |
| `ws_restore` does not work                                | Create the new workspace first. Then copy the old name exactly as `ws_restore -l` shows it. Do not use the name from `ws_list`. Answer the quick question that appears. |
| You want to share a workspace with another lab            | Contact <help-rcc@mcw.edu>. `ws_share` is not available yet.                                                                                                            |

--8<-- "includes/abbreviations.md"
