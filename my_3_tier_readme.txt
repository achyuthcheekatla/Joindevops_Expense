Free Tier: Redhat-9-DevOps-Practice
ami-0220d79f3f480ecf5

1.Security group created
sg-mysqldb-ds92

2.instance created 
mysqldb-ds92

3. dnf install mysql-server -y

Enable and start the MySQL service:

systemctl enable mysqld
systemctl start mysqld
Set the root password (use ExpenseApp@1 or a password of your choice):

To connect external if anyone needs to connect...

mysql_secure_installation --set-root-pass ExpenseApp@1

here
username: root 
password: ExpenseApp@1

Backend-------------
a.create sg backend
b.create an instance for backend
c.create a dir and download the code.
d.installing nodejs

dnf module disable nodejs -y
dnf module enable nodejs:24 -y
dnf install nodejs -y

mkdir /app


Now we have to create a systemctl files

cd /etc/systemd/system/

vim backend.service

chmod 755 backend.service

create a backend.service file 
-------------------------
[Unit]
Description=Expense Backend Service
After=network.target

[Service]
User=expense
Environment=DB_HOST=172.31.41.138 
Environment=DB_USER=expense
Environment=DB_PWD=ExpenseApp@1
Environment=DB_DATABASE=transactions
Environment=ADMIN_TOKEN=admin123
ExecStart=/bin/node /app/index.js
SyslogIdentifier=backend
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target

---------------------
systemctl daemon-reload
systemctl enable backend
systemctl start backend
systemctl status backend

install client mysql in backend 
dnf install mysql -y


mysql -h 172.31.41.138 -u root -pExpenseApp@1 < /app/schema/backend.sql
No such file/folder

mysql -h 172.31.41.138 -u root -pExpenseApp@1 < backend.sql
warning

USE transactions;
SHOW TABLES;

to check tables are executed or not
mysql -h 172.31.41.138 -u root -pExpenseApp@1 -e "USE transactions; SHOW TABLES;"

mysql -h 172.31.41.138 -u root -pExpenseApp@1 ---> login to mysql working

USE transactions; SHOW TABLES;

SELECT COUNT(*) FROM transactions; --> output is 0

107.23.38.187 | 172.31.35.244 | t3.micro | null
[ root@ip-172-31-35-244 ~ ]# cat app/schema/backend.sql
CREATE DATABASE IF NOT EXISTS transactions;
USE transactions;

CREATE TABLE IF NOT EXISTS transactions (
    id          INT AUTO_INCREMENT PRIMARY KEY,
    amount      INT NOT NULL,
    description VARCHAR(255) NOT NULL,
    category    VARCHAR(50) NOT NULL
);

CREATE USER IF NOT EXISTS 'expense'@'%' IDENTIFIED BY 'ExpenseApp@1';
GRANT ALL ON transactions.* TO 'expense'@'%';
FLUSH PRIVILEGES;

49:27 ip-172-31-35-244.ec2.internal backend[66038]: Node.js v24.21.0
Oct 06 19:49:27 ip-172-31-35-244.ec2.internal systemd[1]: backend.service: Main process exited, code=exited, status=1/FAILURE
Oct 06 19:49:27 ip-172-31-35-244.ec2.internal systemd[1]: backend.service: Failed with result 'exit-code'.
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal systemd[1]: backend.service: Scheduled restart job, restart counter is at 723.
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal systemd[1]: Stopped Expense Backend Service.
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal systemd[1]: Started Expense Backend Service.
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal snoopy[66075]: [uid:991 sid:66075 tty:(none) cwd:/ filename:/bin/node]: /bin/node /app/index.js
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]: node:internal/modules/cjs/loader:1568
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:   throw err;
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:   ^
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]: Error: Cannot find module '/app/index.js'
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:     at Module._resolveFilename (node:internal/modules/cjs/loader:1564:15)
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:     at wrapResolveFilename (node:internal/modules/cjs/loader:1118:27)
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:     at defaultResolveImplForCJSLoading (node:internal/modules/cjs/loader:1142:10)
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:     at resolveForCJSWithHooks (node:internal/modules/cjs/loader:1169:12)
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:     at Module._load (node:internal/modules/cjs/loader:1341:5)
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:     at wrapModuleLoad (node:internal/modules/cjs/loader:261:19)
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:     at Module.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:154:5)
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:     at node:internal/main/run_main_module:33:47 {
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:   code: 'MODULE_NOT_FOUND',
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]:   requireStack: []
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]: }
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal backend[66075]: Node.js v24.21.0
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal systemd[1]: backend.service: Main process exited, code=exited, status=1/FAILURE
Oct 06 19:49:32 ip-172-31-35-244.ec2.internal systemd[1]: backend.service: Failed with result 'exit-code'.

----------------------------------------------------------------

curl http://localhost:8080/health
{"status":"ok","db":"up"}

-------Frontend--------
create the sg for frontend 
ssh 22 0.0.0.0/0
TCP 80 

dnf install nginx -y

systemctl enable nginx
systemctl start nginx
systemctl status nginx ---> running


/usr/share/nginx/html/* ---> index.html

default configuration file in nginx
etc/nginx/nginx.conf

here we can change the default port 
 root@ip-172-31-44-139 /var/log/nginx ]# cat access.log

49.205.227.227 - - [07/Oct/2026:18:13:21 +0000] "GET / HTTP/1.1" 200 5909 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/154.0.0.0 Safari/537.36" "-"
49.205.227.227 - - [07/Oct/2026:18:13:21 +0000] "GET /icons/poweredby.png HTTP/1.1" 200 2126 "http://52.90.72.147/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/154.0.0.0 Safari/537.36" "-"
49.205.227.227 - - [07/Oct/2026:18:13:21 +0000] "GET /system_noindex_logo.png HTTP/1.1" 200 2478 "http://52.90.72.147/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/154.0.0.0 Safari/537.36" "-"
49.205.227.227 - - [07/Oct/2026:18:13:21 +0000] "GET /poweredby.png HTTP/1.1" 200 368 "http://52.90.72.147/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/154.0.0.0 Safari/537.36" "-"
49.205.227.227 - - [07/Oct/2026:18:13:22 +0000] "GET /favicon.ico HTTP/1.1" 404 3971 "http://52.90.72.147/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/154.0.0.0 Safari/537.36" "-"


rm -rf /usr/share/nginx/html/*

curl -o /tmp/frontend.tar.gz https://raw.githubusercontent.com/daws-92s/expense-documentation/refs/heads/main/artifacts/expense-frontend-v5.tar.gz

cd /usr/share/nginx/html
tar -xzf /tmp/frontend.tar.gz


configuring Reverse Proxy

vim /etc/nginx/default.d/expense.conf
172.31.45.182

proxy_http_version 1.1;
proxy_set_header Host              $host;
proxy_set_header X-Real-IP         $remote_addr;
proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_connect_timeout 5s;
proxy_read_timeout    30s;   # 504 if the backend takes longer than this

# Browser calls /api/transaction → nginx strips /api → backend gets /transaction
location /api/ {
    proxy_pass http://172.31.45.182:8080/;
}

location /health {
    stub_status on;
    access_log off;
}

# ── HTTP status code demos at the web-server layer ──────────────────────────
# 301 — page moved permanently
location = /home {
    return 301 /;
}

# 302 — temporary redirect to an external page
location = /docs {
    return 302 https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status;
}

# 403 — nobody is allowed here
location /admin {
    deny all;
}


----------
nginx -t

systemctl restart nginx

curl http://localhost/api/health

HTTP Status Codes
--------------------------------------------------

Session-10 --->02.0ct.2026---> 08.10.2026

Domain purchased

https://achsam.online

Nameservers
===========
lunar.dns-parking.com
solar.dns-parking.com


nslookup achsam.online

Domain transfer to AWS Nameservers 
============================
Route53 ---> Hosted Zone--> Nameservers update in the Hostinguer server/website with below ns

ns-1918.awsdns-47.co.uk.
ns-517.awsdns-00.net.
ns-379.awsdns-47.com.
ns-1067.awsdns-05.org.

