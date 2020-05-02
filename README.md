# SGCS: Modern PHP & WordPress Coding Standards w/VS Code

*This was put together for my own purposes. Feel free to use and modify however you see fit.*

These are my PHP and WordPress rulesets for [PHPCS](https://github.com/squizlabs/PHP_CodeSniffer). [WPCS](https://github.com/WordPress/WordPress-Coding-Standards) rules that conflict with the more modern [PHP PSR](https://www.php-fig.org/psr/) standards are excluded. [PHPBFC](https://github.com/squizlabs/PHP_CodeSniffer/wiki/Fixing-Errors-Automatically) is used to beautiful code automatically on save. [PHPCompatibility](https://github.com/PHPCompatibility/PHPCompatibility) is included. My purpose is modern PHP development within WordPress projects without totally abandoning the WordPress coding standards.

- **PHPCS-PHP** is used for PHP (non-WordPress) projects
- **PHPCS-WP** is used for WordPress plugins and themes (not a WordPress installation)
- **PHPCS-WP-LEGACY** is used for older WordPress plugins and themes

These instructions are geared towards [VS Code](https://code.visualstudio.com/) users on macOS. Check out [Ahmad Awais](https://gist.github.com/AhmadAwais)'s excellent [VSCode.pro](https://vscode.pro) which inspired my setup.

## Installation

First make sure you have [Composer](https://getcomposer.org/) installed (I like [Homebrew](https://brew.sh/) for that).

```sh
# Make and change into ~/bin dir.
cd ~ && mkdir -p bin && cd bin

# Clone PHPCS, WPCS, PHPCompatibility and SGCS repos.
git clone https://github.com/squizlabs/PHP_CodeSniffer.git phpcs
git clone -b master https://github.com/WordPress-Coding-Standards/WordPress-Coding-Standards.git wpcs
git clone -b master https://github.com/PHPCompatibility/PHPCompatibility.git PHPCompatibility
git clone -b master https://github.com/stevengliebe/SGCS SGCS

# Symlink phpcs and phpcbf.
sudo ln -s ~/bin/phpcs/bin/phpcs /usr/local/bin/phpcs
sudo ln -s ~/bin/phpcs/bin/phpcbf /usr/local/bin/phpcbf

# Set PHPCS configuration for SGCS, WPCS and PHPCompatibility.
phpcs --config-set installed_paths /Users/$USER/bin/SGCS,/Users/$USER/bin/wpcs,/Users/$USER/bin/PHPCompatibility

# Check it
phpcs -i

```

## Configuration

### VS Code

Install the following VS Code extensions:

- [phpcs](https://marketplace.visualstudio.com/items?itemName=ikappas.phpcs) (Ioannis Kappas)
- [phpcbf](https://marketplace.visualstudio.com/items?itemName=persoderlind.vscode-phpcbf) (Per Soderlind)
- [Save and Run](https://marketplace.visualstudio.com/items?itemName=wk-j.save-and-run) (wk-j)
- [PHP Intelephense](https://marketplace.visualstudio.com/items?itemName=bmewburn.vscode-intelephense-client) is also nice to have (not required)

Configure VS Code `settings.json`. This is what I use.

```jsonc
// FILES —————————————————————————————
"files.associations": {
    ".php_cs": "php",
    ".php_cs.dist": "php",
    "*.php": "php", // important
   // Add whatever else here
},
// PHP  —————————————————————————————
"php.suggest.basic": false,
"intelephense.format.enable": true,
"[php]": {
    "editor.formatOnSave": false,
    "editor.formatOnPaste": false
},
// PHPCS —————————————————————————————
"phpcs.executablePath": "/usr/local/bin/phpcs",
"phpcs.standard": "SGCS-PHP",
"phpcs.showSources": true,
// PHPCBF —————————————————————————————
"phpcbf.onsave": true,
"phpcbf.standard": "SGCS-PHP",
"phpcbf.executablePath": "/usr/local/bin/phpcbf",
"saveAndRun": { // Extension: Save and Run
    "commands": [
        { // Beautify PHP code on save w/PHPBCF
            "match": "\\.php$",
            "cmd": "phpcbf -q --standard='SGCS-PHP' '${file}'",
            "silent": true
        }
    ]
}
```

### phpcs.xml Examples

Here's an example `phpcs.xml` file for a PHP (non-WordPress) project. It uses the **SGCS-PHP** ruleset.

```xml
<?xml version="1.0"?>
<ruleset>

    <!-- SGCS ruleset for PHP projects -->
    <rule ref="SGCS-PHP"/>

</ruleset>

```

Below uses **SGCS-WP** for a WordPress project and also checks code for compatibility with PHP 7.1+. Match this to a plugin's "Requires PHP" version.

```xml
<?xml version="1.0"?>
<ruleset>

    <!-- SGCS ruleset for WordPress plugins/themes -->
    <rule ref="SGCS-WP"/>

    <!-- PHPCompatibility to check 7.1 and newer -->
    <config name="testVersion" value="7.1-"/>

</ruleset>
```

## Updating

### Command Line

Just pull the Git repos to update the rules.

```sh
cd ~/bin/phpcs && git pull
cd ~/bin/wpcs && git pull
cd ~/bin/PHPCompatibility && git pull
cd ~/bin/SGCS && git pull
```

### VS Code Buttons

It's handy to make a button in VS Code with `buttons.jsonc` ([vscode-buttons](https://marketplace.visualstudio.com/items?itemName=stuartthomson.vscode-buttons) extension) to update PHPCS and the rulesets with one click.

```json
{
    "buttons": [
        {
            "name": "Update PHPCS",
            "script": "cd ~/bin/phpcs && git pull; cd ~/bin/wpcs && git pull; cd ~/bin/PHPCompatibility && git pull; cd ~/bin/SGCS && git pull",
            "description": "Pull latest Git repos"
        }
    ]
}
```

License: MIT
