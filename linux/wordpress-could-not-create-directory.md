# WordPress: "Could not create directory"

**Area:** linux  
**Tested on:** Ubuntu 24.04.4 LTS, nginx/1.24.0, PHP 8.3.6, WordPress 7.1.3  
**Last tested:** 2026-10-25  
**Source:** Lab incident 01, reproduced on my test server

## Symptom

- In WordPress, the customer tried to install LiteSpeed Cache from Plugins → Add New.
- WordPress showed **“Installation failed: Could not create directory.”** Retrying did not help.
- The site still works; the problem is creating a plugin folder, not loading the site.

## Quick check

```bash
# Check who owns the folder where WordPress creates plugins
ls -ld /var/www/example.com/public/wp-content/plugins
```

Healthy: The owner and group are `www-data www-data`.

Broken: The owner and group are `root root`, so PHP-FPM running as `www-data` cannot write there.

```text
drwxr-xr-x ... root root ... /var/www/example.com/public/wp-content/plugins
```

## Diagnosis

1. Check the owner of the plugins folder, where WordPress needs to create the new plugin directory.

   ```bash
   # Show the plugins folder's owner and permissions
   ls -ld /var/www/example.com/public/wp-content/plugins
   ```

   - Healthy: The folder is owned by `www-data www-data` and its owner can write to it.
   - Broken: The folder is owned by `root root`, so PHP-FPM cannot create a directory there.

2. Check whether any files or folders in `wp-content` are still owned by root.

   ```bash
   # List root-owned files and folders inside wp-content
   find /var/www/example.com/public/wp-content -user root
   ```

   - Healthy: The command shows no paths.
   - Broken: It lists paths, such as plugin folders or files, that `www-data` may not be able to change.

3. Check the ownership and permissions on the parent folders in the write path.

   ```bash
   # Show the permissions on wp-content and its plugin folders
   ls -ld /var/www/example.com/public/wp-content /var/www/example.com/public/wp-content/plugins /var/www/example.com/public/wp-content/upgrade
   ```

   - Healthy: The folders have suitable permissions, such as `755`, and are owned by `www-data`.
   - Broken: A folder is owned by root or does not let `www-data` write to it.

## Fix

```bash
# Give www-data ownership of WordPress content, including files and folders inside it
sudo chown -R www-data:www-data /var/www/example.com/public/wp-content
```

Why this is right: PHP-FPM runs as `www-data`, so it needs write access to create plugin folders. With suitable permissions such as `755` for folders and `644` for files, other users can read but cannot write. Leave `wp-config.php` at `640`.

- ⚠️ Changing only a folder with non-recursive `chown` does not change the files and folders inside it. The `akismet` folder and its contents needed recursive ownership correction.
- ❌ Don't use `chmod 777` or `chmod -R 777`; that lets every user and a hacked script write there.
- ❌ Don't apply `chmod -R 755` or `chmod -R 644` to the whole site; this can make `wp-config.php` readable by everyone.
- ❌ Don't change ownership of the whole site to root; PHP-FPM would still lack the needed write access.

## Verify

On the server:

```bash
# Confirm there are no root-owned paths left in wp-content
find /var/www/example.com/public/wp-content -user root

# Check that www-data can create a folder in plugins
sudo -u www-data mkdir /var/www/example.com/public/wp-content/plugins/.support-write-check

# Remove the test folder after the write check
sudo rmdir /var/www/example.com/public/wp-content/plugins/.support-write-check

# Check that www-data can create a folder in upgrade
sudo -u www-data mkdir /var/www/example.com/public/wp-content/upgrade/.support-write-check

# Remove the test folder after the write check
sudo rmdir /var/www/example.com/public/wp-content/upgrade/.support-write-check

# Confirm wp-config.php is still readable only by its owner and group
ls -l /var/www/example.com/public/wp-config.php
```

The first command should show no paths. The `mkdir` and `rmdir` commands should complete without errors. `wp-config.php` should show permissions `-rw-r-----` (640).

As the customer: Retry installing LiteSpeed Cache from Plugins → Add New and confirm the installation completes.

## Prevention

- Avoid running WordPress file changes as root; check ownership after any root-run file operation.
- Keep `wp-content` owned by `www-data`, with folders at `755` and files at `644`.
- Keep `wp-config.php` at `640`; do not change permissions across the whole site.

## Escalate when

- If the write check fails after the ownership fix, check the full path for restrictive parent-folder permissions and escalate to the server team.

  ```bash
  # Show permissions on each folder in the path to plugins
  namei -l /var/www/example.com/public/wp-content/plugins
  ```

- If the server is out of space, escalate to the server team.

  ```bash
  # Check free space on the filesystem containing the site
  df -h /var/www/example.com/public
  ```

- If ownership and permissions are correct but WordPress still fails, ask the server team to check the PHP-FPM user and WordPress filesystem settings.

## Diagnosis log

| Date | Minutes to root cause | Notes |
|---|---|---|
| 2026-10-25 | 19 | I found root ownership blocking PHP-FPM from creating the plugin folder. I first changed a folder without changing everything inside it; I learned to check and fix ownership recursively. |
