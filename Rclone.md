# Rclone

## Install

```
You could try installing rclone in your own space on Anvil (such as your home directory or the project directory of your group). The following steps worked for me:

$ cd
$ wget https://downloads.rclone.org/rclone-current-linux-amd64.zip
$ unzip rclone-current-linux-amd64.zip
$ cd rclone-v1.66.0-linux-amd64
$ ./rclone version
```

## Usage

```cmd
# check remote connections and creat new connections
rclone config

# check files and folers
rclone ls <storage>:<folder_name> # check files
rclone lsd <storage>:<folder_name> # check folders

# upload to cloud storage
rclone copy <source_folder> <storage>:<folder_name>

# download from cloud storage
rclone copy <storage>:<folder_name> <source_folder> 
	-v (verbose): show more details
```

