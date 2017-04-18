wp-cli/core-command
===================

Manage WordPress core files.

[![Build Status](https://travis-ci.org/wp-cli/core-command.svg?branch=master)](https://travis-ci.org/wp-cli/core-command)

Quick links: [Using](#using) | [Installing](#installing) | [Contributing](#contributing)

## Using

This package implements the following commands:

### wp core check-update

Check for WordPress updates via Version Check API.

~~~
wp core check-update [--minor] [--major] [--field=<field>] [--fields=<fields>] [--format=<format>]
~~~

Lists the most recent versions when there are updates available,
or success message when up to date.

**OPTIONS**

	[--minor]
		Compare only the first two parts of the version number.

	[--major]
		Compare only the first part of the version number.

	[--field=<field>]
		Prints the value of a single field for each update.

	[--fields=<fields>]
		Limit the output to specific object fields. Defaults to version,update_type,package_url.

	[--format=<format>]
		Render output in a particular format.
		---
		default: table
		options:
		  - table
		  - csv
		  - count
		  - json
		  - yaml
		---

**EXAMPLES**

    $ wp core check-update
    +---------+-------------+-------------------------------------------------------------+
    | version | update_type | package_url                                                 |
    +---------+-------------+-------------------------------------------------------------+
    | 4.5.2   | major       | https://downloads.wordpress.org/release/wordpress-4.5.2.zip |
    +---------+-------------+-------------------------------------------------------------+



### wp core download

Download core WordPress files.

~~~
wp core download [--path=<path>] [--locale=<locale>] [--version=<version>] [--force]
~~~

Downloads and extracts WordPress core files to the specified path. Uses
an archive file stored in cache if WordPress has been previously
downloaded.

**OPTIONS**

	[--path=<path>]
		Specify the path in which to install WordPress.

	[--locale=<locale>]
		Select which language you want to download.

	[--version=<version>]
		Select which version you want to download. Accepts a version number, 'latest' or 'nightly'

	[--force]
		Overwrites existing files, if present.

**EXAMPLES**

    $ wp core download --locale=nl_NL
    Downloading WordPress 4.5.2 (nl_NL)...
    md5 hash verified: c5366d05b521831dd0b29dfc386e56a5
    Success: WordPress downloaded.



### wp core install

Runs the standard WordPress installation process.

~~~
wp core install --url=<url> --title=<site-title> --admin_user=<username> [--admin_password=<password>] --admin_email=<email> [--skip-email]
~~~

Creates the WordPress tables in the database using the URL, title, and
default admin user details provided. Performs the famous 5 minute install
in seconds or less.

Note: if you've installed WordPress in a subdirectory, then you'll need
to `wp option update siteurl` after `wp core install`. For instance, if
WordPress is installed in the `/wp` directory and your domain is wp.dev,
then you'll need to run `wp option update siteurl http://wp.dev/wp` for
your WordPress install to function properly.

Note: When using custom user tables (e.g. `CUSTOM_USER_TABLE`), the admin
email and password are ignored if the user_login already exists. If the
user_login doesn't exist, a new user will be created.

**OPTIONS**

	--url=<url>
		The address of the new site.

	--title=<site-title>
		The title of the new site.

	--admin_user=<username>
		The name of the admin user.

	[--admin_password=<password>]
		The password for the admin user. Defaults to randomly generated string.

	--admin_email=<email>
		The email address for the admin user.

	[--skip-email]
		Don't send an email notification to the new admin user.

**EXAMPLES**

    # Install WordPress in 5 seconds
    $ wp core install --url=example.com --title=Example --admin_user=supervisor --admin_password=strongpassword --admin_email=info@example.com
    Success: WordPress installed successfully.

    # Install WordPress without disclosing admin_password to bash history
    $ wp core install --url=example.com --title=Example --admin_user=supervisor --admin_email=info@example.com --prompt=admin_password < admin_password.txt



### wp core is-installed

Check if WordPress is installed.

~~~
wp core is-installed [--network]
~~~

Determines whether WordPress is installed by checking if the standard
database tables are installed. Doesn't produce output; uses exit codes
to communicate whether WordPress is installed.

	[--network]
		Check if this is a multisite install.

**EXAMPLES**

    # Check whether WordPress is installed; exit status 0 if installed, otherwise 1
    $ wp core is-installed
    $ echo $?
    1

    # Bash script for checking whether WordPress is installed or not
    if ! $(wp core is-installed); then
        wp core install
    fi



### wp core multisite-convert

Transform a single-site install into a WordPress multisite install.

~~~
wp core multisite-convert [--title=<network-title>] [--base=<url-path>] [--subdomains]
~~~

Creates the multisite database tables, and adds the multisite constants
to wp-config.php.

For those using WordPress with Apache, remember to update the `.htaccess`
file with the appropriate multisite rewrite rules.

**OPTIONS**

	[--title=<network-title>]
		The title of the new network.

	[--base=<url-path>]
		Base path after the domain name that each site url will start with.
		---
		default: /
		---

	[--subdomains]
		If passed, the network will use subdomains, instead of subdirectories. Doesn't work with 'localhost'.

**EXAMPLES**

    $ wp core multisite-convert
    Set up multisite database tables.
    Added multisite constants to wp-config.php.
    Success: Network installed. Don't forget to set up rewrite rules.



### wp core multisite-install

Install WordPress multisite from scratch.

~~~
wp core multisite-install [--url=<url>] [--base=<url-path>] [--subdomains] --title=<site-title> --admin_user=<username> [--admin_password=<password>] --admin_email=<email> [--skip-email]
~~~

Creates the WordPress tables in the database using the URL, title, and
default admin user details provided. Then, creates the multisite tables
in the database and adds multisite constants to the wp-config.php.

**OPTIONS**

	[--url=<url>]
		The address of the new site.

	[--base=<url-path>]
		Base path after the domain name that each site url in the network will start with.
		---
		default: /
		---

	[--subdomains]
		If passed, the network will use subdomains, instead of subdirectories. Doesn't work with 'localhost'.

	--title=<site-title>
		The title of the new site.

	--admin_user=<username>
		The name of the admin user.
		---
		default: admin
		---

	[--admin_password=<password>]
		The password for the admin user. Defaults to randomly generated string.

	--admin_email=<email>
		The email address for the admin user.

	[--skip-email]
		Don't send an email notification to the new admin user.

**EXAMPLES**

    $ wp core multisite-install --title="Welcome to the WordPress" \
    > --admin_user="admin" --admin_password="password" \
    > --admin_email="user@example.com"
    Single site database tables already present.
    Set up multisite database tables.
    Added multisite constants to wp-config.php.
    Success: Network installed. Don't forget to set up rewrite rules.



### wp core update

Update WordPress to a newer version.

~~~
wp core update [<zip>] [--minor] [--version=<version>] [--force] [--locale=<locale>]
~~~

Defaults to updating WordPress to the latest version.

If you see "Error: Another update is currently in progress.", you may
need to run `wp option delete core_updater.lock` after verifying another
update isn't actually running.

**OPTIONS**

	[<zip>]
		Path to zip file to use, instead of downloading from wordpress.org.

	[--minor]
		Only perform updates for minor releases (e.g. update from WP 4.3 to 4.3.3 instead of 4.4.2).

	[--version=<version>]
		Update to a specific version, instead of to the latest version. Alternatively accepts 'nightly'.

	[--force]
		Update even when installed WP version is greater than the requested version.

	[--locale=<locale>]
		Select which language you want to download.

**EXAMPLES**

    # Update WordPress
    $ wp core update
    Updating to version 4.5.2 (en_US)...
    Downloading update from https://downloads.wordpress.org/release/wordpress-4.5.2-no-content.zip...
    Unpacking the update...
    Cleaning up files...
    No files found that need cleaned up
    Success: WordPress updated successfully.

    # Update WordPress to latest version of 3.8 release
    $ wp core update --version=3.8 ../latest.zip
    Updating to version 3.8 ()...
    Unpacking the update...
    Cleaning up files...
    File removed: wp-admin/js/tags-box.js
    ...
    File removed: wp-admin/js/updates.min.
    377 files cleaned up
    Success: WordPress updated successfully.

    # Update WordPress to 3.1 forcefully
    $ wp core update --version=3.1 --force
    Updating to version 3.1 (en_US)...
    Downloading update from https://wordpress.org/wordpress-3.1.zip...
    Unpacking the update...
    Warning: Failed to fetch checksums. Please cleanup files manually.
    Success: WordPress updated successfully.



### wp core update-db

Run the WordPress database update procedure.

~~~
wp core update-db [--network] [--dry-run]
~~~

	[--network]
		Update databases for all sites on a network

	[--dry-run]
		Compare database versions without performing the update.

**EXAMPLES**

    # Update the WordPress database
    $ wp core update-db
    Success: WordPress database upgraded successfully from db version 36686 to 35700.

    # Update databases for all sites on a network
    $ wp core update-db --network
    WordPress database upgraded successfully from db version 35700 to 29630 on example.com/
    Success: WordPress database upgraded on 123/123 sites



### wp core version

Display the WordPress version.

~~~
wp core version [--extra]
~~~

**OPTIONS**

	[--extra]
		Show extended version information.

**EXAMPLES**

    # Display the WordPress version
    $ wp core version
    4.5.2

    # Display WordPress version along with other information
    $ wp core version --extra
    WordPress version: 4.5.2
    Database revision: 36686
    TinyMCE version:   4.310 (4310-20160418)
    Package language:  en_US

## Installing

Installing this package requires WP-CLI v0.23.0 or greater. Update to the latest stable release with `wp cli update`.

Once you've done so, you can install this package with `wp package install wp-cli/core-command`.

## Contributing

We appreciate you taking the initiative to contribute to this project.

Contributing isn’t limited to just code. We encourage you to contribute in the way that best fits your abilities, by writing tutorials, giving a demo at your local meetup, helping other users with their support questions, or revising our documentation.

### Reporting a bug

Think you’ve found a bug? We’d love for you to help us get it fixed.

Before you create a new issue, you should [search existing issues](https://github.com/wp-cli/core-command/issues?q=label%3Abug%20) to see if there’s an existing resolution to it, or if it’s already been fixed in a newer version.

Once you’ve done a bit of searching and discovered there isn’t an open or fixed issue for your bug, please [create a new issue](https://github.com/wp-cli/core-command/issues/new) with the following:

1. What you were doing (e.g. "When I run `wp post list`").
2. What you saw (e.g. "I see a fatal about a class being undefined.").
3. What you expected to see (e.g. "I expected to see the list of posts.")

Include as much detail as you can, and clear steps to reproduce if possible.

### Creating a pull request

Want to contribute a new feature? Please first [open a new issue](https://github.com/wp-cli/core-command/issues/new) to discuss whether the feature is a good fit for the project.

Once you've decided to commit the time to seeing your pull request through, please follow our guidelines for creating a pull request to make sure it's a pleasant experience:

1. Create a feature branch for each contribution.
2. Submit your pull request early for feedback.
3. Include functional tests with your changes. [Read the WP-CLI documentation](https://wp-cli.org/docs/pull-requests/#functional-tests) for an introduction.
4. Follow the [WordPress Coding Standards](http://make.wordpress.org/core/handbook/coding-standards/).


*This README.md is generated dynamically from the project's codebase using `wp scaffold package-readme` ([doc](https://github.com/wp-cli/scaffold-package-command#wp-scaffold-package-readme)). To suggest changes, please submit a pull request against the corresponding part of the codebase.*
