# Managing User Access in Linux

> **Disclaimer:** This project was completed as part of the Google Cybersecurity Certificate (Coursera). The scenario comes from the course. The commands, outputs, and explanations are my own documentation of the work I performed.

## Project description
A new employee, `researcher9`, joined my organization's Research department, and I was responsible for managing their access to the system over time. Using Linux commands, I added the user, placed them in the correct groups, gave them ownership of a project file, and removed them from the system when they left. Managing users this way is part of authentication and access management: only the right people have accounts, and accounts are updated or removed when people change roles or leave.

## Commands Used
| Command | Purpose |
|---|---|
| `useradd` | Add a new user to the system |
| `usermod -g` | Change a user's primary group |
| `usermod -a -G` | Append a user to a supplementary (secondary) group |
| `chown` | Change the owner of a file |
| `userdel` | Delete a user from the system |
| `groupdel` | Delete a group |
| `sudo` | Run a command with root privileges |

Adding, modifying, and deleting users and groups requires root privileges, so every one of these commands is run with `sudo`.

## Add a new user
The username assigned to the new employee was `researcher9`. I added the user to the system:

```bash
sudo useradd researcher9
```

Next, I added the user to the `research_team` group as their primary group using `usermod` with the `-g` option:

```bash
sudo usermod researcher9 -g research_team
```

`researcher9` now has `research_team` as their primary group.

## Assign file ownership
`researcher9` took responsibility for the `project_r` project, so I made them the owner of `project_r.txt`. The file is in `/home/researcher2/projects` and was owned by `researcher2`. I navigated to the directory, confirmed the file was there, and changed its owner with `chown`:

```bash
cd /home/researcher2/projects
ls
sudo chown researcher9 project_r.txt
```
Output of `ls`:
```
project_r.txt
```

## Add the user to a secondary group
A couple of months later, the employee's role changed and they began working in both Research and Sales. I added `researcher9` to the `sales_team` group as a secondary group, while keeping `research_team` as their primary group:

```bash
sudo usermod researcher9 -a -G sales_team
```

- `-G` specifies the supplementary group.
- `-a` appends the user to that group. Without `-a`, `-G` would replace all of the user's existing supplementary groups.
- Options are case-sensitive: lowercase `-a`, uppercase `-G`.

## Delete a user
A year later, the employee left the company, so I removed them from the system:

```bash
sudo userdel researcher9
```
Output:
```
userdel: group researcher9 not removed because it is not the primary group of user researcher9.
```

This message is expected. When a user is created in Linux, a group with the same name is automatically created with that user as its only member. Because I changed `researcher9`'s primary group to `research_team`, `userdel` left that leftover group behind. Removing empty groups after deleting a user is good practice, so I deleted it:

```bash
sudo groupdel researcher9
```

## Full Command Sequence
```bash
sudo useradd researcher9
sudo usermod researcher9 -g research_team
cd /home/researcher2/projects
ls
sudo chown researcher9 project_r.txt
sudo usermod researcher9 -a -G sales_team
sudo userdel researcher9
sudo groupdel researcher9
```

## Summary
I managed the full lifecycle of a user account: adding `researcher9` and assigning their primary group, giving them ownership of `project_r.txt`, adding them to a secondary group when their role changed, and deleting both the user and their leftover group when they left. Each step keeps access aligned with the person's current role, so new employees get only what they need and former employees keep no access. This supports the principle of least privilege and reduces the risk of unauthorized access to organization data.

## Key Takeaways
- User and group management commands change system accounts, so they require root privileges through `sudo`.
- `-g` sets a user's primary group, while `-a -G` appends a secondary group without removing existing ones.
- Creating a user also creates a matching group, so clean up leftover empty groups after deleting a user.
- `chown` changes a file's owner, which controls who has user-level permissions on it.
- Removing accounts promptly when people leave is a core part of limiting unauthorized access.
- A good habit is to confirm each change afterward, for example with `ls -l` for file ownership or `id researcher9` for group membership.
