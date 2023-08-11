# Artifactory Gatekeeper plugin

With the Snyk plugin for Artifactory, you can scan your artifacts for open source vulnerabilities and license issues.

After the plugin is installed, it runs in the background and can do the following automatically:

* Add vulnerability and license issue counts from Snyk as properties in artifact
* Block developers from downloading packages with vulnerability and license issues according to a configured threshold

By scanning artifacts as part of your workflow and then displaying those test results directly from the Artifactory UI, the Snyk Artifactory Plugin enables you
to track and identify issues that risk your application security more quickly and to avoid using those artifacts in your projects.

### Supported package managers

* NPM
* Maven (.jar files)
* Gradle (.jar files)
* SBT (.jar files)
* Pip (PyPi)

### Prerequisites

* Snyk Enterprise Account
* Artifactory Version 7.4.3 and higher

## Installation

1. Log in to your Snyk account.
2. Click on settings > **General** to locate, copy, and save the following:
  1. Service account token or Organization API token
  2. The Organization ID for any one of your organizations
3. Go to [the Snyk Artifactory plugin repo in GitHub](https://github.com/snyk/artifactory-snyk-security-plugin) and navigate to the **Releases**.
4. From the most current release, expand the **Assets** section to download the artifactory-snyk-security-plugin-\<version>.zip archive.
5. Extract the archive. It should have the following structure: **plugins (directory)** followed by
  * snykSecurityPlugin.groovy — plugin
  * snykSecurityPlugin.properties — plugin configuration
  * lib (directory)
    * artifactory-snyk-security-core.jar - plugin library
    * snykSecurityPlugin.version - plugin version
6. Open "snykSecurityPlugin.properties" in a text editor.
  1. Set the API Token and Organization ID from the earlier steps as "snyk.api.token" and "snyk.api.organization" respectively.
  2. Configure the rest of the properties as needed or leave them as defaults. See the section [Artifact properties](#artifact-properties).
  3. For a full list of properties, [view the properties file on GitHub](https://github.com/snyk/artifactory-snyk-security-plugin/blob/master/core/src/main/groovy/io/snyk/plugins/artifactory/snykSecurityPlugin.properties).
7. Place all the files under "$JFROG\_HOME/artifactory/var/etc/artifactory/plugins".
8. Restart your Artifactory server. **Note: Refresh now** or **Reload** is not sufficient. Artifactory must be restarted.
9. Log in to your Artifactory instance and navigate to the **System Logs** to check that Snyk has been installed successfully.

![Successful installation of Snyk](docs/system-logs.png)

## How the Artifactory plugin works

Whenever a download is requested from Artifactory whether from a package manager or a URL, Snyk automatically scans the artifact for vulnerabilities and license issues.

To view details about download status, open the **System Logs**.

If a scan finds issues, based on your configuration, the download request can be blocked with a HTTP status code "403 Forbidden".

You can find the results of a scan under the artifact properties where you can decide to ignore the issues and allow downloads. To find the artifact, use the Artifactory Search Bar or navigate the tree view.

![Results of a scan](docs/properties.png)

## Artifact properties

| **Property**                                      | **Description**                                                                                                                                                        |
|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **snyk.issue.url**                                | This is the URL to the Snyk database and explanation of the vulnerability, including specific details about vulnerable versions, available upgrades, and Snyk patches. |
| **snyk.issue.vulnerabilities**                    | Regardless of the thresholds configured, this row displays vulnerability summary scan results.                                                                         |
| **snyk.issue.vulnerabilities.forceDownload**      | When "true", allows downloads for this artifact even when there are vulnerabilities.                                                                                   |
| **snyk.issue.vulnerabilities.forceDownload.info** | Use this field to provide additional information for why the forceDownload is enabled.                                                                                 |
| **snyk.issue.licenses**                           | Regardless of the thresholds configured, this row displays license summary scan results.                                                                               |
| **snyk.issue.licenses.forceDownload**             | When "true", allows downloads for this artifact even when there are license issues.                                                                                    |
| **snyk.issue.licenses.forceDownload.info**        | Use this field to provide additional information for why the forceDownload is enabled.                                                                                 |

## Troubleshooting

You can enable debug logs by modifying your `${ARTIFACTORY_HOME}/var/etc/artifactory/logback.xml`file and adding the following line:

```
<logger name="io.snyk" level="debug"/>
```

Artifactory automatically picks up the new configuration. If this does not happen, restart Artifactory.
