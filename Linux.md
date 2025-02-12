# Linux

## Basics of Shell

````shell
pwd # 显示当前目录
du -sh # 查看自己使用了多少存储
空格 # 翻页；
回车 # 快速翻页
ctrl+c # 退出 
cd # 在当前路径的基础上切换文件路径
more fileName # 快速检查某一个文件

mkdir # 创建目录
# e.g. mkdir runoob2/test

less # 使用 less 可以随意浏览文件，而 more 仅能向前移动，却不能向后移动，而且 less 在查看之前不会加载整个文件

ls # 用于显示指定工作目录下的内容（列出目前工作目录所含的文件及子目录)
# ls -l|grep “^-”| wc -l # 查看当前目录下的文件数量，不包含子目录中的文件
# |: pipe operator, to feed a file content as input to another command (cm1 file | another command)
ls | head -n 1 # display the first file

mv # 为文件或目录改名或将文件由一个目录移入另一个目录中
# mv /home/ffxhd/testThinkPHP5/tp5/* /home/ffxhd/testThinkPHP5
mv `ls | head -n 1` .. # mv the first file to the upper level
mv typeI_img/`ls typeI_img | head -n 1` ../Michigan/typeI_img

rm # 用于删除一个文件或者目录
# rm -rf /var/log/httpd/access
# rm -f /var/log/httpd/access.log

sed OPTIONS... [SCRIPT] [INPUTFILE...]
# a stream editor and it can perform lots of functions on file like searching, find and replace, insertion or deletion.By using SED you can edit files even without opening them, which is much quicker way to find and replace something in file, than first opening that file in VI Editor and then changing it. 
	 # substituing string
	 device="cuda"
	 sed -i 's/device = .*/device = '\"$device\"'/g' config.py
	 # replce device=.* with device = '\"$device\"'
	 # -i: replaces the text in the original file itself rather than displaying it on the terminal; s: substituion command; /: dilmiters; g: replacing all the occurrence of the pattern in a line

#t data usage 
# locate one path first
du -sh
-s: summary
-h: humman read (add unit after number)

#t zip and unzip
zip squash.zip file1 file2 file3
zip -r squash.zip dir1
unzip squash.zip
unzip sample_fixed.zip

# search file
find /home/username/ -name "*.err"
````

## Copy, Move & Remove

````shell
cp # 复制文件到新路径
# cp -r /data/vali_target/. /data/data_au/vali_target # 将该目录下的所有文件复制到新的目录中
# cp a b c d # 将多个文件复制到新目录中
# echo /home/dir1 /home/dir2 /home/dir3 | xargs -n 1 cp -v /dir1 /dir2 # 将多个文件复制到多个文件夹。
# -n 1，表示每一命令行只能有一个参数，并传给cp命令；cp，表示复制；-v，表示将复制过程详细罗列出来，这样就可以清楚看到文件是如何复制到不同的文件夹内的

{cp/mv/rm} `find {Dir} -type f -newermt '2023-01-01 00:00:00' ! -newermt '2023-02-01 00:00:00'` FOLDER # copies all the files in the directory that were created after 2023-01-01 and before 2023-02-01 to the FOLDER
	# "-type f": only find regular files (not directories or other types of files)
	
find . -mtime -90 -exec cp {} Folder \; # copy the last three months' files from one directory to another directory

# cp all files with suffix 'jpg' in main dir recursively to target dir
find main_dir -type f -name '*.jpg' -exec cp {} target_dir \;
````



## Count the number of files

```shell
#t count number
find {Dir} -type f -name '*.tif' -newermt '2023-01-01 00:00:00' | wc -l
# "| wc -l": count the number of lines in the output of the previous command. 
	# "|": a pipe which is used to redirect the output of one command to another 	command. 
	# "wc": count the number of lines, words, and characters in a file or a stream of 		text. 
	# "-l": tells the "wc" command to count only the number of lines in the input.

#t other commands
ls -dq *.TIF* | wc -l  # count all files with specific name in current folder
ls -l|grep ".tif"| wc -l # suffix
ls -l|grep "^-"| wc -l # count the number of files in current folder (cd), not include 
```

## Create file 

```bash
touch filename.txt
echo "Hello, world!" > filename.txt # create and add content to the file
```
