# linux-fundamental
## 0.3.1 Linux CLI Essentials – Summary Notes

## Why It Matters
- Core skill for security analysts, pentesters, and system defenders.
- Critical for log analysis, malware hunting, forensics, and automation.

## Essential Commands

| Command | Purpose |
|----------|---------|
| `cat` | View file contents |
| `grep` | Search for patterns in text |
| `find` | Locate files based on criteria |
| `awk` | Process text by column |
| `cut` | Trim text to specific fields |
| `ls` | List directory contents |
| `chmod`, `chown` | Manage file permissions and ownership |
| `head`, `tail` | View start/end of files |
| `wc` | Count words, lines, or characters |
| `sort`, `uniq` | Organize and de-duplicate data |
| `xargs` | Pass output as input to other commands |
| `tee` | Output to file and screen simultaneously |
| `diff` | Compare files to detect changes |

## Security Use Cases
- Log forensics
- Malware hunting
- Detecting privilege escalation risks
- Monitoring system changes
- Building security automation

## Real-World Example

- **Find all SUID binaries (common for privilege escalation):**

```bash
find / -perm -4000 -type f 2>/dev/null
```

- **Extract failed SSH login attempts:**

```bash
grep "Failed password" /var/log/auth.log
```