## Quick overview

In this tutorial, we will configure our farm account to access our Dropbox account with the `rclone` software. `rclone` is a great tool for any cloud storage system that requires login information (e.g. google drive, dropbox, microsoft onedrive). [For more info on `rclone`.](https://en.wikipedia.org/wiki/Rclone)

## Farm modules

Open a new terminal window and log into farm with `ssh`. 

Farm has certain software available to everyone. These are called modules. To keep resource use minimal, these software are available for you to load in your working session, but they need to be actively loaded. (Think of it a bit like activating a conda environment).

Let's check farm for a module called `rclone`.

```
module avail | grep rclone
```
> `module avail` loads a list of all available modules on farm.
> 
> `|` is the pipe operator that pipes one command's output into the following command as input
>
> `grep rclone` extracts any **line** that contains the string "rclone"

```
aragorn/1.2.38               bwa-mem2/2.2.1       distangsd/main        genrich/0.6        iq-tree/2.1.3     maven/3.8.4        nco/5.0.1                  pigz/2.7                rclone/1.59.1             slim/4.0.1            usearch/11.0.667
deprecated/bambam/1.4                        deprecated/EDTA/1.9.4                     deprecated/iqtree/2.1.2                     deprecated/nco/4.4.4                     deprecated/rclone/1.38                         deprecated/sratoolkit/2.7.0
deprecated/bambus/2.33                       deprecated/egglib/2.1.11                  deprecated/iraf/2.1.6                       deprecated/ncview/2.1.1                  deprecated/rclone/1.49.5                       deprecated/sratoolkit/2.8.2
deprecated/bamtools/2.2.3                    deprecated/EMAN2/2                        deprecated/irf/3.07                         deprecated/ncview/2.1.7                  deprecated/rclone/1.53.3                       deprecated/sratoolkit/2.11.0
```
> In this output, there are many `deprecated` versions of rclone available. There is also a single `rclone` module available on the first line (towards the end of the line).

Loading a module:

```
$ module load rclone

Loading rclone/1.59.1
```

You now have the `rclone` module loaded, this means you have the `rclone` software available for use in your working session.

Verify this by running:

```
$ rclone --help
```

## Configuring `rclone`

Running `rclone` requires a configuration for each remote (cloud storage company).

Configuring is facilitated by the `rclone config` command which will ask the user for input to generate a configuration file for each new remote the user would like to access.

```
$ rclone config
2024/05/01 09:49:21 NOTICE: Config file "/home/baumlerc/.config/rclone/rclone.conf" not found - using defaults
No remotes found, make a new one?
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n
```
> We would like to create a new remote with `n`


```
Enter name for new remote.
name> dropbox
```
> name your remote something easy to remember like `dropbox`
> 
> You can also see the remotes you have with `rclone listremotes` command

We are now presented with a long list of optional storages to connect with:

```
Option Storage.
Type of storage to configure.
Choose a number from below, or type in your own value.
 1 / 1Fichier
   \ (fichier)
 2 / Akamai NetStorage
   \ (netstorage)
 3 / Alias for an existing remote
   \ (alias)
 4 / Amazon Drive
   \ (amazon cloud drive)
 5 / Amazon S3 Compliant Storage Providers including AWS, Alibaba, Ceph, China Mobile, Cloudflare, ArvanCloud, Digital Ocean, Dreamhost, Huawei OBS, IBM COS, IDrive e2, Lyve Cloud, Minio, Netease, RackCorp, Scaleway, SeaweedFS, StackPath, Storj, Tencent COS and Wasabi
   \ (s3)
 6 / Backblaze B2
   \ (b2)
 7 / Better checksums for other remotes
   \ (hasher)
 8 / Box
   \ (box)
 9 / Cache a remote
   \ (cache)
10 / Citrix Sharefile
   \ (sharefile)
11 / Combine several remotes into one
   \ (combine)
12 / Compress a remote
   \ (compress)
13 / Dropbox
   \ (dropbox)
14 / Encrypt/Decrypt a remote
   \ (crypt)
15 / Enterprise File Fabric
   \ (filefabric)
16 / FTP
   \ (ftp)
17 / Google Cloud Storage (this is not Google Drive)
   \ (google cloud storage)
18 / Google Drive
   \ (drive)
19 / Google Photos
   \ (google photos)
20 / HTTP
   \ (http)
21 / Hadoop distributed file system
   \ (hdfs)
22 / HiDrive
   \ (hidrive)
23 / Hubic
   \ (hubic)
24 / In memory object storage system.
   \ (memory)
25 / Internet Archive
   \ (internetarchive)
26 / Jottacloud
   \ (jottacloud)
27 / Koofr, Digi Storage and other Koofr-compatible storage providers
   \ (koofr)
28 / Local Disk
   \ (local)
29 / Mail.ru Cloud
   \ (mailru)
30 / Mega
   \ (mega)
31 / Microsoft Azure Blob Storage
32 / Microsoft OneDrive                                             
   \ (onedrive)
33 / OpenDrive
   \ (opendrive)
34 / OpenStack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ (swift)
35 / Pcloud
   \ (pcloud)
36 / Put.io
   \ (putio)
37 / QingCloud Object Storage
   \ (qingstor)
38 / SSH/SFTP
   \ (sftp)
39 / Sia Decentralized Cloud
   \ (sia)
40 / Storj Decentralized Cloud Storage
   \ (storj)
41 / Sugarsync
   \ (sugarsync)
42 / Transparently chunk/split large files
   \ (chunker)
43 / Union merges the contents of several upstream fs
   \ (union)
44 / Uptobox
   \ (uptobox)
45 / WebDAV
   \ (webdav)
46 / Yandex Disk
   \ (yandex)
47 / Zoho
   \ (zoho)
48 / premiumize.me
   \ (premiumizeme)
49 / seafile
   \ (seafile)
Storage> dropbox
```
> Let's connect to Dropbox by typing in `dropbox`
>
> This option can be found here : `13 / Dropbox
   \ (dropbox)`

Leave the next two options blank.

```
Option client_id.
OAuth Client Id.
Leave blank normally.
Enter a value. Press Enter to leave empty.
client_id>
```
> Blank

```
Option client_secret.
OAuth Client Secret.
Leave blank normally.
Enter a value. Press Enter to leave empty.
client_secret>
```
> Blank


```
Edit advanced config?
y) Yes
n) No (default)
y/n> n
```
> Use the default config with `n`

```
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine

y) Yes (default)
n) No
y/n> n
```
> We are working in a remote machine. Select `n`.

The next step of the configuration requires use to connect rclone on farm to dropbox to create an access token that will allow us to easily transfer files.

In a ***new terminal window***, run the following three commands

```
$ ssh -L localhost:53682:localhost:53682 {USERNAME}@farm.cse.ucdavis.edu
```
> Add in your username!
> 
> This is an ssh tunnel. It is allowing us to connect our local computer's browser with the remote computer resources.

Load the module `rclone` in this new terminal that is connected to farm. (Because this is a new working session!)

```
$ module load rclone
```

Run `rclone authorize` to begin generating the token. Your browser will not automatically open. Copy the link in the output (the second line) and paste the link in your web browser.

```
$ rclone authorize 'dropbox'

2024/05/01 09:56:09 NOTICE: Config file "/home/baumlerc/.config/rclone/rclone.conf" not found - using defaults
2024/05/01 09:56:09 NOTICE: If your browser doesn't open automatically go to the following link: http://127.0.0.1:53682/auth?state=VSw2e-lR82zVuiSYD-3n0g
2024/05/01 09:56:09 NOTICE: Log in and authorize rclone for access
2024/05/01 09:56:09 NOTICE: Waiting for code...
```

Once you load the link in your browser, the following output automatically generates.

```
2024/05/01 09:57:49 NOTICE: Got code
Paste the following into your remote machine --->
{THIS_IS_YOUR_ACCESS_TOKEN!!!}
<---End paste
```
> Be sure to copy the access token!

Paste the access token from your second terminal window into the first terminal window that is still running the `rclone config` setup.

```
Option config_token.
For this to work, you will need rclone available on a machine that has
a web browser available.
For more help and alternate methods see: https://rclone.org/remote_setup/
Execute the following on the machine with the web browser (same rclone
version recommended):
        rclone authorize "dropbox"
Then paste the result.
Enter a value.
config_token> {PASTE_YOUR_ACCESS_TOKEN_HERE}
```

That was the hard part, we are almost done!

```
Configuration complete.
Options:
- type: dropbox
- token: {YOUR_PASTED_ACCESS_TOKEN}
Keep this "remote" remote?
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d> y
```
> Select `y` because it is ok

This last output is showing you what has been created. A remote named "dropbox" that is connected to "dropbox".

```
Current remotes:

Name                 Type
====                 ====
dropbox              dropbox

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q
```
> You have completed the configuration process! Select `q` to quit generating config file content.

## Verify the config

You will be able to see your remotes with:

```
$ rclone listremotes
dropbox
```

You will be able to view your configuration with:

```
$ ls ~/.config/rclone/
rclone.conf
```

You can even see what the `rclone config` command generated with:

```
$less ~/.config/rclone/rclone.conf
```
The name of your remotes will be in `[]` followed by their `type` and `token`.
```
[dropbox]
type = dropbox
token = {YOUR_ACCESS_TOKEN}
```

## Using a configured `rclone`

If files are not in your account, go to a dropbox account that has files your are interested ([link](https://www.dropbox.com/scl/fo/ykfrxcef2fnx6jow4odxv/h?rlkey=tpiu5wxf4vy1whkfl0kelvxjb&e=4&dl=0)) and select `Copy to Dropbox` to copy the files to your account.

List the files within your dropbox account with:

```
$ rclone ls 'dropbox:'

175261407 pbmc_3Mreads_S1_R1.fastq.gz
139640460 pbmc_3Mreads_S1_R2.fastq.gz
```

Copy a single file:

```
$ rclone copy 'dropbox:pbmc_3Mreads_S1_R1.fastq.gz' ~/single-cell/
```

```
$ ls ~/single-cell/

pbmc_3Mreads_S1_R1.fastq.gz
```

Copy everything in dropbox:

```
rclone copy 'dropbox:' ~/single-cell/
```

```
$ ls single-cell/
pbmc_3Mreads_S1_R1.fastq.gz  pbmc_3Mreads_S1_R2.fastq.gz
```

You can copy directories as well by creating a folder for your files:

```
$ rclone ls 'dropbox:'
175261407 serena-test/pbmc_3Mreads_S1_R1.fastq.gz
139640460 serena-test/pbmc_3Mreads_S1_R2.fastq.gz
```
> I created a folder, `serena-test/`
>
> Notice when I list the content of all dropbox, it returns the path for each file.

Copy everything in a single directory:

```
$ rclone copy 'dropbox:serena-test' ~/single-cell/
```
> This will only copy files that are not already in `~/single-cell`.

## Resources

1. [Rclone blog on dropbox](https://rclone.org/dropbox/)
2. [Rclone blog on remote machines](https://rclone.org/remote_setup/)
3. [Rclone wiki](https://en.wikipedia.org/wiki/Rclone)
4. [A university's blog on remote computer rclone usage](https://www.carc.usc.edu/user-information/user-guides/data-management/transferring-files-rclone)
