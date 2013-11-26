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

An input line is normally made up of fields separated by white space. So you can get http status code with $9.

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

### Your boss ask you again "Ignore Googlebot"

#### Solution

```shell
$ awk '$9==500' access.log | awk -F\" '$6 !~ /Googlebot/' | wc -l
```

Use _F_ option to change field separator from white space to double quote. So you can get UserAgent with $6. And matched records whoes sixth field does not contain "Googlebot".

```
127.0.0.1 - - [23/Nov/2013:14:30:15 +0900]                                        => $1
GET /search?keyword=test HTTP/1.0                                                 => $2
 500 2326                                                                         => $3
http://www.example.com/                                                           => $4
                                                                                  => $5
Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:25.0) Gecko/20100101 Firefox/25.0 => $6
                                                                                  => $7
```

## Your boss ask you "When start the error?"

You have to find first line which http status code is 500.

### Solution

```shell
$ awk '$9==500' access.log | head -n 1
```

Use _head_ command and _n_ option to display first line.

## Your boss ask you "When stop the error?"

You have to find last line which http status code is 500.

### Solution

```shell
$ awk '$9==500' access.log | tail -n 1
```

Use _tail_ command and _n_ option to display last line.

## Your boss ask you "Is there any difference of the number of access between today and yesterday's log?"

You have to compare today's access log and yesterday's one.

### Solution

```shell
$ wc -l access.log
$ wc -l access.log.yesterday
```

Compare number of the lines(access).

### Your boss ask you again "from 10:05 to 19:45 please"

#### Solution

```shell
$ sed -n '/2013:10:05/,/2013:19:46/p' access.log | wc -l
$ sed -n '/2013:10:05/,/2013:19:46/p' access.log.yesterday | wc -l
```

Use _sed_ command to slice log data.

And there are two precautions.

First end pattern should be **2013:19:46** because _sed_ command is finished immediately after matched end pattern.

```
2013:19:45:00 <- finish sed command in this line if /2013:19:45/
2013:19:45:23 <- miss this line
2013:19:45:48 <- miss this line
2013:19:46:02
```

Second from first matched line to end of lines are returned if not matched end pattern.

## Your boss ask you "How many count of each http status code?"

You have to count not only 500 but also 200, 404 and so on.

### Solution

```shell
$ awk '{ print $9 }' access.log | sort | uniq -c
```

Use _sort_ and _uniq_ command to count each http status code.

First process only output http status code. Second process sort http status codes. Third process output unique status code with the count of the number.
