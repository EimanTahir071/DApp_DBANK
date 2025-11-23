# Quick Reference: Pushing Folders to GitHub

**For detailed instructions, see [PUSHING_LARGE_FOLDERS.md](PUSHING_LARGE_FOLDERS.md)**

## Quick Commands

### Basic Push (Small Files)
```bash
cd /path/to/your/project
git add .
git commit -m "Your message"
git push origin main
```

### First Time Setup
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/username/repo.git
git push -u origin main
```

### With Git LFS (Large Files > 50MB)
```bash
git lfs install
git lfs track "*.zip"
git lfs track "*.psd"
git add .gitattributes
git add .
git commit -m "Add large files"
git push origin main
```

### Folder with Spaces
```bash
git add "Folder Name With Spaces"
git commit -m "Add folder"
git push
```

## Common Issues

| Problem | Quick Fix |
|---------|-----------|
| File too large (>100MB) | Use Git LFS: `git lfs track "filename"` |
| Push too slow | Increase buffer: `git config --global http.postBuffer 524288000` |
| Failed to push refs | Pull first: `git pull origin main --rebase` |
| Permission denied | Check credentials and repository access |

## File Size Limits

- ⚠️ **50MB**: Warning (use Git LFS)
- ❌ **100MB**: Blocked (must use Git LFS)
- 📦 **Repository**: Keep under 1GB recommended

## Need More Help?

See the complete guide: [PUSHING_LARGE_FOLDERS.md](PUSHING_LARGE_FOLDERS.md)
