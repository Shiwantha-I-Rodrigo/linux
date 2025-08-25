# 🔹 Scenarios

## Scenario 1 — “The web app won’t start on the new port”

You’ve deployed a small Flask app behind Apache on **Rocky Linux / Fedora**. The app’s code lives in `/srv/web/app`, and Apache should listen on **TCP 8081**. The app reads static files from `/srv/web/app/static` and makes outbound calls to a backend API.

Symptoms:

* Apache starts, but the app doesn’t answer on 8081.
* Access logs are empty; error logs mention permission issues on `/srv/web/app`.
* Security team asks that you do a quick test run in non-blocking mode, then restore protections.
* Devs also need **read/execute** on the code dir for group `dev`, and **read/write** on `/srv/web/app/static/config.json` for user `alice`.
* Provide a quick SELinux denial report for the incident review and confirm the system’s SELinux state before and after.

Your tasks (combine them logically):

* Inspect current SELinux mode and overall status.
* Temporarily relax enforcement to validate the workflow, then re-enable protection.
* Make the web content location and the custom port compliant.
* Allow the web server to talk to the backend over the network.
* Fix security contexts on disk recursively.
* Set the requested ACLs, show them, and later clean up the per-file ACL once testing ends.
* Produce a summarized SELinux analysis for the auditors.

---

## Scenario 2 — “FTP vendor dropbox with tight controls”

A vendor uploads files via **vsftpd** to `/srv/ftp/upload` on **Rocky Linux / Fedora**. The vendor account is `bob`. Security requirements:

* The vendor must be able to write **only** into `/srv/ftp/upload`.
* FTP service should be allowed to read/write the designated upload area under SELinux.
* Give group `vendors` **read/write** to the upload directory, and user `bob` **read/write** to `/srv/ftp/upload/incoming.txt` (example file).
* Verify all ACLs, then remove just `bob`’s special ACL from the file after UAT, and finally wipe **all** ACLs from the upload dir when the project ends.
* Ensure content in `/srv/ftp/upload` has appropriate SELinux labels applied recursively and persists.
* Make the FTP home-dir access policy persist across reboots.
* Provide an SELinux denial report sweep for the change ticket.

---

## Scenario 3 — “PostgreSQL data move + secondary port”

DBA moved PostgreSQL’s data directory to `/data/pgdata` and wants a secondary listener on **TCP 5433** for migration testing (5432 remains as is). After the move, PostgreSQL won’t start; logs hint at SELinux. Your tasks:

* Label the new data path appropriately and apply it recursively.
* Register the extra DB port with SELinux policy.
* Confirm general SELinux status, and check the state of relevant booleans.
* Show a quick readout of *all* SELinux booleans on the system for the change record.

---

## Scenario 4 — “User web directories for Marketing”

Marketing wants Apache `~user` home pages enabled (e.g., `http://host/~carol`) on **Fedora/Rocky**. Content sits under each user’s `~/public_html`. Requirements:

* Enable policy so Apache can read users’ public web content.
* Fix labels under `/home/*/public_html` recursively so Apache serves them.
* Grant group `marketing` **read** access to `/home/carol/public_html/press-kit.txt` using ACLs. Validate the ACLs.
* Later, remove **just** that group ACL from the file when the campaign ends.

---

## Scenario 5 — “Tightening back down after an incident”

After a short maintenance window where SELinux was relaxed for testing, you must:

* Verify mode, then **ensure** the system is back to enforcing.
* Re-generate an SELinux denial analysis for the post-mortem (entire log).
* Confirm that your earlier web-related boolean change is **persistently** enabled.
* Show the current value of that specific boolean only (not the whole list).

---

# 🔹 Answer Key

## Scenario 1

```bash
# Inspect SELinux mode and overall status
getenforce
sestatus

# (Temporary) relax to permissive for a quick live test, then put it back later
setenforce 0
# ... run your quick functional test ...
setenforce 1

# Prepare example content
mkdir -p /srv/web/app/static
echo '{"feature":"on"}' > /srv/web/app/static/config.json

# Register custom Apache port 8081 under the httpd SELinux type
semanage port -a -t http_port_t -p tcp 8081

# Label the web root so httpd may read it; then apply recursively
semanage fcontext -a -t httpd_sys_content_t '/srv/web(/.*)?'
restorecon -R /srv/web

# Allow Apache to make outbound network connections (persistently)
setsebool -P httpd_can_network_connect on

# ACLs for devs and a specific developer
setfacl -m g:dev:rx /srv/web/app
setfacl -m u:alice:rw /srv/web/app/static/config.json
getfacl /srv/web/app /srv/web/app/static/config.json

# Later, remove just the per-file ACL for alice from that file
setfacl -x u:alice /srv/web/app/static/config.json
getfacl /srv/web/app/static/config.json

# Summarize SELinux denials for the incident
sealert -a /var/log/audit/audit.log
```

---

## Scenario 2

```bash
# Prep paths/content
mkdir -p /srv/ftp/upload
echo 'placeholder' > /srv/ftp/upload/incoming.txt

# Ensure FTP may use the upload area under a read/write-friendly public type
semanage fcontext -a -t public_content_rw_t '/srv/ftp/upload(/.*)?'
restorecon -R /srv/ftp/upload

# Allow FTP to read/write user/home-style content persistently
setsebool -P ftp_home_dir on

# ACLs: group vendors RW on dir, user bob RW on file
setfacl -m g:vendors:rw /srv/ftp/upload
setfacl -m u:bob:rw /srv/ftp/upload/incoming.txt
getfacl /srv/ftp/upload /srv/ftp/upload/incoming.txt

# After UAT, remove just bob’s ACL from the file
setfacl -x u:bob /srv/ftp/upload/incoming.txt
getfacl /srv/ftp/upload/incoming.txt

# End of project: wipe ALL ACLs from the upload directory
setfacl -b /srv/ftp/upload
getfacl /srv/ftp/upload

# Provide a full SELinux denial sweep for the change ticket
sealert -a /var/log/audit/audit.log
```

---

## Scenario 3

```bash
# Prep data dir
mkdir -p /data/pgdata

# Label new PostgreSQL data directory and apply recursively
semanage fcontext -a -t postgresql_db_t '/data/pgdata(/.*)?'
restorecon -R /data/pgdata

# Register secondary PostgreSQL port 5433
semanage port -a -t postgresql_port_t -p tcp 5433

# Confirm general SELinux status
sestatus

# Spot-check a specific boolean (example) and also dump ALL booleans
# (Specific check)
getsebool httpd_can_network_connect
# (All)
getsebool -a
```

---

## Scenario 4

```bash
# Example user content
mkdir -p /home/carol/public_html
echo 'Press kit draft' > /home/carol/public_html/press-kit.txt

# Enable userdir-style access for Apache persistently
setsebool -P httpd_enable_homedirs on

# Ensure proper labels on public_html trees
semanage fcontext -a -t httpd_user_content_t '/home/[^/]+/public_html(/.*)?'
restorecon -R /home

# ACL: grant marketing read on the press kit, verify, then later remove
setfacl -m g:marketing:r-- /home/carol/public_html/press-kit.txt
getfacl /home/carol/public_html/press-kit.txt

# Campaign end: remove just that group ACL
setfacl -x g:marketing /home/carol/public_html/press-kit.txt
getfacl /home/carol/public_html/press-kit.txt
```

---

## Scenario 5

```bash
# Verify current SELinux mode, then force enforcing
getenforce
setenforce Enforcing    # equivalent to 'setenforce 1'
getenforce

# Full denial analysis for the post-mortem
sealert -a /var/log/audit/audit.log

# Confirm the web outbound boolean is persistently enabled (specific boolean only)
getsebool httpd_can_network_connect
```

---