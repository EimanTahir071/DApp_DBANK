# How to Push Large Folders to GitHub from Git Bash

This guide explains how to push large or heavy folders from your laptop to GitHub using Git Bash, including handling large files properly.

## Table of Contents
- [Understanding GitHub File Size Limits](#understanding-github-file-size-limits)
- [Method 1: Standard Git Push (for folders < 100MB)](#method-1-standard-git-push-for-folders--100mb)
- [Method 2: Using Git LFS (for large files > 50MB)](#method-2-using-git-lfs-for-large-files--50mb)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)

## Understanding GitHub File Size Limits

GitHub has the following limitations:
- **File size warning**: Files larger than 50MB will generate a warning
- **File size limit**: Files larger than 100MB will be rejected
- **Repository size recommendation**: Keep repositories under 1GB (5GB hard limit)

## Method 1: Standard Git Push (for folders < 100MB)

If your folder contains files under 50MB each, follow these steps:

### Step 1: Open Git Bash
Navigate to your project directory:
```bash
cd /path/to/your/project
```

### Step 2: Initialize Git (if not already initialized)
```bash
git init
```

### Step 3: Add Your Folder
To add a specific folder:
```bash
git add "Your Folder Name"
```

To add all files:
```bash
git add .
```

**Note**: If your folder name has spaces, use quotes around it.

### Step 4: Commit Your Changes
```bash
git commit -m "Add large folder with project files"
```

### Step 5: Add Remote Repository
If you haven't connected to GitHub yet:
```bash
git remote add origin https://github.com/YourUsername/YourRepo.git
```

### Step 6: Push to GitHub
```bash
git push -u origin main
```

Or if your default branch is `master`:
```bash
git push -u origin master
```

### Step 7: For Subsequent Pushes
After the first push:
```bash
git push
```

## Method 2: Using Git LFS (for large files > 50MB)

Git Large File Storage (LFS) is required for files larger than 50MB.

### Step 1: Install Git LFS

**Windows:**
Download from [git-lfs.github.com](https://git-lfs.github.com/) or use:
```bash
git lfs install
```

**Mac:**
```bash
brew install git-lfs
git lfs install
```

**Linux:**
```bash
sudo apt-get install git-lfs
git lfs install
```

### Step 2: Navigate to Your Repository
```bash
cd /path/to/your/repository
```

### Step 3: Track Large Files
Track specific file types:
```bash
git lfs track "*.psd"
git lfs track "*.zip"
git lfs track "*.mp4"
```

Or track specific large files:
```bash
git lfs track "path/to/large-file.bin"
```

This creates a `.gitattributes` file that must be committed.

### Step 4: Add .gitattributes
```bash
git add .gitattributes
```

### Step 5: Add Your Large Files/Folder
```bash
git add "Your Large Folder"
# or
git add .
```

### Step 6: Commit and Push
```bash
git commit -m "Add large files using Git LFS"
git push -u origin main
```

## Troubleshooting

### Problem: "File is too large" Error

**Solution 1: Use Git LFS (recommended)**
Follow [Method 2](#method-2-using-git-lfs-for-large-files--50mb) above.

**Solution 2: Remove large files from history**

⚠️ **WARNING**: This method rewrites Git history and can cause data loss. Create a backup before proceeding!

If you already committed large files:
```bash
# Step 1: Find large files in your repository
# This command lists the 10 largest files
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  sed -n 's/^blob //p' | \
  sort --numeric-sort --key=2 | \
  tail -n 10

# Step 2: BACKUP YOUR REPOSITORY FIRST!
cp -r .git .git-backup

# Step 3: Remove the large file from history
# Replace 'path/to/large-file' with the actual path
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch path/to/large-file" \
  --prune-empty --tag-name-filter cat -- --all

# Step 4: Force push (will overwrite remote history!)
# WARNING: This affects all collaborators
git push origin --force --all
```

**Note**: Consider using `git-filter-repo` (a modern alternative) instead:
```bash
# Install git-filter-repo
pip install git-filter-repo

# Remove large file
git filter-repo --path path/to/large-file --invert-paths
```

### Problem: Push is Too Slow or Fails

**Solution 1: Increase Git buffer size**
```bash
git config --global http.postBuffer 524288000
```

**Solution 2: Push in smaller chunks**
Use shallow clones or split commits:
```bash
git push origin HEAD~10:main
git push origin HEAD~5:main
git push origin main
```

**Solution 3: Check network connection**
- Ensure stable internet connection
- Try pushing during off-peak hours
- Consider compressing files before pushing

### Problem: "Failed to push some refs"

**Solution: Pull latest changes first**
```bash
git pull origin main --rebase
git push origin main
```

### Problem: Folder with Spaces in Name

**Solution: Use quotes**
```bash
git add "Folder With Spaces"
git commit -m "Add folder with spaces in name"
```

## Best Practices

### 1. Use .gitignore
Exclude unnecessary large files:
```bash
# Create .gitignore file
echo "node_modules/" >> .gitignore
echo "*.log" >> .gitignore
echo "dist/" >> .gitignore
echo "build/" >> .gitignore
```

Common patterns to ignore:
```
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
*.exe
*.dll

# Large media files (if not needed in version control)
*.mp4
*.mov
*.avi

# IDE files
.vscode/
.idea/
*.swp

# OS files
.DS_Store
Thumbs.db
```

### 2. Compress Files When Possible
Before pushing:
```bash
# Compress folder
tar -czf archive.tar.gz "Large Folder"

# Add to LFS if needed
git lfs track "*.tar.gz"
git add archive.tar.gz
```

### 3. Split Large Commits
If you have many files:
```bash
# Add files in groups
git add folder1/
git commit -m "Add folder1"
git push

git add folder2/
git commit -m "Add folder2"
git push
```

### 4. Clean Up Before Pushing
```bash
# Check repository size
du -sh .git

# Clean up unnecessary files
git gc --aggressive --prune=now
```

### 5. Monitor Your Repository Size
```bash
# Check what's taking up space
git count-objects -vH
```

### 6. Consider Alternatives for Very Large Files
- **Git LFS**: Files 50MB - 2GB
- **Cloud Storage**: Share links in README (Google Drive, Dropbox)
- **Release Assets**: Upload binaries to GitHub Releases
- **External Hosting**: Use CDN or artifact repository

## Example: Complete Workflow

Here's a complete example for pushing a heavy folder:

```bash
# 1. Navigate to project
cd /c/Users/YourName/Projects/MyProject

# 2. Initialize Git (if needed)
git init

# 3. Check for large files
find . -type f -size +50M

# 4. If large files exist, set up Git LFS
git lfs install
git lfs track "*.zip"
git lfs track "*.psd"
git add .gitattributes

# 5. Create .gitignore
echo "node_modules/" >> .gitignore
echo "*.log" >> .gitignore
git add .gitignore

# 6. Add your folder
git add "Heavy Folder"

# 7. Commit
git commit -m "Add heavy folder with assets"

# 8. Add remote
git remote add origin https://github.com/YourUsername/YourRepo.git

# 9. Push
git push -u origin main

# 10. Verify
git status
```

## Additional Resources

- [GitHub Documentation on Large Files](https://docs.github.com/en/repositories/working-with-files/managing-large-files)
- [Git LFS Official Documentation](https://git-lfs.github.com/)
- [Git Documentation](https://git-scm.com/doc)

## Need More Help?

If you continue to experience issues:
1. Check the error message carefully
2. Verify your internet connection
3. Ensure you have write access to the repository
4. Check GitHub status page for any outages
5. Contact GitHub Support for account-specific issues
