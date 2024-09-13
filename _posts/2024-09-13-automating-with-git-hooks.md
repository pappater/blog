---
layout: post
title: "Automating README.md Updates with Git Hooks and Bash Script"
date: 2024-09-13 20:03:00 +0530
categories: jekyll update
---

In this article, we’ll explore how to automate the process of updating your `README.md` file whenever a new folder is added to your repository. We’ll use a Bash script combined with Git hooks to ensure that the `README.md` file always reflects the current state of your repository.

## Overview

We will cover:

1. Creating and using a Bash script to update the `README.md` file.
2. Setting up a Git hook to automatically execute the script after each commit.
3. Navigating and editing files using the Nano editor.

## 1. Creating the Bash Script

First, we need a Bash script that will update the `README.md` file with any new folders added to your repository. Here’s a sample script named `update_readme.sh`.

### Script Content

```bash
#!/bin/bash

# Variables
README="README.md"
FOLDERS=($(ls -d */)) # Get all folders
TEMP_README="README_temp.md"
EXISTING_TOPICS_SECTION=false
NEW_TOPICS_SECTION=false

# Create a temporary README to store the updated content
cp $README $TEMP_README

# Loop through each folder to check if they already exist in the README
for folder in "${FOLDERS[@]}"
do
    # Remove trailing slash from folder name
    folder_name="${folder%/}"

    # Check if the folder contains a README.md and is not already in the main README.md
    if [ -f "$folder/README.md" ] && ! grep -q "\[$folder_name\]" $README; then
        # If new topic section hasn't been added, add a comment for new topics
        if [ "$NEW_TOPICS_SECTION" = false ]; then
            echo "" >> $TEMP_README
            echo "### Newly Added Topics" >> $TEMP_README
            NEW_TOPICS_SECTION=true
        fi
        # Append the folder link to the temporary README
        echo "### [$folder_name]($folder_name/README.md)" >> $TEMP_README
    fi
done

# Overwrite the original README with the updated content
mv $TEMP_README $README

echo "Main README.md updated with new folders (if any)."
```

### How to Save and Execute the Script

1. **Save the Script**:

   - Open your terminal and navigate to the root directory of your repository.
   - Create and open the script file:
     ```bash
     nano update_readme.sh
     ```
   - Paste the script content into Nano.
   - Save the file by pressing `CTRL + O`, then press `Enter` to confirm.
   - Exit Nano by pressing `CTRL + X`.

2. **Make the Script Executable**:

   ```bash
   chmod +x update_readme.sh
   ```

3. **Run the Script Manually**:
   ```bash
   ./update_readme.sh
   ```

## 2. Setting Up Git Hooks

Git hooks are scripts that Git executes before or after events such as commits or pushes. We'll use a `post-commit` hook to automatically run our script after each commit.

### Navigating to the `.git/hooks` Directory

1. **Open Terminal**:

   - Navigate to the root directory of your Git repository.

2. **Access the `.git/hooks` Directory**:

   ```bash
   cd .git/hooks
   ```

3. **Create or Edit the `post-commit` Hook**:

   - Create a new file called `post-commit`:
     ```bash
     nano post-commit
     ```
   - Add the following content to run your script:
     ```bash
     #!/bin/bash
     ./update_readme.sh
     git add README.md
     git commit -m "Auto-update README with new folders"
     ```
   - Save and exit Nano (`CTRL + O`, `Enter`, `CTRL + X`).

4. **Make the Hook Executable**:
   ```bash
   chmod +x post-commit
   ```

## 3. Working with Nano Editor

**Nano** is a simple and user-friendly text editor for the command line. Here’s a quick guide on how to use it:

- **Open a File**:

  ```bash
  nano filename
  ```

- **Save Changes**:

  - Press `CTRL + O` (Write Out).
  - Press `Enter` to confirm the file name.

- **Exit Nano**:
  - Press `CTRL + X`.

## Conclusion

By following these steps, you can automate the updating of your `README.md` file, ensuring it always reflects the current state of your repository. This approach saves time and keeps your documentation up to date with minimal effort.
