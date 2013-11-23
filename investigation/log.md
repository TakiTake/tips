# Investigate log file

You recieve a lot of alert mail from your app.

This is log data sample _access.log_.

```
127.0.0.1 - - [23/Nov/2013:14:30:15 +0900] "GET /search?keyword=test HTTP/1.0" 500 2326 "http://www.example.com/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:25.0) Gecko/20100101 Firefox/25.0" 
```

## Your boss ask you "How many errors?".

You have to count 500 error from access log.

### Solution

```shell
$ awk '$9==500' access.log | wc -l
```

Use _awk_ command to filter lines.

An input line is normally made up of fields separated by white space. So you can get http status with $9.

```
127.0.0.1                 => $1
-                         => $2
-                         => $3
[23/Nov/2013:14:30:15     => $4
+0900]                    => $5
"GET                      => $6
/search?keyword=test      => $7
HTTP/1.0"                 => $8
500                       => $9
2326                      => $10
"http://www.example.com/" => $11
"Mozilla/5.0              => $12
(Macintosh;               => $13
Intel                     => $14
Mac                       => $15
OS                        => $16
X                         => $17
10.8;                     => $18
rv:25.0)                  => $19
Gecko/20100101            => $20
Firefox/25.0"             => $21
```

## Your boss ask you "When start the error?"

You have to find first line which http status is 500.

### Solution

```shell
$ awk '$9==500' access.log | head -n 1
```

Use _head_ command and _n_ option to display first line.

## Your boss ask you "When stop the error?"

You have to find last line which http status is 500.

### Solution

```shell
$ awk '$9==500' access.log | tail -n 1
```

Use _tail_ command and _n_ option to display last line.
