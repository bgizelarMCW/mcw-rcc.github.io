# Scratch Workspaces

<!-- ROLLOUT NOTE: remove this admonition once every lab has moved to workspaces ("full prod"). -->
!!! info "Workspaces are rolling out lab by lab"
    RCC is moving labs to workspaces one at a time, and will contact your lab before its move. If your lab has not moved yet, nothing changes for you: keep using `/scratch/g/pi_netid` as before. Not sure? Run `ws_list -l`. If your lab is listed as `lab_pi_netid`, it has moved.

Scratch storage is organized into workspaces. A workspace is a folder with an expiration date. You create one for each job or analysis, work in it, and release it when you are done. RCC cleans up expired workspaces automatically, which keeps scratch available for everyone.

Your lab's scratch space is `/scratch/g/pi_netid`, where `pi_netid` is your PI's MCW username. You do not save files directly in that folder. You create a workspace inside it and work there.

## Workspace Dashboard in Open OnDemand

Prefer clicking to typing? The **Workspace Dashboard** does everything on this page in your web browser. It is the easiest way to manage your workspaces, and it runs the same `ws_*` commands for you, as you. A workspace you create in the dashboard shows up in `ws_list`, and one you create on the command line shows up in the dashboard.

[Open the Workspace Dashboard](https://ondemand.rcc.mcw.edu/pun/sys/workspace-dashboard){ .md-button .md-button--primary target="_blank" rel="noopener" }

To find it yourself, log in to [Open OnDemand](https://ondemand.rcc.mcw.edu){:target="_blank"} with your MCW username and password, then select **Workspaces > Workspace Dashboard** in the top menu, or click the **Workspace Dashboard** tile on the front page.

What you can do with it:

- **See everything at a glance.** Each workspace is a card with its path, expiry date, and a color badge for the days left. On the last day the badge counts down the hours.
- **Create a workspace** in one form: pick a name and the number of days, and choose whether your lab can read and write it.
- **Get reminder emails automatically.** Every workspace you create gets a reminder at your MCW email address every night during the last {{ ws_reminder_days }} days before it expires. There is nothing to tick.
- **Extend with confidence.** The **Extend** button shows when you can extend. If it is too early, it shows the date you can, so you never waste a try.
- **Share with people or groups** outside your lab, with read only or read and write access. See who has access, remove one person, or use **Remove everyone**, which shows the list of who will lose access before you confirm.
- **See your lab's workspaces** on the **Lab workspaces** tab, and what your lab can do in each of yours: read and write, read only, or no access.
- **Find what others shared with you** on the **Shared with me** tab.
- **Release a workspace** when you are done, and optionally delete its files right away to free up your lab's quota. The dashboard asks you to confirm first.
- **Restore a recently expired workspace** from the **Recently expired** tab, which shows the last day you can restore each one.
- **Open a workspace** in the OnDemand file browser or a terminal with one click.

!!! tip "Learn the command line as you go"
    As you fill in a form, the dashboard shows the matching command, such as `ws_allocate -F lab_jsmith -d 30 -n rnaseq`, and it updates as you type. Every action also shows its **Command line equivalent**. Copy these commands into your job scripts, or run them in a terminal next time.

For step by step instructions and screenshots, see the [Workspace Dashboard guide](workspace-dashboard.md).

## Quick Reference

| Task                     | Command                        | If you are in more than one lab            |
|--------------------------|--------------------------------|--------------------------------------------|
| Create a workspace       | `ws_allocate myproject`        | `ws_allocate -F lab_pi_netid myproject`    |
| List your workspaces     | `ws_list`                      | `ws_list -F lab_pi_netid`                  |
| Extend a workspace       | `ws_extend myproject 60`       | `ws_extend -F lab_pi_netid myproject 60`   |
| Release a workspace      | `ws_release myproject`         | `ws_release -F lab_pi_netid myproject`     |
| See what can be restored | `ws_restore -l`                | `ws_restore -l` (shows all your labs)      |
| Restore a workspace      | `ws_restore old_name new_name` | Same command. Create the new one with `-F` |

## Which Command Do I Use?

- **You belong to one lab:** use the plain commands in the middle column.
- **You belong to more than one lab:** add `-F lab_pi_netid` to say which lab you mean. For example, if your PI's username is `jsmith`, use `-F lab_jsmith`.
- With `ws_allocate`, the lab is required. If you leave it out, the command lists your labs and shows an example command with `-F` that you can copy.
- With the other commands, it is the safe choice when you are unsure.
- Not sure which labs you belong to? Run `ws_list -l`. It shows your labs as `lab_pi_netid`. Ignore the `lab_donotuse` entry. It is not a lab.

## How Workspaces Work

- A workspace lasts 60 days by default. 60 days is also the maximum.
- You can extend each workspace once, for up to 60 days.
- {{ ws_extend_rule }} See [When You Can Extend](#when-you-can-extend).
- An extension counts from the day you run it, not from the old expiration date.
- Asking for fewer days than the workspace has left shortens it. A shorter request does not use up your extension.
- Your lab group can read and write your workspace by default.
- {{ ws_reminder_rule }} See [Expiration Reminder Emails](#expiration-reminder-emails).
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

### Expiration Reminder Emails

{{ ws_reminder_rule }} The email goes to your MCW email address and comes from <help-rcc@mcw.edu>. Each email names the workspace and its expiration date. Once you extend a workspace, the emails stop until the last {{ ws_reminder_days }} days before its new expiration date.

You can change the defaults when you create the workspace:

- `-r` sets how many days before expiration the nightly emails start. For example, `ws_allocate -r 14 myproject` starts them 14 days before.
- `-m` sends them to another email address. For example, `ws_allocate -m name@mcw.edu myproject`.

!!! warning "Changing a reminder later uses your extension"
    `-r` and `-m` can only be set when you create a workspace, or when you extend it. Changing them on an existing workspace counts as your one extension. You do not need them to get reminders.

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
- If you ask for fewer days than remain, you are asked to confirm, and the workspace is shortened. Your extension is not used.
- If you ask for more than 60 days, you are asked whether to continue with 60. In a script or batch job there is no question, and 60 is used with a warning.
- To change when reminders start, or where they go, while extending, use `ws_allocate -x -r 14 -m name@mcw.edu myproject 60`. This uses your extension, so the [extension rule](#when-you-can-extend) applies.
- Check `available extensions` in `ws_list` to see if you have an extension left. When it shows 0, the workspace cannot be extended again.
- Anyone in your lab can extend a workspace that is shared with the lab, using the owner's username. For example, to extend `myproject` owned by `jdoe`: `ws_allocate -x -F lab_pi_netid -u jdoe myproject 60`. The same rules apply, and it uses the workspace's one extension.

### When You Can Extend

{{ ws_extend_rule }} The lifetime counts from when you created the workspace to its expiration date.

| Workspace lifetime | You can extend from |
|--------------------|---------------------|
| 60 days            | day 45              |
| 30 days            | day 23              |
| 20 days            | day 13              |
| 7 days or less     | right away          |

If you try earlier, nothing changes and your extension is not used. The message tells you the first date you can extend:

```text
error: workspace can not be extended yet, 75% of its current duration has to be elapsed first, or less than 7 days must remain.
error: you can extend it on or after Thu Jan  7 20:01:38 2027 (in 42 days).
error: workspace was not extended and no extension was used.
```

Run the same `ws_extend` command again on or after that date. The [Workspace Dashboard](workspace-dashboard.md) shows this date on each workspace's **Extend** button.

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

## Share a Workspace

Your lab group can already read and write your workspace, unless you created it with `--private`.

To give someone outside your lab access, use `ws_share`. Only the workspace owner can share it. If you belong to more than one lab, add `-F lab_pi_netid` as with the other commands.

| Task                                   | Command                                       |
|----------------------------------------|-----------------------------------------------|
| Share read-only with a user            | `ws_share share myproject jdoe`               |
| Share read and write with a user       | `ws_share --readwrite share myproject jdoe`   |
| Share with a group                     | `ws_share sharegroup myproject sg-otherlab`   |
| See who you shared with                | `ws_share list myproject`                     |
| Stop sharing with a user               | `ws_share unshare myproject jdoe`             |
| Stop sharing with a group              | `ws_share unsharegroup myproject sg-otherlab` |
| Stop sharing with everyone             | `ws_share unshare-all myproject`              |
| Check that sharing is set up correctly | `ws_share check myproject`                    |

`jdoe` is the other person's MCW username. Use the workspace name you chose (`myproject`), not the Id from `ws_list`.

- You can only share with people who have an [RCC account](../cluster/accounts.md). An MCW username alone is not enough.
- To share with everyone in another lab, share with that lab's group. It is `sg-` followed by the PI's MCW username, such as `sg-otherlab`.
- You can also share, see who has access, and remove access in the [Workspace Dashboard](workspace-dashboard.md#share-a-workspace).

- **Read-only** lets them open and copy your files and list your folders. **Read and write** also lets them create, change, and delete files.
- To change someone from read-only to read and write, or back, run `ws_share share` again with or without `--readwrite`. It replaces their old access.
- Files and folders you add later are shared automatically. Changing permissions with `chmod` does not remove the sharing.
- Send them the full path from `ws_list`, for example `/scratch/g/pi_netid/netid-myproject`. They can open that path, but they cannot list `/scratch/g/pi_netid` to find it.
- Sharing ends when the workspace expires or is released.
- You cannot share with a group that has more than 100 members. Sharing read and write with a group asks you to confirm first.

!!! note "Files written by others count against your lab's quota"
    Files that people you shared with create in your workspace are stored in your lab's scratch space and count against your lab's scratch quota. They own those files. If you stop sharing and see `Operation not permitted` for some files, those files belong to someone else. Contact <help-rcc@mcw.edu> if you need them changed.

## Using Workspaces in Jobs

Create the workspace on a login node before you submit a job, and copy your input data into it. `ws_find` prints the path of a workspace, so you do not need to type it:

```bash
ws_allocate myproject
cp -r /group/pi_netid/work/inputs "$(ws_find myproject)"/
```

In your job script, change to the workspace before the job's commands:

```bash
cd "$(ws_find -F lab_pi_netid myproject)"
```

Then submit the job from the login node as usual with `sbatch job.slurm`.

!!! warning "Your job must finish before the workspace expires"
    When your job uses a workspace, its `--time` limit must end before the workspace expires. If it does not, `sbatch` rejects the job and tells you how to fix it: extend the workspace, ask for less time, or move to a new workspace. If the workspace expires within 7 days, the job is accepted with a reminder. See [Job Time Limits and Workspace Expiration](../cluster/jobs/running-jobs.md#job-time-limits-and-workspace-expiration).

Tips for planning:

- Give a new workspace enough days for all the jobs you plan to run in it, including time waiting in the queue. The maximum, and the default, is 60 days.
- Before a long job, run `ws_list` and check `remaining time`.
- In job scripts, use `ws_find -F lab_pi_netid myproject` or the full `/scratch/g/...` path. The check can see those, but not a path built from a variable or `ws_find` without `-F`.

See [Using Data in a Job](../cluster/jobs/storage-job.md) for more.

## Troubleshooting

| Message or problem                                         | What to do                                                                                                                                                                                                                   |
|------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Non-existent workspace given                               | Use the name you chose when you created it (`myproject`), not the Id from `ws_list` (`netid-myproject`).                                                                                                                     |
| "not currently a member of lab ... in this login session"  | You were added to the lab recently. Log out and log back in, then try again.                                                                                                                                                 |
| `ws_allocate` lists your labs and asks you to try again    | You belong to more than one lab. Add `-F lab_pi_netid`.                                                                                                                                                                      |
| `available extensions : 0`                                 | The workspace was already extended and cannot be extended again. Copy your data to group storage, or create a new workspace.                                                                                                 |
| Extension did not give you more time                       | An extension counts from today. If you asked for fewer days than remained, the workspace was shortened.                                                                                                                      |
| ws_extend says the workspace can not be extended yet       | It is too early: less than {{ ws_extend_pct }}% of its lifetime has passed and more than {{ ws_extend_window }} days remain. Run the command again on or after the date it shows. Your extension was not used.               |
| You did not get an expiration email                        | Emails start {{ ws_reminder_days }} days before expiration, from <help-rcc@mcw.edu>. Check your junk folder. If you used `-m`, `ws_list -v` shows the address. See [Reminder Emails](#expiration-reminder-emails).           |
| Cannot create files in `/scratch/g/pi_netid`               | Create a workspace with `ws_allocate`, and save files there.                                                                                                                                                                 |
| A workspace expired or was released and you need the data  | Restore it within 14 days. See [Restore a Workspace](#restore-a-workspace).                                                                                                                                                  |
| `ws_restore` does not work                                 | Create the new workspace first. Then copy the old name exactly as `ws_restore -l` shows it. Do not use the name from `ws_list`. Answer the quick question that appears.                                                      |
| Someone you shared with cannot find your workspace         | Send them the full path from `ws_list`. They cannot list your lab folder, so they need the exact path. Run `ws_share list myproject` to confirm they are listed.                                                             |
| `JOB REJECTED: workspace ... expires on ...` from `sbatch` | The job would still be running when the workspace expires. Follow one of the fixes in the message. See [Job Time Limits and Workspace Expiration](../cluster/jobs/running-jobs.md#job-time-limits-and-workspace-expiration). |
| `JOB REJECTED: ... workspace that expired or was released` | Restore the workspace with the steps in the message, then submit from the restored workspace.                                                                                                                                |
| `WORKSPACE NOTICE: ... expires in N days`                  | Your job was accepted. Extend the workspace when you can, or copy what you need to group storage.                                                                                                                            |
| `WORKSPACE WARNING: ... is not a workspace`                | Your job was accepted, but it runs outside a workspace. Create a workspace and run your jobs from there.                                                                                                                     |

--8<-- "includes/abbreviations.md"
