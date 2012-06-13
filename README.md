#!/usr/bin/python
# Filename: film.py
# This is an update/upgrade on an earlier made script to extract and cut+paste moviefiles. Welcome to v2.0!
# This update should do the following: - Extraction should be carried out by a python lib instead of an OS command for usage on all OS's
#                                      - Script speed should be faster since the files will not get extracted and cut+pasted, but rather be extracted in the correct dir right away
#  								   - Script should be way neater and easier to read
#									   - A new option should be added to make the script prompt for a new filename. (optional!)
#									   - Settings should be imported out of settings.ini
#									   - Doubt: Maybe script should recognize a dir full of .avi's aswell and have the same options mentioned before.
#
#
#

#  IMPORTANT script doesn't yet work with path names that contain '\t' or '\n'


#Import libraries:

import sys, os.path, glob, UnRAR2

# Import settings.ini for the needed settings
with open('settings.ini', 'r') as settingsfile:
	settings = settingsfile.readlines()
for set in settings:
	if set[0:6]=='source':
		source = set[17:-1]
	elif set[0:6]=='destin':
		dest = set[22:-1]
	elif set[0:6]=='change':
		change = set[12:13]
# Settings Imported!

# Check given settings:
if os.path.isdir(source)==False:
	sys.exit('Source directory is not a valid directory or doesn\'t exist!')
if os.path.isdir(dest)==False:
	sys.exit('Destination directory is not a valid directory or doesn\'t exist!')
if change != '1' and change != '0':
	sys.exit('Variable change_name can only be 1 or 0!')
change = int(change)
# Settings Checked!

# Setting needed list:
dirlist = []
filenames = []
avi_size = 524288000
# Values set!

# Define Function to list directorys
def list_directories(path, dirlist):
	for directories in os.walk(path):
		for directorie in directories:
			if os.path.isdir(str(directorie)) == True:
				dirlist.append(directorie)
	return dirlist
# Function defined / works properly

# Define function to check for (supported) filetypes
def dir_check(dir, contents):
	os.chdir(dir)
	avi = glob.glob("*.avi")
	if len(avi)!=0:
		for x in avi:
			if os.path.getsize(x)>avi_size:
				contents.append(x)
		#print 'One or more .avi files found. Script will assume they are movies and copy them.'
	return contents
# Function defined  / works properly

# Define function to check for existance of end_directory
def dest_dir_check(dir, destination):
	end_dir = os.path.join(destination, os.path.basename(dir))
	if(os.path.isdir(end_dir)):
		pass
	else:
		pass # mkdirs =)
		#print end_dir
		#os.mkdir(end_dir)
# Function defined / works properly (delete last comment =))

# Define Unrar function
def rar_check(dir, contents):
	os.chdir(dir)
	rar = glob.glob("*.rar")
	if len(rar)==0:
		rar = glob.glob("*.r00")
	if len(rar)!=0:
		rar.sort()
		#print rar[0]
		archive = UnRAR2.RarFile(rar[0])
		#print archive
		for rarinfo in archive.infoiter():
			name = rarinfo.filename
			name2 = os.path.splitext(name)
			if name2[1] ==".avi":
				#print rar[0]
				if rarinfo.size > avi_size:
					print rarinfo.filename
				#pass
	

# Call Function
list_directories(source, dirlist)
dirlist.sort()

# x = PWD.
# Loop through the list of directories to check for avi, check for rar, check for newmap existance and do shit!
for x in dirlist:
	dir_check(x, filenames)
	if filenames!=[]:
		dest_dir_check(x, dest)
		#print filenames
		
	rar_check(x, filenames)
	
	
		
		
		
	del filenames[:]



#Loop through:
	#Avi-check
	#If no avi than rar check
	#End-dir check
	#Extract
	#Cut+paste
	#Delete
	#Delete from list
#End Loop


