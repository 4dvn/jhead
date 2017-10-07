# Jhead
## Jhead is a command line driven program for manipulating the non-image parts of Exif flavour JPEG files that most digital cameras produce.

### Jhead v3.00 program Features

-Extracting camera settings from Exif image files
-Able to set and/or adjust the Exif time field
-Manipulation (extract, replace, regenerate) of Exif integral thumbnails
-Transplant Exif image header from one JPEG to another
-Edit JPEG comment fields
-Automatically rotate images upright (using jpegtran) according to "orientation" tag.
-Manage running programs on large batches of Jpegs and restoring Exif header information afterwards.
-Display embedded GPS info (if present)

### General metadata options

`-te <name>	Transplant Exif header from image <name> into specified image. This option is useful if you like to edit the photos but still want the Exif header on your photos. As most photo editing programs will wipe out the Exif header, this option can be used to re-transplant them back in after editing the photos.
This feature has an interesting 'relative path' option for specifying the thumbnail name. Whenever the <name> contains the characters '&i', jhead will substitute the original filename for this name. This allows creating a 'relative name' when doing a whole batch of files. For example, the incantation:
jhead -te "originals\&i" *.jpg
would transfer the Exif header for each .jpg file in the originals directory by the same name, Both Win32 and most UNIX shells treat the '&' character in a special way, so you have to put quotes around that command line option for the '&' to even be passed to the program.
-dc	Delete comment field from the JPEG header. Note that the comment is not part of the Exif header.
-de	Delete the Exif header entirely. This leaves other sections (IPTC, XMP, comment) intact
-di	Delete IPTC section (if present). Leaves other sections intact.
-dx	Delete XMP section (if present). Leaves other sections intact.
-du	Delete any sections that jhead doesn't know about. Leaves Exif, XMP, IPTC and comment sections intact.
-purejpg	Delete all JPEG sections that aren't necessary for rendering the image. Strips any metadata that various applications may have left in the image. A combination of the -de -dc and -du options.
-mkexif	Creates minimal Exif header. Exif header contains date/time, and empty thumbnail fields only. Date/time set to file time by default. use with -rgt option if you want the Exif header to contain a thumbnail. Note that Exif header creation is very limited at this time, and no other fields can be added to the Exif header this way.
-ce	Edit the JPEG header comment field (note, this comment field is outside the Exif structure and can be part of Exif and non Exif style JPEG images).
A temporary file containing the comment is created and a text editor is launched to edit the file. The editor is specified in the EDITOR environment variable. If none is specified notepad or vi are used under Windows and UNIX respectively. After the editor exits, the data is transferred back into the image, and the temporary file deleted.
-cs <name>	Save comment section to a file
-ci <name>	Replace comment with text from file.
-cl <comment>	Replace comment with comment from command line.`

### Date / Time manipulation options

`-ft	Sets the file's system time stamp to what is stored in the Exif header.
-dsft	Sets the Exif timestamp to the file's timestamp. Requires an Exif header to pre-exist. Use -mkexif option to create one if needed.
-n[<fmt-string>]	This option causes files to be renamed and/or moved according to the Exif header "DateTimeOriginal" field. If the file is not an Exif file, or the DateTimeOriginal does not contain a valid value, the file date is used.
If the name includes '/' or '\' (under windows), this is interpreted as a new path for the file. If the new path does not exist, the path will be created.

If the [fmt-string] is omitted, the file will be renamed to MMDD-HHMMSS.
If a [fmt-string] is provided, the fmt-string will be passed to the strftime function for formatting. In addition, if the format string contains '%f', this will substitute the original name of the file (minus extension).
A sequence number may also be included by including '%i' in the format string. Leading zeros can be specified. '%03i' for example will pad the numbers to '001', '002'... this works just like printf in C, but with '%i' instead of '%d'.
If the target name already exists, the name will be appended with "a", "b", "c", etc, unless the name ends with a letter, in which case it will be appended with "0", "1", "2", etc.
This feature is especially useful if more than one digital camera was used to take pictures of an event. By renaming them to a scheme according to date, they will automatically appear in order of taking when viewed with some sort of viewer like Xnview or AcdSee, and sorted by name. Or you could use the -ft option and view the images sorted by date. Typically, one of the carera's date will be set not quite right, in which case you may have to use the -ta or -da options on those files first.

Some of the more useful arguments for strftime are:
%d  	Day of month as decimal number (01 – 31)
%H	Hour in 24-hour format (00 – 23)
%j	Day of year as decimal number (001 – 366)
%m	Month as decimal number (01 – 12)
%M	Minute as decimal number (00 – 59)
%S	Second as decimal number (00 – 59)
%U	Week of year as decimal number, with Sunday as first day of week (00 – 53)
%w	Weekday as decimal number (0 – 6; Sunday is 0)
%y	Year without century, as decimal number (00 – 99)
%Y	Year with century, as decimal number
Example:
    jhead -n%Y%m%d-%H%M%S *.jpg

This will rename files matched by *.jpg according to YYYYMMDD-HHMMSS

Note to Windows batch file users: '%' is used to deliminate macros in Windows batch files. You must use %% to get one % passed to the program. So from a batch file, you would have to write "jhead -n%%Y%%m%%d-%%H%%M%%S *.jpg"

For a full listing of strftime arguments, look up the strftime function. Note that some arguments to the strftime function (not listed here) produce strings with characters such as '/' and ':' that may not be valid as part of a filename on various systems.

-a	(Windows only option). Rename files with the same name but different extension as well. This is useful for renaming .AVI files based on Exif file in .THM, or to rename sound annotation files or raw files with jpeg files. Use together with '-n' option.
-ta<timediff>	Adjust time stored in the Exif header by h:mm forwards or backwards. Useful when having taken pictures with the wrong time set on the camera, such as after travelling across time zones, or when daylight savings time has changed. This option uses the time from the "DateTimeOriginal" (tag 0x9003) field, but sets all the time fields in the Exif header to the new value.
Examples:
Adjust time one hour forward (you would use this after you forgot to set daylight savings time on the digicam)
jhead -ta+1:00 *.jpg
Adjust time back by 23 seconds (you would use this to get the timestamps from two digicams in sync after you found that they didn't quite align)
jhead -ta-0:00:23 *.jpg
Adjust time forward by 2 days and 1 hour (49 hours)
jhead -ta+49 *.jpg
-da<date>-<date>	Works like -ta, but for specifying large date offsets, to be used when fixing dates from cameras where the date was set incorrectly, such as having date and time reset by battery removal on some cameras. This feature is best for adjusting dates on pictures taken over a large range of dates. For pictures all taken the same date, the "-ds" option is often easier to use.
Because different months and years have different numbers of days in them, a simple offset for months, days, years would lead to unexpected results at times. The time offset is thus specified as a difference between two dates, so that jhead can figure out exactly how many days the timestamp needs to be adjusted by, including leap years and daylight savings time changes. The dates are specified as yyyy:mm:dd. For sub-day adjustments, a time of day can also be included, by specifying yyyy:nn:dd/hh:mm or yyyy:mm:dd/hh:mm:ss

Examples:
Year on camera was set to 2004 instead of 2005 for pictures taken in April

jhead -da2005:03:01-2004:03:01
Default camera date is 2002:01:01, and date was reset on 2005:05:29 at 11:21 am
jhead -da2005:05:29/11:21-2002:01:01
-ts<date-time>	Sets the date and time stored in the Exif header to what is specified on the command line. This option changes all the date fields in the Exif header. Time must be specified as:
    yyyy:mm:dd-hh:mm:ss
-ds<date-time>	Sets the date stored in the Exif header to what is specified on the command line. Can be used to set date, just year and month, or just year. Date is specified as:
    yyyy:mm:dd, yyyy:mm, or yyyy
Thumbnail manipulation options

-dt	Delete thumbnails from the Exif header, but leave the interesting parts intact. This option truncates the thumbnail from the Exif header, provided that the thumbnail is the last part of the Exif header (which so far as I know is always the case). Exif headers have a built-in thumbnail, which is typically 240x160 and 10k in size. This thumbnail is used by digital cameras. Windows XP, as well as various photo viewing software may also use this thumbnail if present, but work just fine if it isn't.
-st <name>	Save the built in thumbnail from Jpegs that came from a digital camera. The thumbnail lives inside the Exif header, and is a very low-res JPEG image. Note that making any changes to a photo, except for with some programs, generally wipes out the Exif header and with it the thumbnail.
I implemented this option because I kept getting asked about having such an option. I don't consider the built in thumbnails to be all that useful - too low res. However, now you can see for yourself. I always generate my thumbnails using ImageMagick (see end of this page).
Like the '-te' option, this feature has the 'relative path' option for specifying the thumbnail name. Whenever the <name> contains the characters '&i', jhead will substitute the original filename for this name. This allows creating a 'relative name' when doing a whole batch of files. For example, the incantation:
jhead -st "thumbnails\&i" *.jpg
would create a thumbnail for each .jpg file in the thumbnails directory by the same name, (provided that the thumbnails directory exists, of course). Both Win32 and most UNIX shells treat the '&' character in a special way, so you have to put quotes around that command line option for the '&' to even be passed to the program.
If a '-' is specified for the output file, the thumbnail is sent to stdout. (UNIX build only)

-rt <name>	Replace thumbnails from the Exif header. This only works if the Exif header already contains an Exif header a thumbnail.
-rgt[size]	Regnerate Exif thumbnail. 'size' specifies maximum height or width of thumbnail. I added this option because I had a lot of images that I had rotated with various tools that don't update the Exif header. But newer image browsers such as XnView make use of the Exif thumbnail, and so the thumbnails would be different from the image itself. Note that the rotation tag also needed to be cleared (-norot option).
Typically, only images that are shot in portrait orientation are afflicted with this. You can use the -orp option to tell jhead to only operate on images that are upright.
This option relies on 'mogrify' program (from ImageMagick) to regenerate the thumbnail. Linux users often already have this tool pre-installed. Windows users have to go and download it. This option only works if the image already contains a thumbail.

Rotation tag manipulation

-autorot	Using the 'Orientation' tag of the Exif header, rotate the image so that it is upright. The program 'jpegtran' is used to perform the rotation. This program is present in most Linux distributions. For windows, you need to get a copy of it. After rotation, the orientation tag of the Exif header is set to '1' (normal orientation). The Exif thumbnail is also rotated. Other fields of the Exif header, including dimensions are untouched, but the JPEG height/width are adjusted.
This feature is especially useful with newer digital cameras, which set the orientation field in the Exif header automatically using a built in orientation sensor in the camera.
-norot	Clears the Exif header rotation tag without altering the image. You may find that your images have rotation tags in them from your camera, but you already rotated them with some lossless tool without clearing the rotation tag. Now your friendly browser rotates the images on you again because the image rotation tag still indicates the image should be rotated. Use this option to fix this problem. You may also want to regenerate the thumbnail using the -rgt option.
Output verbosity control

-h	Displays summary of command line options.
-v	Makes the program even more verbose than it already is. Like DOS programs, and unlike UNIX programs, Jhead gives feedback as to what it is doing, even when nothing goes wrong. Windows user that I am, when something doesn't give me feedback for 20 seconds, I assume its crashed.
-q	Makes the progran not spit out messages on success - more like the "Silence is golden" Unix way. its crashed.
-V	Print version info and compilation date.
-Exifmap	Show a map of the bytes in the Exif header. Useful when analyzing strange Exif headers, not of much use to non software developers.
-se	Suppress error messages relating to corrupt Exif header structure.
-c	Concise output. This causes picture info to be summarized on one line instead of several. Useful for grep-ing through images, as well as importing into spread sheets (data is space delimited with quotes as text qualifier).
File matching and selection

-model	Restricts processing of files to those whose camera model, as indicated by the Exif image information, contains the substring specified in the argument after '-model'. For example, the following command will list only images that are from an S100 camera:
jhead -model S100 *.jpg

I use this option to restrict my JPEG re-compressing to those images that came from my Cannon S100 digicam, (see the -cmd option).

-exonly	Skip all files that don't have an Exif header. This skips all files that did not come directly from the digital camera, as most photo editing software does not preserve the Exif header when saving pictures.
-cmd<command>	Executes the specified command on each Jepg file to be processed.
The Exif section of each file is read before running the command, and re-inserted after the command finishes.

This is useful for using jhead's file globbing capability for processing a whole directory tree of files.

It's also useful for restoring the exif header after operatiosn that wipe out the Exif metadata. Most programs today however will keep the Exif metadata intact, so this is less important than it used to be.

-orp -orl	Operate only on images with portrait (-orp) or landscape (-orl) aspect ratio.
Please note that this is solely based on jpeg width and height values. Some browsers may auto rotate the image on displaying it based on the Exif orientation tag, so that images shot in portrait mode are displayed as portrait. However, the image itself may not be stored in portrait orientation. The -autorot and -norot options are useful for dealing with rotation issues.
-r	The recursive feature of version 1.0 never worked to my satisfaction, and I replaced it with my recursive file globbing code in the Windows version. See below.
Bugs and Shortcomings

After jhead runs a program to rotate or resize an image, the image dimensions and thumbnail in the Exif header are not adjusted.
Modifying of Exif header data is very limited, as Jhead internally only has a read only implementation of the file system contained in the Exif header. For the most part, Exif can only modify pre-existing fixed-length fields in the header.

Most Canon digital SLR cameras fail to adjust the effective sensor resolution when shooting at less than full resolution, causing jhead to incorrectly miscalculate the sensor width and 35mm equivalent focal length. The same can result from resizing photos with Photoshop, which will manipulate parts of the Exif header. This is often reported as a bug in Jhead, but Jhead can't do much about incorrect data.

Name globbing and recursive directories under Windows

Name globbing means matching wildcard patterns to actual file names. If you know what this term means, you are probably annoyed at how programs on Windows typically handle this. The Win32 version of this program goes beyond the pattern matching that Windows provides, and allows you to specify fancy UNIX-like patterns such as:
  jhead c:\pix\199*\*\*.jpg

This program goes one step beyond beyond that in that "**" as a path component means any level of subdirectories. The invocation

  jhead c:\**\*.jpg

will find ALL Jpegs files on the c: drive, including those in the root directory. The ** only works if it is the only part of that path component. For example, the path 'c:\a**\*.jpg' will not recurse. The '**' recursive expansion is ONLY supported on the Windows version. The code is in the module 'myglob.c', if you want to reuse it (I certainly intend to reuse that code for other applications). Under UNIX, the shell's wildcard expansion is pretty decent already, and dealing with the convoluted nature of some UNIX file layouts, doing better would have been much more of a challenge.`

## Compiling:

    Windows:

    Run the batch file make.bat


    Linux / Unix / MacOS:

    type 'make'.

Jhead homeage: http://www.sentex.net/~mwandel/jhead
Last Updated: Feb 2 2015
