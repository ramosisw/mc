# MinIO Client Complete Guide [![Slack](https://slack.min.io/slack?type=svg)](https://slack.min.io)

MinIO Client (mc) provides a modern alternative to UNIX commands like ls, cat, cp, mirror, diff etc. It supports filesystems and Amazon S3 compatible cloud storage service (AWS Signature v2 and v4).

```
ls       list buckets and objects
tree     list buckets and objects in a tree format
mb       make a bucket
rb       remove a bucket
cat      display object contents
head     display first 'n' lines of an object
pipe     stream STDIN to an object
share    generate URL for temporary access to an object
cp       copy objects
mirror   synchronize objects to a remote site
find     search for objects
sql      run sql queries on objects
stat     stat contents of objects
diff     list differences in object name, size, and date between buckets
rm       remove objects
event    manage object notifications
watch    watch for object events
policy   manage anonymous access to objects
admin    manage MinIO servers
session  manage saved sessions for cp command
config   manage mc configuration file
update   check for a new software update
version  print version info
```

## 1.  Download MinIO Client
### Docker Stable
```
docker pull minio/mc
docker run minio/mc ls play
```

### Docker Edge
```
docker pull minio/mc:edge
docker run minio/mc:edge ls play
```

**Note:** Above examples run `mc` against MinIO [_play_ environment](#test-your-setup) by default. To run `mc` against other S3 compatible servers, start the container this way:

```
docker run -it --entrypoint=/bin/sh minio/mc
```

then use the [`mc config` command](#3-add-a-cloud-storage-service).

### Homebrew (macOS)
Install mc packages using [Homebrew](http://brew.sh/)

```
brew install minio/stable/mc
mc --help
```

### Binary Download (GNU/Linux)
| Platform | Architecture | URL |
| ---------- | -------- |------|
|GNU/Linux|64-bit Intel|https://dl.min.io/client/mc/release/linux-amd64/mc |
||64-bit PPC|https://dl.min.io/client/mc/release/linux-ppc64le/mc |

```
chmod +x mc
./mc --help
```

### Binary Download (Microsoft Windows)
| Platform | Architecture | URL |
| ---------- | -------- |------|
|Microsoft Windows|64-bit Intel|https://dl.min.io/client/mc/release/windows-amd64/mc.exe |

```
mc.exe --help
```

### Install from Source
Source installation is intended only for developers and advanced users. `mc update` command does not support update notifications for source based installations. Please download official releases from https://min.io/download/#minio-client.

If you do not have a working Golang environment, please follow [How to install Golang](https://golang.org/doc/install).

```
go get -d github.com/minio/mc
cd ${GOPATH}/src/github.com/minio/mc
make
```

## 2. Run MinIO Client

### GNU/Linux

```
chmod +x mc
./mc --help
```

### macOS

```
chmod 755 mc
./mc --help
```

### Microsoft Windows

```
mc.exe --help
```

## 3. Add a Cloud Storage Service
Note: If you are planning to use `mc` only on POSIX compatible filesystems, you may skip this step and proceed to **Step 4**.

To add one or more Amazon S3 compatible hosts, please follow the instructions below. `mc` stores all its configuration information in ``~/.mc/config.json`` file.

#### Usage

```
mc config host add <ALIAS> <YOUR-S3-ENDPOINT> <YOUR-ACCESS-KEY> <YOUR-SECRET-KEY> <API-SIGNATURE>
```

Alias is simply a short name to your cloud storage service. S3 end-point, access and secret keys are supplied by your cloud storage provider. API signature is an optional argument. By default, it is set to "S3v4".

### Example - MinIO Cloud Storage
MinIO server displays URL, access and secret keys.


```
mc config host add minio http://192.168.1.51 BKIKJAA5BMMU2RHO6IBB V7f1CwQqAcwo80UEIJEjc5gVQUSSx5ohQ9GSrr12 --api S3v4
```

### Example - Amazon S3 Cloud Storage
Get your AccessKeyID and SecretAccessKey by following [AWS Credentials Guide](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSGettingStartedGuide/AWSCredentials.html).

```
mc config host add s3 https://s3.amazonaws.com BKIKJAA5BMMU2RHO6IBB V7f1CwQqAcwo80UEIJEjc5gVQUSSx5ohQ9GSrr12 --api S3v4
```

### Example - Google Cloud Storage
Get your AccessKeyID and SecretAccessKey by following [Google Credentials Guide](https://cloud.google.com/storage/docs/migrating?hl=en#keys)

```
mc config host add gcs  https://storage.googleapis.com BKIKJAA5BMMU2RHO6IBB V8f1CwQqAcwo80UEIJEjc5gVQUSSx5ohQ9GSrr12
```

### Specify host configuration through environment variable
```
export MC_HOST_<alias>=https://<Access Key>:<Secret Key>@<YOUR-S3-ENDPOINT>
```

Example:
```
export MC_HOST_myalias=https://Q3AM3UQ867SPQQA43P2F:zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG@play.min.io
mc ls myalias
```

## 4. Test Your Setup
`mc` is pre-configured with https://play.min.io, aliased as "play". It is a hosted MinIO server for testing and development purpose.  To test Amazon S3, simply replace "play" with "s3" or the alias you used at the time of setup.

*Example:*

List all buckets from https://play.min.io

```
mc ls play
[2016-03-22 19:47:48 PDT]     0B my-bucketname/
[2016-03-22 22:01:07 PDT]     0B mytestbucket/
[2016-03-22 20:04:39 PDT]     0B mybucketname/
[2016-01-28 17:23:11 PST]     0B newbucket/
[2016-03-20 09:08:36 PDT]     0B s3git-test/
```

## 5. Everyday Use
You may add shell aliases to override your common Unix tools.

```
alias ls='mc ls'
alias cp='mc cp'
alias cat='mc cat'
alias mkdir='mc mb'
alias pipe='mc pipe'
alias find='mc find'
alias tree='mc tree'
```

## 6. Global Options

### Option [--debug]
Debug option enables debug output to console.

*Example: Display verbose debug output for `ls` command.*

```
mc --debug ls play
mc: <DEBUG> GET / HTTP/1.1
Host: play.min.io
User-Agent: MinIO (darwin; amd64) minio-go/1.0.1 mc/2016-04-01T00:22:11Z
Authorization: AWS4-HMAC-SHA256 Credential=**REDACTED**/20160408/us-east-1/s3/aws4_request, SignedHeaders=expect;host;x-amz-content-sha256;x-amz-date, Signature=**REDACTED**
Expect: 100-continue
X-Amz-Content-Sha256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
X-Amz-Date: 20160408T145236Z
Accept-Encoding: gzip

mc: <DEBUG> HTTP/1.1 200 OK
Transfer-Encoding: chunked
Accept-Ranges: bytes
Content-Type: text/xml; charset=utf-8
Date: Fri, 08 Apr 2016 14:54:55 GMT
Server: MinIO/DEVELOPMENT.2016-04-07T18-53-27Z (linux; amd64)
Vary: Origin
X-Amz-Request-Id: HP30I0W2U49BDBIO

mc: <DEBUG> Response Time:  1.220112837s

[...]

[2016-04-08 03:56:14 IST]     0B albums/
[2016-04-04 16:11:45 IST]     0B backup/
[2016-04-01 20:10:53 IST]     0B deebucket/
[2016-03-28 21:53:49 IST]     0B guestbucket/
```

### Option [--json]
JSON option enables parseable output in [JSON lines](http://jsonlines.org/) format.

*Example: List all buckets from MinIO play service.*

```
mc --json ls play
{"status":"success","type":"folder","lastModified":"2016-04-08T03:56:14.577+05:30","size":0,"key":"albums/"}
{"status":"success","type":"folder","lastModified":"2016-04-04T16:11:45.349+05:30","size":0,"key":"backup/"}
{"status":"success","type":"folder","lastModified":"2016-04-01T20:10:53.941+05:30","size":0,"key":"deebucket/"}
{"status":"success","type":"folder","lastModified":"2016-03-28T21:53:49.217+05:30","size":0,"key":"guestbucket/"}
```

### Option [--no-color]
This option disables the color theme. It is useful for dumb terminals.

### Option [--quiet]
Quiet option suppress chatty console output.

### Option [--config-dir]
Use this option to set a custom config path.

### Option [ --insecure]
Skip SSL certificate verification.

## 7. Commands

|   |   | |
|:---|:---|:---|
|[**ls** - List buckets and objects](#ls)   |[**tree** - List buckets and objects in a tree format](#tree)  |[**mb** - Make a bucket](#mb)  | [**cat** - Concatenate an object](#cat)  |
|[**cp** - Copy objects](#cp) |[**rb** - Remove a bucket](#rb)  | [**pipe** - Pipe to an object](#pipe) |
| [**share** - Share access](#share)  | [**rm** - Remove objects](#rm)   | [**find** - Find files and objects](#find) |
| [**diff** - Diff buckets](#diff) |[**mirror** - Mirror buckets](#mirror)|[**session** - Manage saved sessions](#session) |
| [**config** - Manage config file](#config)  | [**policy** - Set public policy on bucket or prefix](#policy)  | [**event** - Manage events on your buckets](#event)  |
| [**update** - Manage software updates](#update)  |  [**watch** - Watch for events](#watch) | [**stat** - Stat contents of objects and folders](#stat) |
| [**head** - Display first 'n' lines of an object](#head) | [**version** - Show version](#version) | |
| | [**sql** - Run sql queries on objects](#sql) | |


###  Command `ls` - List Objects
`ls` command lists files, buckets and objects. Use `--incomplete` flag to list partially copied content.

```
USAGE:
   mc ls [FLAGS] TARGET [TARGET ...]

FLAGS:
  --recursive, -r               list recursively
  --incomplete, -I              list incomplete uploads
  --help, -h                    show help
```

*Example: List all buckets on https://play.min.io.*

```
mc ls play
[2016-04-08 03:56:14 IST]     0B albums/
[2016-04-04 16:11:45 IST]     0B backup/
[2016-04-01 20:10:53 IST]     0B deebucket/
[2016-03-28 21:53:49 IST]     0B guestbucket/
[2016-04-08 20:58:18 IST]     0B mybucket/
```

<a name="tree"></a>
### Command `tree` - List buckets and directories in a tree format

`tree` command lists buckets and directories in a tree format. Use `--files` flag to include files/objects in listing.

```sh
USAGE:
   mc tree [FLAGS] TARGET [TARGET ...]

FLAGS:
  --help, -h                    show help
  --files, -f                   include files in tree
  --depth, -d                   set the maximum depth of the tree
```

_Example: List all buckets on play/test-bucket in a tree format._

```sh
mc tree play/test-bucket
play/test-bucket/
├─ dir_a
├─ dir_b
│  └─ dir_bb
└─ dir_x
   └─ dir_xx
```


<a name="mb"></a>
### Command `mb` - Make a Bucket
`mb` command creates a new bucket on an object storage. On a filesystem, it behaves like `mkdir -p` command. Bucket is equivalent of a drive or mount point in filesystems and should not be treated as folders. MinIO does not place any limits on the number of buckets created per user.
On Amazon S3, each account is limited to 100 buckets. Please refer to [Buckets Restrictions and Limitations on S3](http://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html) for more information.

```
USAGE:
   mc mb [FLAGS] TARGET [TARGET...]

FLAGS:
  --region value                specify bucket region; defaults to 'us-east-1' (default: "us-east-1")
  --ignore-existing, -p         ignore if bucket/directory already exists
  --help, -h                    show help

```

*Example: Create a new bucket named "mybucket" on https://play.min.io.*


```
mc mb play/mybucket
Bucket created successfully ‘play/mybucket’.
```

*Example: Create a new bucket named "mybucket" on https://s3.amazonaws.com.*


```
mc mb s3/mybucket --region=us-west-1
Bucket created successfully ‘s3/mybucket’.
```

<a name="rb"></a>
### Command `rb` - Remove a Bucket
`rb` command removes a bucket and all its contents on an object storage. On a filesystem, it behaves like `rmdir` command.

Note that when a bucket is removed all policies associated with the bucket will also be removed. If you would like to just
empty the objects in a bucket use `rm` command

```
USAGE:
   mc rb [FLAGS] TARGET [TARGET...]

FLAGS:
  --force                       allow a recursive remove operation
  --dangerous                   allow site-wide removal of objects
  --help, -h                    show help

```

*Example: Remove a bucket named "mybucket" on https://play.min.io.*


```
mc rb play/mybucket --force
Bucket removed successfully ‘play/mybucket’.
```

<a name="cat"></a>
### Command `cat` - Concatenate Objects
`cat` command concatenates contents of a file or object to another. You may also use it to simply display the contents to stdout

```
USAGE:
   mc cat [FLAGS] SOURCE [SOURCE...]

FLAGS:
  --encrypt-key value           encrypt/decrypt objects (using server-side encryption with customer provided keys)
  --help, -h                    show help

ENVIRONMENT VARIABLES:
   MC_ENCRYPT_KEY:  list of comma delimited prefix=secret values
```

*Example: Display the contents of a text file `myobject.txt`*

```
mc cat play/mybucket/myobject.txt
Hello MinIO!!
```

*Example: Display the contents of a server encrypted object `myencryptedobject.txt`*

```
mc cat --encrypt-key "play/mybucket=32byteslongsecretkeymustbegiven1" play/mybucket/myencryptedobject.txt
Hello MinIO!!
```

*Example: Display the contents of a server encrypted object `myencryptedobject.txt`. Pass base64 encoded string if encryption key contains non-printable character like tab*

```
mc cat --encrypt-key "play/mybucket=MzJieXRlc2xvbmdzZWNyZWFiY2RlZmcJZ2l2ZW5uMjE=" play/mybucket/myencryptedobject.txt
Hello MinIO!!
```

<a name="sql"></a>
### Command `sql` - Run sql queries on objects
`sql` run sql queries on objects.

```
USAGE:
  mc sql [FLAGS] TARGET [TARGET...]

FLAGS:
  --query value, -e value       sql query expression
  --recursive, -r               sql query recursively
  --csv-input value             csv input serialization option
  --json-input value            json input serialization option
  --compression value           input compression type
  --csv-output value            csv output serialization option
  --json-output value           json output serialization option
  --encrypt-key value           encrypt/decrypt objects (using server-side encryption with customer provided keys)
  --help, -h                    show help

ENVIRONMENT VARIABLES:
   MC_ENCRYPT_KEY:  list of comma delimited prefix=secret values

INPUT SERIALIZATION
  --csv-input or --json-input can be used to specify input data format. Format is
  specified by a string with pattern "key=value,..." for valid key(s).

  DATA FORMAT:
    csv: Use --csv-input flag
      Valid keys:
        RecordDelimiter (rd)
        FieldDelimiter (fd)
        QuoteChar (qc)
        QuoteEscChar (qec)
        FileHeader (fh)
        Comments (cc)
        QuotedRecordDelimiter (qrd)

    json: Use --json-input flag
      Valid keys:
        Type
    parquet: If object name ends in .parquet, this is automatically interpreted.

OUTPUT SERIALIZATION
  --csv-output or --json-output can be used to specify output data format. Format is
  specified by a string with pattern "key=value,..." for valid key(s).
  DATA FORMAT:
    csv: Use --csv-output flag
      Valid keys:
        RecordDelimiter (rd)
        FieldDelimiter (fd)
        QuoteChar (qc)
        QuoteEscChar (qec)
        QuoteFields (qf)

    json: Use --json-output flag
      Valid keys:
        RecordDelimiter (rd)

COMPRESSION TYPE
    --compression specifies if the queried object is compressed.
    Valid values: NONE | GZIP | BZIP2

```

*Example: Select all columns on a set of objects recursively on AWS S3*

```
mc sql --recursive --query "select * from S3Object" s3/personalbucket/my-large-csvs/
```

*Example: Run an aggregation query on an object on MinIO*

```
mc sql --query "select count(s.power) from S3Object" myminio/iot-devices/power-ratio.csv
```

*Example: Run an aggregation query on an encrypted object with customer provided keys*

```
mc sql --encrypt-key "myminio/iot-devices=32byteslongsecretkeymustbegiven1" \
    --query "select count(s.power) from S3Object" myminio/iot-devices/power-ratio-encrypted.csv
```

For more query examples refer to official AWS S3 documentation [here](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectSELECTContent.html#RESTObjectSELECTContent-responses-examples)

<a name="head"></a>
### Command `head` - Display few lines of object
`head` display first 'n' lines of an object

```
USAGE:
   mc head [FLAGS] SOURCE [SOURCE...]

FLAGS:
  -n value, --lines value       print the first 'n' lines (default: 10)
  --encrypt-key value           encrypt/decrypt objects (using server-side encryption with customer provided keys)
  --help, -h                    show help

ENVIRONMENT VARIABLES:
   MC_ENCRYPT_KEY:  list of comma delimited prefix=secret values
```

*Example: Display the first line of a text file `myobject.txt`*

```
mc head -n 1 play/mybucket/myobject.txt
Hello!!
```

*Example: Display the first line of a server encrypted object `myencryptedobject.txt`*

```
mc head -n 1 --encrypt-key "play/mybucket=32byteslongsecretkeymustbegiven1" play/mybucket/myencryptedobject.txt
Hello!!
```

<a name="pipe"></a>
### Command `pipe` - Pipe to Object
`pipe` command copies contents of stdin to a target. When no target is specified, it writes to stdout.

```
USAGE:
   mc pipe [FLAGS] [TARGET]

FLAGS:
  --encrypt value               encrypt objects (using server-side encryption with server managed keys)
  --encrypt-key value           encrypt/decrypt objects (using server-side encryption with customer provided keys)
  --help, -h                    show help

ENVIRONMENT VARIABLES:
   MC_ENCRYPT:      list of comma delimited prefix values
   MC_ENCRYPT_KEY:  list of comma delimited prefix=secret values
```

*Example: Stream MySQL database dump to Amazon S3 directly.*

```
mysqldump -u root -p ******* accountsdb | mc pipe s3/sql-backups/backups/accountsdb-oct-9-2015.sql
```


<a name="cp"></a>
### Command `cp` - Copy Objects
`cp` command copies data from one or more sources to a target.  All copy operations to object storage are verified with MD5SUM checksums. Interrupted or failed copy operations can be resumed from the point of failure.

```
USAGE:
   mc cp [FLAGS] SOURCE [SOURCE...] TARGET

FLAGS:
  --recursive, -r                    copy recursively
  --older-than value                 copy object(s) older than N days (default: 0)
  --newer-than value                 copy object(s) newer than N days (default: 0)
  --storage-class value, --sc value  set storage class for new object(s) on target
  --attr                             add custom metadata for the object (format: KeyName1=string;KeyName2=string)
  --encrypt value                    encrypt/decrypt objects (using server-side encryption with server managed keys)
  --encrypt-key value                encrypt/decrypt objects (using server-side encryption with customer provided keys)
  --help, -h                         show help

ENVIRONMENT VARIABLES:
   MC_ENCRYPT:      list of comma delimited prefixes
   MC_ENCRYPT_KEY:  list of comma delimited prefix=secret values
```

*Example: Copy a text file to an object storage.*

```
mc cp myobject.txt play/mybucket
myobject.txt:    14 B / 14 B  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100.00 % 41 B/s 0
```

*Example: Copy a text file to an object storage with specified metadata.*

```
mc cp --attr key1=value1;key2=value2 myobject.txt play/mybucket
myobject.txt:    14 B / 14 B  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100.00 % 41 B/s 0
```

*Example: Copy a folder recursively from MinIO cloud storage to Amazon S3 cloud storage with specified metadata.*
```
mc cp --attr Cache-Control=max-age=90000,min-fresh=9000\;key1=value1\;key2=value2 --recursive play/mybucket/burningman2011/ s3/mybucket/
https://play.minio.io:9000/mybucket/myobject.txt:    14 B / 14 B  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100.00 % 41 B/s 0
```

*Example: Copy a text file to an object storage and assign storage-class `REDUCED_REDUNDANCY` to the uploaded object.*

```
mc cp --storage-class REDUCED_REDUNDANCY myobject.txt play/mybucket
myobject.txt:    14 B / 14 B  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100.00 % 41 B/s 0
```

*Example: Copy a server-side encrypted file to an object storage.*

```
mc cp --recursive --encrypt-key "s3/documents/=32byteslongsecretkeymustbegiven1 , myminio/documents/=32byteslongsecretkeymustbegiven2" s3/documents/myobject.txt myminio/documents/
myobject.txt:    14 B / 14 B  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100.00 % 41 B/s 0
```

*Example: Perform key-rotation on a server-side encrypted object*

```
mc cp --encrypt-key 'myminio1/mybucket=32byteslongsecretkeymustgenerate , myminio2/mybucket/=32byteslongsecretkeymustgenerat1' myminio1/mybucket/encryptedobject myminio2/mybucket/encryptedobject
encryptedobject:    14 B / 14 B  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100.00 % 41 B/s 0
```
Notice that two different aliases myminio1 and myminio2 are used for the same endpoint to provide the old secretkey and the newly rotated key.

*Example: Copy a javascript file to object storage and assign Cache-Control header to the uploaded object*

```sh
mc cp --attr Cache-Control=no-cache myscript.js play/mybucket
myscript.js:    14 B / 14 B  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100.00 % 41 B/s 0
```

<a name="rm"></a>
### Command `rm` - Remove Objects
Use `rm` command to remove file or object

```
USAGE:
   mc rm [FLAGS] TARGET [TARGET ...]

FLAGS:
  --recursive, -r               remove recursively
  --force                       allow a recursive remove operation
  --dangerous                   allow site-wide removal of objects
  --incomplete, -I              remove incomplete uploads
  --fake                        perform a fake remove operation
  --stdin                       read object names from STDIN
  --older-than value            remove objects older than L days, M hours and N minutes LMN[d|h|m]. (default: 0)
  --newer-than value            remove objects newer than L days, M hours and N minutes LMN[d|h|m]. (default: 0)
  --encrypt-key value           encrypt/decrypt objects (using server-side encryption with customer provided keys)
  --help, -h                    show help

ENVIRONMENT VARIABLES:
   MC_ENCRYPT_KEY:  list of comma delimited prefix=secret values
```

*Example: Remove a single object.*

```
mc rm play/mybucket/myobject.txt
Removing `play/mybucket/myobject.txt`.
```
*Example: Remove an encrypted object.*

```
mc rm --encrypt-key "play/mybucket=32byteslongsecretkeymustbegiven1" play/mybucket/myobject.txt
Removing `play/mybucket/myobject.txt`.
```

*Example: Recursively remove a bucket's contents. Since this is a dangerous operation, you must explicitly pass `--force` option.*

```
mc rm --recursive --force play/mybucket
Removing `play/mybucket/newfile.txt`.
Removing `play/mybucket/otherobject.txt`.
```

*Example: Remove all uploaded incomplete files for an object.*

```
mc rm --incomplete play/mybucket/myobject.1gig
Removing `play/mybucket/myobject.1gig`.
```
*Example: Remove object and output a message only if the object is created older than 1 day, 2 hours and 30 minutes. Otherwise, the command stays quiet and nothing is printed out.*

```
mc rm -r --force --older-than 1d2h30m myminio/mybucket
Removing `myminio/mybucket/dayOld1.txt`.
Removing `myminio/mybucket/dayOld2.txt`.
Removing `myminio/mybucket/dayOld3.txt`.
```

<a name="share"></a>
### Command `share` - Share Access
`share` command securely grants upload or download access to object storage. This access is only temporary and it is safe to share with remote users and applications. If you want to grant permanent access, you may look at `mc policy` command instead.

Generated URL has access credentials encoded in it. Any attempt to tamper the URL will invalidate the access. To understand how this mechanism works, please follow [Pre-Signed URL](http://docs.aws.amazon.com/AmazonS3/latest/dev/ShareObjectPreSignedURL.html) technique.

```
USAGE:
   mc share [FLAGS] COMMAND

FLAGS:
  --help, -h                       show help

COMMANDS:
   download	  generate URLs for download access
   upload	  generate ‘curl’ command to upload objects without requiring access/secret keys
   list		  list previously shared objects and folders
```

### Sub-command `share download` - Share Download
`share download` command generates URLs to download objects without requiring access and secret keys. Expiry option sets the maximum validity period (no more than 7 days), beyond which the access is revoked automatically.

```
USAGE:
   mc share download [FLAGS] TARGET [TARGET...]

FLAGS:
  --recursive, -r               share all objects recursively
  --expire value, -E value      set expiry in NN[h|m|s] (default: "168h")
  --help, -h                    show help
```

*Example: Grant temporary access to an object with 4 hours expiry limit.*

```

mc share download --expire 4h play/mybucket/myobject.txt
URL: https://play.min.io/mybucket/myobject.txt
Expire: 0 days 4 hours 0 minutes 0 seconds
Share: https://play.min.io/mybucket/myobject.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=Q3AM3UQ867SPQQA43P2F%2F20160408%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20160408T182008Z&X-Amz-Expires=604800&X-Amz-SignedHeaders=host&X-Amz-Signature=1527fc8f21a3a7e39ce3c456907a10b389125047adc552bcd86630b9d459b634

```

#### Sub-command `share upload` - Share Upload
`share upload` command generates a ‘curl’ command to upload objects without requiring access/secret keys. Expiry option sets the maximum validity period (no more than 7 days), beyond which the access is revoked automatically. Content-type option restricts uploads to only certain type of files.

```
USAGE:
   mc share upload [FLAGS] TARGET [TARGET...]

FLAGS:
  --recursive, -r                 recursively upload any object matching the prefix
  --expire value, -E value        set expiry in NN[h|m|s] (default: "168h")
  --content-type value, -T value  specify a content-type to allow
  --help, -h                      show help
```

*Example: Generate a `curl` command to enable upload access to `play/mybucket/myotherobject.txt`. User replaces `<FILE>` with the actual filename to upload*

```
mc share upload play/mybucket/myotherobject.txt
URL: https://play.min.io/mybucket/myotherobject.txt
Expire: 7 days 0 hours 0 minutes 0 seconds
Share: curl https://play.min.io/mybucket -F x-amz-date=20160408T182356Z -F x-amz-signature=de343934bd0ba38bda0903813b5738f23dde67b4065ea2ec2e4e52f6389e51e1 -F bucket=mybucket -F policy=eyJleHBpcmF0aW9uIjoiMjAxNi0wNC0xNVQxODoyMzo1NS4wMDdaIiwiY29uZGl0aW9ucyI6W1siZXEiLCIkYnVja2V0IiwibXlidWNrZXQiXSxbImVxIiwiJGtleSIsIm15b3RoZXJvYmplY3QudHh0Il0sWyJlcSIsIiR4LWFtei1kYXRlIiwiMjAxNjA0MDhUMTgyMzU2WiJdLFsiZXEiLCIkeC1hbXotYWxnb3JpdGhtIiwiQVdTNC1ITUFDLVNIQTI1NiJdLFsiZXEiLCIkeC1hbXotY3JlZGVudGlhbCIsIlEzQU0zVVE4NjdTUFFRQTQzUDJGLzIwMTYwNDA4L3VzLWVhc3QtMS9zMy9hd3M0X3JlcXVlc3QiXV19 -F x-amz-algorithm=AWS4-HMAC-SHA256 -F x-amz-credential=Q3AM3UQ867SPQQA43P2F/20160408/us-east-1/s3/aws4_request -F key=myotherobject.txt -F file=@<FILE>
```

#### Sub-command `share list` - Share List
`share list` command lists unexpired URLs that were previously shared

```
USAGE:
   mc share list COMMAND

COMMAND:
   upload:   list previously shared access to uploads.
   download: list previously shared access to downloads.
```

<a name="mirror"></a>
### Command `mirror` - Mirror Buckets
`mirror` command is similar to `rsync`, except it synchronizes contents between filesystems and object storage.

```
USAGE:
   mc mirror [FLAGS] SOURCE TARGET

FLAGS:
  --overwrite                        overwrite object(s) on target
  --fake                             perform a fake mirror operation
  --watch, -w                        watch and synchronize changes
  --remove                           remove extraneous object(s) on target
  --region value                     specify region when creating new bucket(s) on target (default: "us-east-1")
  -a                                 preserve bucket policy rules on target bucket(s)
  --exclude value                    exclude object(s) that match specified object name pattern
  --older-than value                 filter object(s) older than N days (default: 0)
  --newer-than value                 filter object(s) newer than N days (default: 0)
  --storage-class value, --sc value  specify storage class for new object(s) on target
  --encrypt value                    encrypt/decrypt objects (using server-side encryption with server managed keys)
  --encrypt-key value                encrypt/decrypt objects (using server-side encryption with customer provided keys)
  --help, -h                         show help

ENVIRONMENT VARIABLES:
   MC_ENCRYPT:      list of comma delimited prefixes
   MC_ENCRYPT_KEY:  list of comma delimited prefix=secret values
```

*Example: Mirror a local directory to 'mybucket' on https://play.min.io.*

```
mc mirror localdir/ play/mybucket
localdir/b.txt:  40 B / 40 B  ┃▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓┃  100.00 % 73 B/s 0
```

*Example: Continuously watch for changes on a local directory and mirror the changes to 'mybucket' on https://play.min.io.*

```
mc mirror -w localdir play/mybucket
localdir/new.txt:  10 MB / 10 MB  ┃▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓┃  100.00 % 1 MB/s 15s
```

<a name="find"></a>
### Command `find` - Find files and objects
``find`` command finds files which match the given set of parameters. It only lists the contents which match the given set of criteria.

```
USAGE:
  mc find PATH [FLAGS]

FLAGS:
  --exec value                  spawn an external process for each matching object (see FORMAT)
  --ignore value                exclude objects matching the wildcard pattern
  --name value                  find object names matching wildcard pattern
  --newer value                 match all objects newer than specified time L days, M hours and N minutes
  --older value                 match all objects older than specified time L days, M hours and N minutes
  --path value                  match directory names matching wildcard pattern
  --print value                 print in custom format to STDOUT (see FORMAT)
  --regex value                 match directory and object name with PCRE regex pattern
  --larger value                match all objects larger than specified size in units (see UNITS)
  --smaller value               match all objects smaller than specified size in units (see UNITS)
  --maxdepth value              limit directory navigation to specified depth (default: 0)
  --watch                       monitor a specified path for newly created object(s)
  ...
  ...
  --help, -h                    show help
```

*Example: Find all jpeg images from s3 bucket and copy to MinIO "play/bucket" bucket continuously.*
```
mc find s3/bucket --name "*.jpg" --watch --exec "mc cp {} play/bucket"
```

<a name="diff"></a>
### Command `diff` - Show Difference
``diff`` command computes the differences between the two directories. It only lists the contents which are missing or which differ in size.

It *DOES NOT* compare the contents, so it is possible that the objects which are of same name and of the same size, but have difference in contents are not detected. This way, it can perform high speed comparison on large volumes or between sites

```
USAGE:
  mc diff [FLAGS] FIRST SECOND

FLAGS:
  --config-folder value, -C value  Path to configuration folder. (default: "/root/.mc")
  --quiet, -q                      Disable progress bar display.
  --no-color                       Disable color theme.
  --json                           Enable JSON formatted output.
  --debug                          Enable debug output.
  --insecure                       Disable SSL certificate verification.
  --help, -h                       Show help.

LEGEND:
    < - object is only in source.
    > - object is only in destination.
    ! - newer object is in source.
```

*Example: Compare a local directory and a remote object storage.*

```
 mc diff localdir play/mybucket
‘localdir/notes.txt’ and ‘https://play.min.io/mybucket/notes.txt’ - only in first.
```

### Option [--json]
JSON option enables parseable output in [JSON lines](http://jsonlines.org/) format.

*Example: diff json output.*

```
mc diff minio1/diffbucket minio2/diffbucket --json
{"status":"success","first":"","second":"http://127.0.0.1:9001/diffbucket/file1.png","diff":5}
{"status":"success","first":"http://127.0.0.1:9000/diffbucket/file2.png","second":"","diff":4}
{"status":"success","first":"http://127.0.0.1:9000/diffbucket/file3.png","second":"http://127.0.0.1:9001/diffbucket/file3.png","diff":2}
{"status":"success","first":"http://127.0.0.1:9000/diffbucket/file4.png","second":"http://127.0.0.1:9001/diffbucket/file4.png","diff":1}
```

#### Diff values in json output
|Constant| Value | Meaning |
| ---------- | ---------- | -------- |
|differInNone |0|Does not differ|
|differInSize |1|Differs in size|
|differInTime |2|Differs in time|
|differInType |3|Differs in type exfile/directory|
|differInFirst |4|Only in source (FIRST)|
|differInSecond |5|Only in target (SECOND)|

<a name="watch"></a>
### Command `watch` - Watch for files and object storage events.
``watch`` provides a convenient way to watch on various types of event notifications on object
storage and filesystem.

```
USAGE:
  mc watch [FLAGS] PATH

FLAGS:
  --events value                   filter specific types of events, defaults to all events (default: "put,delete,get")
  --prefix value                   filter events for a prefix
  --suffix value                   filter events for a suffix
  --recursive                      recursively watch for events
  --help, -h                       show help
```

*Example: Watch for all events on object storage*

```
mc watch play/testbucket
[2016-08-18T00:51:29.735Z] 2.7KiB ObjectCreated https://play.min.io/testbucket/CONTRIBUTING.md
[2016-08-18T00:51:29.780Z]  1009B ObjectCreated https://play.min.io/testbucket/MAINTAINERS.md
[2016-08-18T00:51:29.839Z] 6.9KiB ObjectCreated https://play.min.io/testbucket/README.md
```

*Example: Watch for all events on local directory*

```
mc watch ~/Photos
[2016-08-17T17:54:19.565Z] 3.7MiB ObjectCreated /home/minio/Downloads/tmp/5467026530_a8611b53f9_o.jpg
[2016-08-17T17:54:19.565Z] 3.7MiB ObjectCreated /home/minio/Downloads/tmp/5467026530_a8611b53f9_o.jpg
...
[2016-08-17T17:54:19.565Z] 7.5MiB ObjectCreated /home/minio/Downloads/tmp/8771468997_89b762d104_o.jpg
```

<a name="event"></a>
### Command `event` - Manage bucket event notification.
``event`` provides a convenient way to configure various types of event notifications on a bucket. MinIO event notification can be configured to use AMQP, Redis, ElasticSearch, NATS and PostgreSQL services. MinIO configuration provides more details on how these services can be configured.

```
USAGE:
  mc event COMMAND [COMMAND FLAGS | -h] [ARGUMENTS...]

COMMANDS:
  add     add a new bucket notification
  remove  remove a bucket notification. With '--force' can remove all bucket notifications
  list    list bucket notifications

FLAGS:
  --ignore-existing, -p            ignore if event already exists
  --help, -h                       show help
```

*Example: List all configured bucket notifications*

```
mc event list play/andoria
MyTopic        arn:minio:sns:us-east-1:1:TestTopic    s3:ObjectCreated:*,s3:ObjectRemoved:*   suffix:.jpg
```

*Example: Add a new 'sqs' notification resource only to notify on ObjectCreated event*

```
mc event add play/andoria arn:minio:sqs:us-east-1:1:your-queue --event put
```

*Example: Add a new 'sqs' notification resource with filters*

Add `prefix` and `suffix` filtering rules for `sqs` notification resource.

```
mc event add play/andoria arn:minio:sqs:us-east-1:1:your-queue --prefix photos/ --suffix .jpg
```

*Example: Remove a 'sqs' notification resource*

```
mc event remove play/andoria arn:minio:sqs:us-east-1:1:your-queue
```

<a name="policy"></a>
### Command `policy` - Manage bucket policies
Manage anonymous bucket policies to a bucket and its contents

```
USAGE:
  mc policy [FLAGS] set PERMISSION TARGET
  mc policy [FLAGS] set-json FILE TARGET
  mc policy [FLAGS] get TARGET
  mc policy [FLAGS] get-json TARGET
  mc policy list [FLAGS] TARGET

PERMISSION:
  Allowed policies are: [none, download, upload, public].

FILE:
  A valid S3 policy JSON filepath.

FLAGS:
  --help, -h                       show help
```

*Example: Show current anonymous bucket policy*

Show current anonymous bucket policy for ``mybucket/myphotos/2020/`` sub-directory

```sh
mc policy get play/mybucket/myphotos/2020/
Access permission for ‘play/mybucket/myphotos/2020/’ is ‘none’
```

*Example : Set anonymous bucket policy to download only*

Set anonymous bucket policy for ``mybucket/myphotos/2020/`` sub-directory and its objects to ``download`` only. Now, objects under the sub-directory are publicly accessible. e.g ``mybucket/myphotos/2020/yourobjectname``is available at [https://play.min.io:9000/mybucket/myphotos/2020/yourobjectname](https://play.min.io:9000/mybucket/myphotos/2020/yourobjectname)

```sh
mc policy set download play/mybucket/myphotos/2020/
Access permission for ‘play/mybucket/myphotos/2020/’ is set to 'download'
```

*Example : Set anonymous bucket policy from a JSON file*

Configure bucket policy for ``mybucket`` with a policy JSON file.

```sh
mc policy set-json /tmp/policy.json play/mybucket
Access permission for `play/mybucket` is set from `/tmp/policy.json`
```

*Example : Remove current anonymous bucket policy*

Remove any bucket policy for *mybucket/myphotos/2020/* sub-directory.

```sh
mc policy set none play/mybucket/myphotos/2020/
Access permission for ‘play/mybucket/myphotos/2020/’ is set to 'none'
```

<a name="admin"></a>
### Command `admin` - Manage MinIO servers
Please visit [here](https://docs.min.io/docs/minio-admin-complete-guide) for a more comprehensive admin guide.

<a name="session"></a>
### Command `session` - Manage Sessions
``session`` command manages previously saved sessions for `cp` and `mirror` operations

```
USAGE:
  mc session COMMAND [COMMAND FLAGS | -h] [ARGUMENTS...]

COMMANDS:
  list    list all previously saved sessions
  clear   clear a previously saved session
  resume  resume a previously saved session

FLAGS:
  --help, -h                       show help

```

*Example: List all previously saved sessions.*

```
mc session list
IXWKjpQM -> [2016-04-08 19:11:14 IST] cp assets.go play/mybucket
ApwAxSwa -> [2016-04-08 01:49:19 IST] mirror miniodoc/ play/mybucket
```

*Example: Resume a previously saved session.*

```
mc session resume IXWKjpQM
...assets.go: 1.68 KB / 1.68 KB  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100.00 % 784 B/s 2s
```

*Example: Drop a previously saved session.*

```
mc session clear ApwAxSwa
Session ‘ApwAxSwa’ cleared successfully.
```

<a name="config"></a>
### Command `config` - Manage Config File
`config host` command provides a convenient way to manage host entries in your config file `~/.mc/config.json`. It is also OK to edit the config file manually using a text editor.

```
USAGE:
  mc config host COMMAND [COMMAND FLAGS | -h] [ARGUMENTS...]

COMMANDS:
  add, a      add a new host to configuration file
  remove, rm  remove a host from configuration file
  list, ls    lists hosts in configuration file

FLAGS:
  --help, -h                       show help
```

*Example: Manage Config File*

Add MinIO server access and secret keys to config file host entry. Note that, the history feature of your shell may record these keys and pose a security risk. On `bash` shell, use `set -o` and `set +o` to disable and enable history feature momentarily.

```
set +o history
mc config host add myminio http://localhost:9000 OMQAGGOL63D7UNVQFY8X GcY5RHNmnEWvD/1QxD3spEIGj+Vt9L7eHaAaBTkJ
set -o history
```

Remove the host from the config file.

```
mc config host remove myminio
```

List all configured host

```
mc config host list
```

<a name="update"></a>
### Command `update` - Software Updates
Check for new software updates from [https://dl.min.io](https://dl.min.io). Experimental flag checks for unstable experimental releases primarily meant for testing purposes.

```
USAGE:
  mc update [FLAGS]

FLAGS:
  --quiet, -q  suppress chatty console output
  --json       enable JSON formatted output
  --help, -h   show help
```

*Example: Check for an update.*

```
mc update
You are already running the most recent version of ‘mc’.
```

<a name="version"></a>
### Command `version` - Display Version
Display the current version of `mc` installed

```
USAGE:
  mc version [FLAGS]

FLAGS:
  --quiet, -q  suppress chatty console output
  --json       enable JSON formatted output
  --help, -h   show help
```

 *Example: Print version of mc.*

```
mc version
Version: 2016-04-01T00:22:11Z
Release-tag: RELEASE.2016-04-01T00-22-11Z
Commit-id: 12adf3be326f5b6610cdd1438f72dfd861597fce
```
<a name="stat"></a>
### Command `stat` - Stat contents of objects and folders
`stat` command displays information on objects (with optional prefix) contained in the specified bucket on an object storage. On a filesystem, it behaves like `stat` command.

```
USAGE:
   mc stat [FLAGS] TARGET

FLAGS:
  --recursive, -r               stat all objects recursively
  --encrypt-key value           encrypt/decrypt objects (using server-side encryption with customer provided keys)
  --help, -h                    show help

ENVIRONMENT VARIABLES:
   MC_ENCRYPT_KEY:  list of comma delimited prefix=secret values
```

*Example: Display information on a bucket named "mybucket" on https://play.min.io.*


```
mc stat play/mybucket
Name      : mybucket/
Date      : 2018-02-06 18:06:51 PST
Size      : 0B
Type      : folder
```

*Example: Display information on an encrypted object "myobject" in "mybucket" on https://play.min.io.*


```
mc stat play/mybucket/myobject --encrypt-key "play/mybucket=32byteslongsecretkeymustbegiven1"
Name      : myobject
Date      : 2018-03-02 11:47:13 PST
Size      : 132B
ETag      : d03ba22cd78282b7aef705bf31b8cded
Type      : file
Metadata  :
  Content-Type                                   : application/octet-stream
  X-Amz-Server-Side-Encryption-Customer-Key-Md5  : 4xSRdYsabg+s2nlsHKhgnw==
  X-Amz-Server-Side-Encryption-Customer-Algorithm: AES256
```

*Example: Display information on objects contained in the bucket named "mybucket" on https://play.min.io.*

```
mc stat -r play/mybucket
Name      : mybucket/META/textfile
Date      : 2018-02-06 18:17:38 PST
Size      : 1024B
ETag      : d41d8cd98f00b204e9800998ecf8427e
Type      : file
Metadata  :
  Content-Type: application/octet-stream

Name      : mybucket/emptyfile
Date      : 2018-02-06 18:16:14 PST
Size      : 100B
ETag      : d41d8cd98f00b204e9800998ecf8427e
Type      : file
Metadata  :
  Content-Type: application/octet-stream
```
