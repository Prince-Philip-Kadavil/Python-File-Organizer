# Python-File-Organizer
Organizing files can help you keep your directories clean and make it easier to find the files you need. By organizing your files into subdirectories based on their extension, you can quickly find the files you need and keep your directories more manageable. Organizing files can also help you save space on your hard drive and keep your computer running smoothly.

Overall, organizing files is a good practice that can help you stay organized and save time.

## The Python Script

The script we are discussing is written in Python and uses the `os`, `posixpath`, `tarfile`, `datetime` and `boto3` libraries. This is a Python script that organizes files based on their extension and copies them to a new directory. Here's how it works:

1. The script first walks through the source directory and collects file paths based on their extension.
2. It then creates a new folder with the extension name in the destination directory.
3. Finally, it copies the files to their respective folders.

The script also handles file collisions by comparing file content. If two files have the same content, it replaces the existing file. If they have different content, it appends a number to the filename to avoid overwriting.


Let's break down the script to understand it better.


## Collecting file paths based on extension
```python
mydict = {}

source_directory = "/home/ec2-user/python_daily/backup/"

for items in os.walk(source_directory):
    curDir, subDir, subfiles = items
    for file in subfiles:
        abspath = posixpath.join(curDir, file)
        extension = posixpath.splitext(abspath)[-1].lstrip('.')
        mydict.setdefault(extension, []).append(abspath)
```

This section walks through the source directory and collects file paths based on their extension. It uses the `os.walk()` function to recursively walk through all subdirectories of the source directory. For each file it finds, it gets its absolute path and extension using `posixpath.splitext()`. It then adds the file path to a dictionary with the extension as the key.

## Creating new folder with extension name
```python
for extension in mydict:
    folder_path = os.path.join(destination_directory, extension)
    if not os.path.exists(folder_path):
        os.mkdir(folder_path)
```

This section creates a new folder with the extension name in the destination directory for each key in the dictionary. It uses `os.path.join()` to create the folder path and `os.mkdir()` to create the folder if it doesn't already exist.

## Copying files
```python
for extension in mydict:
    for file in mydict[extension]:
        new_file_path = os.path.join(folder_path, os.path.basename(file))
        # Handle file collisions by comparing file content
        if os.path.exists(new_file_path):
            if get_file_checksum(file) == get_file_checksum(new_file_path):
                # The files have the same content, so we can replace the existing file
                os.remove(new_file_path)
            else:
                # Append a number to the filename to avoid overwriting
                counter = 1
                while os.path.exists(new_file_path):
                    filename, ext = os.path.splitext(os.path.basename(file))
                    new_file_path = os.path.join(folder_path, f"{filename}_{counter}{ext}")
                    counter += 1
        shutil.move(file, new_file_path)
```

This section copies each file to its respective folder. It loops through each key in the dictionary and then loops through each file path associated with that key. It uses `os.path.basename()` to get just the filename from the file path and then joins it with the folder path using `os.path.join()`. If there is already a file with that name in the destination folder (i.e., a collision), it compares their contents using `get_file_checksum()`. If they have the same content, it replaces the existing file using `os.remove()`. If they have different content, it appends a number to the filename and tries again until it finds an available filename.

## Conclusion

Overall, this code is a great way to organize files based on their extension and keep your directories clean. It’s also a good example of how Python can be used to automate tasks and save time.
