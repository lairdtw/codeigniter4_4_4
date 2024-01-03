# codeigniter4_4_4

Hey,

I have previously developed websites using CodeIgniter 3, and deploying them on IIS 10 presented no issues.
The auto-routing feature in CodeIgniter 3 was exceptionally user-friendly!

Due to the discontinuation of PHP 7 support, I am currently evaluating the use of the latest CodeIgniter 4 framework.


Steps taken:
1. Download CodeIgniter 4.4.4 from https://github.com/codeigniter4/framework/releases
2. Create an "ci" site in IIS with the following settings:
  Host: localhost:8085
  Path: APPPATH / public
  Additionally, set up URL Rewrite based on the transformation from public/.htaccess to web.config (refer to: https://forum.codeigniter.com/showthread.php?tid=65798&pid=387289#pid387289)


My web.config:
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <directoryBrowse enabled="false" />
        <rewrite>
            <rules>
                <rule name="rule1" stopProcessing="true">
                    <match url="^" ignoreCase="false" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
                        <add input="{URL}" pattern="(.+)/$" ignoreCase="false" />
                    </conditions>
                    <action type="Redirect" url="{C:1}" redirectType="Permanent" />
                </rule>
                <rule name="rule2" stopProcessing="true">
                    <match url="^" ignoreCase="false" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{HTTPS}" pattern="^on$" ignoreCase="false" negate="true" />
                        <add input="{HTTP_HOST}" pattern="^www\.(.+)$" />
                    </conditions>
                    <action type="Redirect" url="http://{C:1}{URL}" redirectType="Permanent" />
                </rule>
                <rule name="rule3" stopProcessing="true">
                    <match url="^([\s\S]*)$" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
                    </conditions>
                    <action type="Rewrite" url="index.php?/{R:1}" appendQueryString="true" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration>



Currently facing issues:
All URLs only display the default welcome page. (Only the default page loads.)

[Correct URLs]
- http://localhost:8085
- http://localhost:8085/index
- http://localhost:8085/index.php
- http://localhost:8085/index.php/home
- http://localhost:8085/index.php/home/index
- http://localhost:8085/home/index

[Incorrect URLs]
- http://localhost:8085/index2
- http://localhost:8085/index.php/home/index2
- http://localhost:8085/home/index2


It seems that all URLs are redirecting to Home.php -> index()?
Very strange.



I have attempted the following solutions:

1. [Official troubleshooting]
app/Config/App.php
public string $indexPage = 'index.php?';
=>not working

2. app/Config/Routing.php
public bool $autoRoute = true;
=>not working

3. app/Config/Feature.php
public bool $autoRoutesImproved = true;
=>not working

4. Finally, I use Apache server to host this website instead.
=> It works!


I attempted the solutions provided in the official troubleshooting documentation (refer to: https://codeigniter4.github.io/userguide/installation/troubleshooting.html#only-the-default-page-loads) and tried modifying the auto-routing configurations. Perhaps there are technical issues with auto-routing in CodeIgniter 4.4.x? (Refer to: https://forum.codeigniter.com/printthread.php?tid=88353)

However, none of the above methods proved effective, leaving me perplexed.


In conclusion, it appears that CodeIgniter 4.4.4 might not yet fully support IIS, or there may be adjustments needed in my environment or project to ensure compatibility with IIS 10.


Please help.



My Environment:
Codeigniter 4.4.4
PHP  8.1.27
IIS 10.0.22621.2428


