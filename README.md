# virtual_host_sh
script for creating virtual host when using apache 2.4 +

#Purpose being, you can tie this into a script from cockpit module and not have to be in terminal
#create the following script and place on system, currently it's pretty basic, but for most needs like just building your virtual host file this can get you going, 
# modify this though, as this script i've only ever used for developing systems 
#

#modify paths of where files are going to be written to 
```
#!/bin/bash

# Define the Apache document root
docRoot="/var/www/sites"

# Prompt for the domain name
read -p "Enter the ServerName (primary domain name, e.g., domain.com): " serverName

# Prompt for server aliases, if any
read -p "Enter ServerAlias (if any, separated by space, e.g., www.domain.com sub.domain.com): " serverAlias

# Create a directory for the domain if it doesn't exist
mkdir -p "$docRoot/$serverName"

# Ensure the target directory exists
vHostDir="/etc/httpd/websites"
mkdir -p $vHostDir

# Set file paths
vHostFilePath="$vHostDir/${serverName}.conf"
errorLogPath="/var/log/httpd/${serverName}-error.log"
accessLogPath="/var/log/httpd/${serverName}-access.log"

# Start constructing the virtual host file
{
echo "<VirtualHost *:80>"
echo "  ServerName $serverName"
# Check if serverAlias is not empty and include ServerAlias directive if true
if [[ ! -z "$serverAlias" ]]; then
    echo "  ServerAlias $serverAlias"
fi
cat << EOF
  DocumentRoot $docRoot/$serverName

  <Directory "$docRoot/$serverName">
    AllowOverride All
    Require all granted
  </Directory>

  DirectoryIndex index.html index.php index.cfm index.js 

  ErrorLog $errorLogPath
  CustomLog $accessLogPath combined
</VirtualHost>
EOF
} > $vHostFilePath

echo "Virtual host file for $serverName created at $vHostFilePath."

# Reminder to restart Apache
echo "Remember to restart your Apache server for changes to take effect."
````
